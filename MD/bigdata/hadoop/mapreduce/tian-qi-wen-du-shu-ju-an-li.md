案例

* **Mapper**

  ```
  import java.io.IOException;

  import org.apache.hadoop.io.IntWritable;
  import org.apache.hadoop.io.LongWritable;
  import org.apache.hadoop.io.Text;
  import org.apache.hadoop.mapred.MapReduceBase;
  import org.apache.hadoop.mapred.Mapper;
  import org.apache.hadoop.mapred.OutputCollector;
  import org.apache.hadoop.mapred.Reporter;

  public class MaxTemperatureMapper extends MapReduceBase implements Mapper<LongWritable, Text, Text, IntWritable> {

  	private static final int MISSING = 9999;

  	@Override
  	public void map(LongWritable key, Text value, OutputCollector<Text, IntWritable> output, Reporter reporter)
  			throws IOException {
  		String line = value.toString();
  		String year = line.substring(15, 19);
  		int airTemperature;
  		if (line.charAt(87) == '+') {
  			airTemperature = Integer.parseInt(line.substring(88, 92));
  		} else {
  			airTemperature = Integer.parseInt(line.substring(87, 92));
  		}

  		String quality = line.substring(92, 93);
  		if (airTemperature != MISSING && quality.matches("[01459]")) {
  			output.collect(new Text(year), new IntWritable(airTemperature));
  		}
  	}

  }

  ```

* **Reducer**

  ```
  import java.io.IOException;
  import java.util.Iterator;

  import org.apache.hadoop.io.IntWritable;
  import org.apache.hadoop.io.Text;
  import org.apache.hadoop.mapred.MapReduceBase;
  import org.apache.hadoop.mapred.OutputCollector;
  import org.apache.hadoop.mapred.Reducer;
  import org.apache.hadoop.mapred.Reporter;

  public class MaxTemperatureReducer extends MapReduceBase implements Reducer<Text, IntWritable, Text, IntWritable> {

      @Override
      public void reduce(Text key, Iterator<IntWritable> values, OutputCollector<Text, IntWritable> output,
              Reporter reporter) throws IOException {
          int maxValue = Integer.MIN_VALUE;
          while (values.hasNext()) {
              maxValue = Math.max(maxValue, values.next().get());
          }
          output.collect(key, new IntWritable(maxValue));

      }

  }
  ```

* **M-R job**

  ```
  import org.apache.hadoop.fs.Path;
  import org.apache.hadoop.io.IntWritable;
  import org.apache.hadoop.io.Text;
  import org.apache.hadoop.mapred.FileInputFormat;
  import org.apache.hadoop.mapred.FileOutputFormat;
  import org.apache.hadoop.mapred.JobClient;
  import org.apache.hadoop.mapred.JobConf;

  public class MaxTemperature {

      public static void main(String[] args) throws Exception {
          if (args.length != 2) {
              System.out.println("Usage:MaxTemperature <input path> <output path>");
              System.exit(-1);
          }

          JobConf conf = new JobConf(MaxTemperature.class);
          conf.setJobName("Max temperature");
          FileInputFormat.addInputPath(conf, new Path(args[0]));
          FileOutputFormat.setOutputPath(conf, new Path(args[1]));
          conf.setMapperClass(MaxTemperatureMapper.class);
          conf.setReducerClass(MaxTemperatureReducer.class);
          conf.setOutputKeyClass(Text.class);
          conf.setOutputValueClass(IntWritable.class);
          JobClient.runJob(conf);
      }

  }
  ```

### 编译执行

* 生成class
  ```
  javac -classpath ../hadoop-core-1.1.2.jar *java
  ```
* 打包成jar包，如果直接使用class可能会出现Class not found 错误
  ```
  jar cvf ./MaxTemperature.jar ./*class
  mv MaxTemperature.jar ..
  rm *.class
  ```
* 打包后将原有的class删除,运行jar
  ```
  bin/hadoop jar ./MaxTemperature.jar MaxTemperature ./in/723440-13964-1959 ./out6
  ```

### 查看结果

```
bin/hadoop fs -cat ./out6/part-00000

1959    361
```

### 旧版气象数据

* 下载地址:

[ftp://ftp.ncdc.noaa.gov/pub/data/noaa/](ftp://ftp.ncdc.noaa.gov/pub/data/noaa/)

