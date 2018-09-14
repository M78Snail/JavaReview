> ## 合并矩阵，并计算推荐结果列表

* 物品矩阵

|  | \[101\] | \[102\] | \[103\] | \[104\] | \[105\] | \[106\] | \[107\] |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| \[101\] | 5 | 3 | 4 | 4 | 2 | 2 | 1 |
| \[102\] | 3 | 3 | 3 | 2 | 1 | 1 | 0 |
| \[103\] | 4 | 3 | 4 | 3 | 1 | 2 | 0 |
| \[104\] | 4 | 2 | 3 | 4 | 2 | 2 | 1 |
| \[105\] | 2 | 1 | 1 | 2 | 2 | 1 | 1 |
| \[106\] | 2 | 1 | 2 | 2 | 1 | 2 | 0 |
| \[107\] | 1 | 0 | 0 | 1 | 1 | 0 | 1 |

* 评分矩阵

|  | 1 | 2 | 3 | 4 | 5 |
| :---: | :---: | :---: | :---: | :---: | :---: |
| \[101\] | 5 | 2 | 2 | 5.0 | 4.0 |
| \[102\] | 3 | 2.5 | 0 | 0 | 3.0 |
| \[103\] | 2.5 | 5 | 0 | 3.0 | 2.0 |
| \[104\] | 0 | 2 | 4 | 4.5 | 4.0 |
| \[105\] | 0 | 0 | 4.5 | 0 | 3.5 |
| \[106\] | 0 | 0 | 0 | 4.0 | 4.0 |
| \[107\] | 0 | 0 | 5.0 | 0 | 0 |

* 推荐分数

|  | 1 | 2 | 3 | 4 | 5 |
| :---: | :---: | :---: | :---: | :---: | :---: |
| \[101\] | 44.0 | 45.5 | 40.0 | 63.0 | 68.0 |
| \[102\] | 31.5 | 32.5 | 18.5 | 37.0 | 42.5 |
| \[103\] | 39.0 | 41.5 | 24.5 | 53.5 | 56.5 |
| \[104\] | 33.5 | 36.0 | 38.0 | 55.0 | 59.0 |
| \[105\] | 15.5 | 15.5 | 26.0 | 26.0 | 32.0 |
| \[106\] | 18.0 | 20.5 | 16.5 | 33.0 | 34.5 |
| \[107\] | 5.0 | 4.0 | 15.5 | 9.5 | 11.5 |

> 算法思想：  
> 用户1对101的打分（5），同时101和其他电影的数量的共存数 相乘得到：
>
> （1，101 ：25）（1，102：15）。。。
>
> 用户1对102的打分（3），同时102和其他电影的数量的共存数 相乘得到：
>
> （1，101：9）（1，102：9）。。。
>
> 用户1对103的打分（2.5），同时103和其他电影的数量的共存数 相乘得到：
>
> （1，101：10）（1，102：7.5）。。。
>
> 则用户1，对于101的打分为：25+9+10=44
>
> 其他用户和电影评分依此类推。

```
import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Iterator;
import java.util.List;
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

public class Step4 {
    public static class Step4_PartialMultiplyMapper extends MapReduceBase
            implements Mapper<LongWritable, Text, IntWritable, Text> {
        private final static IntWritable k = new IntWritable();
        private final static Text v = new Text();

        private final static Map<Integer, List<Cooccurrence>> cooccurrenceMatrix = new HashMap<Integer, List<Cooccurrence>>();

        @Override
        public void map(LongWritable key, Text value, OutputCollector<IntWritable, Text> output, Reporter reporter)
                throws IOException {
            String[] tokens = Recommend.DELIMITER.split(value.toString());

            String[] v1 = tokens[0].split(":");
            String[] v2 = tokens[1].split(":");
            // cooccurrence
            if (v1.length > 1) {
                int itemID1 = Integer.parseInt(v1[0]);
                int itemID2 = Integer.parseInt(v1[1]);
                int num = Integer.parseInt(tokens[1]);
                List<Cooccurrence> list = null;
                if (!cooccurrenceMatrix.containsKey(itemID1)) {
                    list = new ArrayList<Cooccurrence>();
                } else {
                    list = cooccurrenceMatrix.get(itemID1);
                }
                list.add(new Cooccurrence(itemID1, itemID2, num));
                cooccurrenceMatrix.put(itemID1, list);
            }

            // userVector
            if (v2.length > 1) {
                // 商品ID
                int itemID = Integer.parseInt(tokens[0]);
                int userID = Integer.parseInt(v2[0]);
                // 分数
                double pref = Double.parseDouble(v2[1]);
                k.set(userID);
                for (Cooccurrence co : cooccurrenceMatrix.get(itemID)) {
                    v.set(co.getItemID2() + "," + pref * co.getNum());
                    output.collect(k, v);
                }
            }
        }

    }

     public static class Step4_AggregateAndRecommendReducer extends MapReduceBase implements Reducer<IntWritable, Text, IntWritable, Text> {
            private final static Text v = new Text();

            @Override
            public void reduce(IntWritable key, Iterator<Text> values, OutputCollector<IntWritable, Text> output, Reporter reporter) throws IOException {
                Map<String, Double> result = new HashMap<String, Double>();
                while (values.hasNext()) {
                    String[] str = values.next().toString().split(",");
                    if (result.containsKey(str[0])) {
                        result.put(str[0], result.get(str[0]) + Double.parseDouble(str[1]));
                    } else {
                        result.put(str[0], Double.parseDouble(str[1]));
                    }
                }
                Iterator<String> iter = result.keySet().iterator();
                while (iter.hasNext()) {
                    String itemID = iter.next();
                    double score = result.get(itemID);
                    v.set(itemID + "," + score);
                    output.collect(key, v);
                }
            }
        }


    public static void run(Map<String, String> path) throws IOException {
        JobConf conf = Recommend.config();

        String input1 = path.get("Step4Input1");
        String input2 = path.get("Step4Input2");
        String output = path.get("Step4Output");

        HdfsDAO hdfs = new HdfsDAO(Recommend.HDFS, conf);
        hdfs.rmr(output);

        conf.setOutputKeyClass(IntWritable.class);
        conf.setOutputValueClass(Text.class);

        conf.setMapperClass(Step4_PartialMultiplyMapper.class);
        conf.setCombinerClass(Step4_AggregateAndRecommendReducer.class);
        conf.setReducerClass(Step4_AggregateAndRecommendReducer.class);

        conf.setInputFormat(TextInputFormat.class);
        conf.setOutputFormat(TextOutputFormat.class);

        FileInputFormat.setInputPaths(conf, new Path(input1), new Path(input2));
        FileOutputFormat.setOutputPath(conf, new Path(output));

        RunningJob job = JobClient.runJob(conf);
        while (!job.isComplete()) {
            job.waitForCompletion();
        }
    }
}

class Cooccurrence {
    private int itemID1;
    private int itemID2;
    private int num;

    public Cooccurrence(int itemID1, int itemID2, int num) {
        super();
        this.itemID1 = itemID1;
        this.itemID2 = itemID2;
        this.num = num;
    }

    public int getItemID1() {
        return itemID1;
    }

    public void setItemID1(int itemID1) {
        this.itemID1 = itemID1;
    }

    public int getItemID2() {
        return itemID2;
    }

    public void setItemID2(int itemID2) {
        this.itemID2 = itemID2;
    }

    public int getNum() {
        return num;
    }

    public void setNum(int num) {
        this.num = num;
    }

}
```

最后的数据：

```
1    101,44.0
1    102,31.5
1    103,39.0
1    104,33.5
1    105,15.5
1    106,18.0
1    107,5.0
2    101,45.5
2    102,32.5
2    103,41.5
2    104,36.0
2    105,15.5
2    106,20.5
2    107,4.0
3    101,40.0
3    102,18.5
3    103,24.5
3    104,38.0
3    105,26.0
3    106,16.5
3    107,15.5
4    101,63.0
4    102,37.0
4    103,53.5
4    104,55.0
4    105,26.0
4    106,33.0
4    107,9.5
5    101,68.0
5    102,42.5
5    103,56.5
5    104,59.0
5    105,32.0
5    106,34.5
5    107,11.5
```



