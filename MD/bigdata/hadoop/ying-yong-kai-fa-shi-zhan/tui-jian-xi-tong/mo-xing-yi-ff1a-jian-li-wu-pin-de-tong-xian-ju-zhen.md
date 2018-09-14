> ## 建立物品的同现矩阵

* 按用户分组，找到每个用户所选的物品，单独出现计数及两两一组计数

|  | \[101\] | \[102\] | \[103\] | \[104\] | \[105\] | \[106\] | \[107\] |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| \[101\] | 5 | 3 | 4 | 4 | 2 | 2 | 1 |
| \[102\] | 3 | 3 | 3 | 2 | 1 | 1 | 0 |
| \[103\] | 4 | 3 | 4 | 3 | 1 | 2 | 0 |
| \[104\] | 4 | 2 | 3 | 4 | 2 | 2 | 1 |
| \[105\] | 2 | 1 | 1 | 2 | 2 | 1 | 1 |
| \[106\] | 2 | 1 | 2 | 2 | 1 | 2 | 0 |
| \[107\] | 1 | 0 | 0 | 1 | 1 | 0 | 1 |

---

> Step.1

* 转换数据，按照用户（key）分组，电影ID+评分 作为（value），逗号相连

```
  import java.io.IOException;
  import java.util.Iterator;
  import java.util.Map;

  import org.apache.hadoop.fs.Path;
  import org.apache.hadoop.io.IntWritable;
  import org.apache.hadoop.io.Text;
  import org.apache.hadoop.mapred.FileInputFormat;
  import org.apache.hadoop.mapred.FileOutputFormat;
  import org.apache.hadoop.mapred.JobClient;
  import org.apache.hadoop.mapred.JobConf;
  import org.apache.hadoop.mapred.MapReduceBase;
  import org.apache.hadoop.mapred.Mapper;
  import org.apache.hadoop.mapred.OutputCollector;
  import org.apache.hadoop.mapred.Reducer;
  import org.apache.hadoop.mapred.Reporter;
  import org.apache.hadoop.mapred.RunningJob;
  import org.apache.hadoop.mapred.TextInputFormat;
  import org.apache.hadoop.mapred.TextOutputFormat;


  public class Step1 {

      public static class Step1_ToItemPreMapper extends MapReduceBase implements Mapper<Object, Text, IntWritable, Text> {
          private final static IntWritable k = new IntWritable();
          private final static Text v = new Text();

          @Override
          public void map(Object key, Text value, OutputCollector<IntWritable, Text> output, Reporter reporter)
                  throws IOException {
              String[] tokens = Recommend.DELIMITER.split(value.toString());
              int userID = Integer.parseInt(tokens[0]);
              String itemID = tokens[1];
              String pref = tokens[2];
              k.set(userID);
              v.set(itemID + ":" + pref);
              output.collect(k, v);
          }

      }

      public static class Step1_ToUserVectorReducer extends MapReduceBase
              implements Reducer<IntWritable, Text, IntWritable, Text> {
          private final static Text v = new Text();

          @Override
          public void reduce(IntWritable key, Iterator<Text> values, OutputCollector<IntWritable, Text> output,
                  Reporter reporter) throws IOException {
              StringBuilder sb = new StringBuilder();
              while (values.hasNext()) {
                  sb.append("," + values.next());
              }
              v.set(sb.toString().replaceFirst(",", ""));
              output.collect(key, v);

          }

      }

      public static void run(Map<String, String> path) throws IOException {
          JobConf conf = Recommend.config();

          String input = path.get("Step1Input");
          String output = path.get("Step1Output");
          HdfsDAO hdfs = new HdfsDAO(Recommend.HDFS, conf);

          hdfs.rmr(input);
          hdfs.mkdirs(input);
          hdfs.copyFile(path.get("data"), input);

          conf.setMapOutputKeyClass(IntWritable.class);
          conf.setMapOutputValueClass(Text.class);

          conf.setOutputKeyClass(IntWritable.class);
          conf.setOutputValueClass(Text.class);

          conf.setMapperClass(Step1_ToItemPreMapper.class);
          conf.setReducerClass(Step1_ToUserVectorReducer.class);

          conf.setInputFormat(TextInputFormat.class);
          conf.setOutputFormat(TextOutputFormat.class);

          FileInputFormat.setInputPaths(conf, new Path(input));
          FileOutputFormat.setOutputPath(conf, new Path(output));

          RunningJob job = JobClient.runJob(conf);
          while (!job.isComplete()) {
              job.waitForCompletion();
          }
      }
  }
```

**第一步之后的数据：**

```
1    101:5.0,102:3.0,103:2.5
2    101:2.0,102:2.5,103:5.0,104:2.0
3    107:5.0,105:4.5,104:4.0,101:2.0
4    106:4.0,103:3.0,101:5.0,104:4.5
5    104:4.0,105:3.5,106:4.0,101:4.0,102:3.0,103:2.0
```

---

> Step.2

* 对物品组合列表进行计数，建立物品的同现矩阵

```
  import java.io.IOException;
  import java.util.Iterator;
  import java.util.Map;

  import org.apache.hadoop.fs.Path;
  import org.apache.hadoop.io.IntWritable;
  import org.apache.hadoop.io.LongWritable;
  import org.apache.hadoop.io.Text;
  import org.apache.hadoop.mapred.FileInputFormat;
  import org.apache.hadoop.mapred.FileOutputFormat;
  import org.apache.hadoop.mapred.JobClient;
  import org.apache.hadoop.mapred.JobConf;
  import org.apache.hadoop.mapred.MapReduceBase;
  import org.apache.hadoop.mapred.Mapper;
  import org.apache.hadoop.mapred.OutputCollector;
  import org.apache.hadoop.mapred.Reducer;
  import org.apache.hadoop.mapred.Reporter;
  import org.apache.hadoop.mapred.RunningJob;
  import org.apache.hadoop.mapred.TextInputFormat;
  import org.apache.hadoop.mapred.TextOutputFormat;

  public class Step2 {

      public static class Step2_UserVectorToCooccurrenceMapper extends MapReduceBase
              implements Mapper<LongWritable, Text, Text, IntWritable> {
          private final static Text k = new Text();
          private final static IntWritable v = new IntWritable(1);

          @Override
          public void map(LongWritable key, Text value, OutputCollector<Text, IntWritable> output, Reporter reporter)
                  throws IOException {
              String[] tokens = Recommend.DELIMITER.split(value.toString());
              for (int i = 1; i < tokens.length; i++) {
                  String itemID = tokens[i].split(":")[0];
                  for (int j = 1; j < tokens.length; j++) {
                      String itemID2 = tokens[j].split(":")[0];
                      k.set(itemID + ":" + itemID2);
                      output.collect(k, v);
                  }
              }

          }

      }

      public static class Step2_UserVectorToConoccurrenceReducer extends MapReduceBase
              implements Reducer<Text, IntWritable, Text, IntWritable> {
          private IntWritable result = new IntWritable();

          @Override
          public void reduce(Text key, Iterator<IntWritable> values, OutputCollector<Text, IntWritable> output,
                  Reporter reporter) throws IOException {
              int sum = 0;
              while (values.hasNext()) {
                  sum += values.next().get();
              }
              result.set(sum);
              output.collect(key, result);

          }

      }

      public static void run(Map<String, String> path) throws IOException {
          JobConf conf = Recommend.config();

          String input = path.get("Step2Input");
          String output = path.get("Step2Output");
          HdfsDAO hdfs = new HdfsDAO(Recommend.HDFS, conf);

          hdfs.rmr(output);

          conf.setOutputKeyClass(Text.class);
          conf.setOutputValueClass(IntWritable.class);

          conf.setMapperClass(Step2_UserVectorToCooccurrenceMapper.class);
          conf.setCombinerClass(Step2_UserVectorToConoccurrenceReducer.class);
          conf.setReducerClass(Step2_UserVectorToConoccurrenceReducer.class);

          conf.setInputFormat(TextInputFormat.class);
          conf.setOutputFormat(TextOutputFormat.class);

          FileInputFormat.setInputPaths(conf, new Path(input));
          FileOutputFormat.setOutputPath(conf, new Path(output));

          RunningJob job = JobClient.runJob(conf);
          while (!job.isComplete()) {
              job.waitForCompletion();
          }
      }

  }
```

* 最后的输出数据

  ```
  101:101	5
  101:102	3
  101:103	4
  101:104	4
  101:105	2
  101:106	2
  101:107	1
  102:101	3
  102:102	3
  102:103	3
  102:104	2
  102:105	1
  102:106	1
  103:101	4
  103:102	3
  103:103	4
  103:104	3
  103:105	1
  103:106	2
  104:101	4
  104:102	2
  104:103	3
  104:104	4
  104:105	2
  104:106	2
  104:107	1
  105:101	2
  105:102	1
  105:103	1
  105:104	2
  105:105	2
  105:106	1
  105:107	1
  106:101	2
  106:102	1
  106:103	2
  106:104	2
  106:105	1
  106:106	2
  107:101	1
  107:104	1
  107:105	1
  107:107	1
  ```



