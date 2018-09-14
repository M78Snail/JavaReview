```
package org.dataguru.station;

import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map.Entry;
import java.util.TreeMap;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.Text;



import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.FileSplit;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

public class BaseStationDataPreprocess extends Configured implements Tool {

	/**
	 * 计数器 用于计数各种异常数据
	 */
	enum Counter {
		TIMESKIP, // 时间格式有误
		OUTOFTIMESKIP, // 时间不在参数指定的时间段内
		LINESKIP, // 源文件行有误
		USERSKIP // 某个用户某个时间段被整个放弃
	}

	public static class Map extends Mapper<LongWritable, Text, Text, Text> {
		String date;
		String[] timepoint;
		boolean dataSource;

		@Override
		protected void setup(Mapper<LongWritable, Text, Text, Text>.Context context)
				throws IOException, InterruptedException {

			this.date = context.getConfiguration().get("date"); // 读取日期
			this.timepoint = context.getConfiguration().get("timepoint").split("-"); // 读取时间分割点

			// 提取文件名
			FileSplit fs = (FileSplit) context.getInputSplit();
			String fileName = fs.getPath().getName();
			if (fileName.startsWith("POS"))
				dataSource = true;
			else if (fileName.startsWith("NET"))
				dataSource = false;
			else
				throw new IOException("File Name should starts with POS or NET");
		}

		@Override
		protected void map(LongWritable key, Text value, Mapper<LongWritable, Text, Text, Text>.Context context)
				throws IOException, InterruptedException {
			String line = value.toString();
			TableLine tableLine = new TableLine();

			try {
				tableLine.set(line, this.dataSource, this.date, timepoint);
			} catch (LineException e) {
				if (e.getFlag() == -1)
					context.getCounter(Counter.OUTOFTIMESKIP).increment(1);
				else
					context.getCounter(Counter.TIMESKIP).increment(1);
				return;
			} catch (Exception e) {
				context.getCounter(Counter.LINESKIP).increment(1);
				return;
			}
			context.write(tableLine.outKey(), tableLine.outValue());

		}

	}

	/**
	 * 统计同一个IMSI在同一时间段 在不同CGI停留的时长
	 */
	public static class Reduce extends Reducer<Text, Text, NullWritable, Text> {
		private String date;
		private SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

		@Override
		protected void setup(Reducer<Text, Text, NullWritable, Text>.Context context)
				throws IOException, InterruptedException {
			this.date = context.getConfiguration().get("date"); // 读取日期
		}

		/**
		 * values:相同imsi和相同timeFlag的 作为的key
		 */
		@Override
		protected void reduce(Text key, Iterable<Text> values, Reducer<Text, Text, NullWritable, Text>.Context context)
				throws IOException, InterruptedException {
			String imsi = key.toString().split("\\|")[0];
			String timeFlag = key.toString().split("\\|")[1];

			// 用一个TreeMap记录时间 key:输出时间 value：位置代码
			TreeMap<Long, String> uploads = new TreeMap<Long, String>();
			String valueString;
			for (Text value : values) {
				valueString = value.toString();
				try {
					uploads.put(Long.valueOf(valueString.split("\\|")[1]), valueString.split("\\|")[0]);
				} catch (NumberFormatException e) {
					context.getCounter(Counter.TIMESKIP).increment(1);
					continue;
				}

			}

			try {
				// 在最后添加“OFF”位置
				Date tmp = this.formatter.parse(this.date + " " + timeFlag.split("-")[1] + ":00:00");
				uploads.put((tmp.getTime() / 1000L), "OFF");
				// 汇总数据
				HashMap<String, Float> locs = getStayTime(uploads);

				// 输出
				for (Entry<String, Float> entry : locs.entrySet()) {
					StringBuilder builder = new StringBuilder();
					builder.append(imsi).append("|");
					builder.append(entry.getKey()).append("|");
					builder.append(timeFlag).append("|");
					builder.append(entry.getValue());
					
					context.write( NullWritable.get(), new Text(builder.toString()) );
				}
			} catch (Exception e) {
				context.getCounter(Counter.USERSKIP).increment(1);
				return;
			}

		}

		/**
		 * 获得位置停留信息
		 * 
		 * @param uploads
		 *            一个TreeMap记录时间 key:输出时间 value：位置代码
		 * @return 一个TreeMap记录位置停留信息 key:位置代码 value：停留时间
		 */
		private HashMap<String, Float> getStayTime(TreeMap<Long, String> uploads) {
			Entry<Long, String> upload, nextUpload;
			HashMap<String, Float> locs = new HashMap<String, Float>();
			// 初始化
			Iterator<Entry<Long, String>> it = uploads.entrySet().iterator();
			upload = it.next();
			// 计算
			while (it.hasNext()) {
				nextUpload = it.next();
				float diff = (float) (nextUpload.getKey() - upload.getKey()) / 60.0f;
				if (diff <= 60.0) {
					if (locs.containsKey(upload.getValue()))
						locs.put(upload.getValue(), locs.get(upload.getValue()) + diff);
					else
						locs.put(upload.getValue(), diff);
				}
				upload = nextUpload;
			}
			return locs;

		}
	}

	@Override
	public int run(String[] args) throws Exception {
		Configuration conf = getConf();
		conf.set("date", "2013-09-12");
		conf.set("timepoint", "07-09-17-24");
		String input = "hdfs://192.168.25.137:9000/input/station";
		String output = "hdfs://192.168.25.137:9000/user/hdfs/station";

		Job job = new Job(conf, "BaseStationDataPreprocess");
		job.setJarByClass(BaseStationDataPreprocess.class);

		FileInputFormat.addInputPath( job, new Path(input) );			//输入路径
		FileOutputFormat.setOutputPath( job, new Path(output) );		//输出路径

		job.setMapperClass( Map.class );								//调用上面Map类作为Map任务代码
		job.setReducerClass ( Reduce.class );							//调用上面Reduce类作为Reduce任务代码
		job.setOutputFormatClass( TextOutputFormat.class );
		job.setOutputKeyClass( Text.class );
		job.setOutputValueClass( Text.class );

		job.waitForCompletion(true);

		return job.isSuccessful() ? 0 : 1;
		
	}

	public static void main(String[] args) throws Exception {
		
		// 运行任务
		int res = ToolRunner.run(new Configuration(), new BaseStationDataPreprocess(), args);

		System.exit(res);
	}

}

```



