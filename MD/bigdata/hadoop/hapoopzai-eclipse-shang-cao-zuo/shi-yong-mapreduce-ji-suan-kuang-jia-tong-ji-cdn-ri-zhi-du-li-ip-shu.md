## 使用MapReduce计算框架统计CDN日志独立IP数

```
package org.dataguru.mr.kpi;

import java.io.IOException;
import java.util.HashSet;
import java.util.Iterator;
import java.util.Set;

import org.apache.hadoop.fs.Path;
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
import org.apache.hadoop.mapred.TextInputFormat;
import org.apache.hadoop.mapred.TextOutputFormat;

public class KPIIP {

    public static class KPIIPMapper extends MapReduceBase implements Mapper<Object, Text, Text, Text> {
        private Text word = new Text();
        private Text ips = new Text();

        @Override
        public void map(Object key, Text value, OutputCollector<Text, Text> output, Reporter reporter)
                throws IOException {
            KPI kpi = KPI.filterIPs(value.toString());
            if (kpi.isValid()) {
                word.set(kpi.getRequest());
                ips.set(kpi.getRemote_addr());
                output.collect(word, ips);
                // System.out.println("//ips>>"+ips);
            }
        }
    }

    public static class KPIIPReducer extends MapReduceBase implements Reducer<Text, Text, Text, Text> {

        private Text result = new Text();
        @Override
        public void reduce(Text key, Iterator<Text> values, OutputCollector<Text, Text> output, Reporter reporter)
                throws IOException {

            Set<String> count = new HashSet<String>();
            while (values.hasNext()) {
                count.add(values.next().toString());
            }
            result.set(String.valueOf(count.size()));
            output.collect(key, result);
        }

    }

    public static void main(String[] args) throws Exception {
        String input = "hdfs://192.168.25.137:9000/input/access_log.txt";
        String output = "hdfs://192.168.25.137:9000/user/hdfs/ip";

        JobConf conf = new JobConf(KPIIP.class);
        conf.setJobName("VideoIpCount");

        conf.setMapOutputKeyClass(Text.class);
        conf.setMapOutputValueClass(Text.class);

        conf.setOutputKeyClass(Text.class);
        conf.setOutputValueClass(Text.class);

        conf.setMapperClass(KPIIPMapper.class);
        conf.setReducerClass(KPIIPReducer.class);

        conf.setInputFormat(TextInputFormat.class);
        conf.setOutputFormat(TextOutputFormat.class);

        FileInputFormat.setInputPaths(conf, new Path(input));
        FileOutputFormat.setOutputPath(conf, new Path(output));

        JobClient.runJob(conf);
        System.exit(0);
    }

}
```

```
package org.dataguru.mr.kpi;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.HashSet;
import java.util.Locale;
import java.util.Set;

/*
 * KPI Object
 */
/*
 * KPI Object
 */
public class KPI {
	private String remote_addr;// 记录客户端的ip地址
	private String remote_user;// 记录客户端用户名称,忽略属性"-"
	private String time_local;// 记录访问时间与时区
	private String request;// 记录请求的url与http协议
	private String status;// 记录请求状态；成功是200
	private String body_bytes_sent;// 记录发送给客户端文件主体内容大小
	private String http_referer;// 用来记录从那个页面链接访问过来的
	private String http_user_agent;// 记录客户浏览器的相关信息
	private boolean valid = true;// 判断数据是否合法

	private static KPI parser(String line) {
		//System.out.println(line);
		KPI kpi = new KPI();
		String[] arr = line.split(" ");
		if (arr.length > 11) {
			kpi.setRemote_addr(arr[0]);
			kpi.setRemote_user(arr[1]);
			kpi.setTime_local(arr[3].substring(1));
			kpi.setRequest(arr[6]);
			kpi.setStatus(arr[8]);
			kpi.setBody_bytes_sent(arr[9]);
			kpi.setHttp_referer(arr[10]);
			if (arr.length > 12) {
				kpi.setHttp_user_agent(arr[11] + " " + arr[12]);
			} else {
				kpi.setHttp_user_agent(arr[11]);
			}
			if (Integer.parseInt(kpi.getStatus()) >= 400) {// 大于400，HTTP错误
				kpi.setValid(false);
			}
		} else {
			kpi.setValid(false);
		}
		return kpi;
	}

	/**
	 * 按page的pv分类
	 */
	public static KPI filterPVs(String line) {
		/*KPI kpi = parser(line);
		Set<String> pages = new HashSet<String>();
		pages.add("/forum-46-1.html");
		pages.add("/forum-58-1.html");
		pages.add("/forum-61-1.html");
		if (!pages.contains(kpi.getRequest())) {
			kpi.setValid(false);
		}
		return kpi;*/
		return parser(line);
	}

	/**
	 * 按page的独立ip分类
	 */
	public static KPI filterIPs(String line) {
		/*KPI kpi = parser(line);
		Set<String> pages = new HashSet<String>();
		pages.add("/forum-46-1.html");
		pages.add("/forum-58-1.html");
		pages.add("/forum-61-1.html");
		if (!pages.contains(kpi.getRequest())) {
			kpi.setValid(false);
		}
		return kpi;*/
		return parser(line);
	}

	/**
	 * PV按浏览器分类
	 */
	public static KPI filterBroswer(String line) {
		return parser(line);
	}

	/**
	 * PV按小时分类
	 */
	public static KPI filterTime(String line) {
		return parser(line);
	}

	/**
	 * PV按访问域名分类
	 */
	public static KPI filterDomain(String line) {
		return parser(line);
	}

	@Override
	public String toString() {
		StringBuilder sb = new StringBuilder();
		sb.append("valid:" + this.valid);
		sb.append("\nremote_addr:" + this.remote_addr);
		sb.append("\nremote_user:" + this.remote_user);
		sb.append("\ntime_local:" + this.time_local);
		sb.append("\nrequest:" + this.request);
		sb.append("\nstatus:" + this.status);
		sb.append("\nbody_bytes_sent:" + this.body_bytes_sent);
		sb.append("\nhttp_referer:" + this.http_referer);
		sb.append("\nhttp_user_agent:" + this.http_user_agent);
		return sb.toString();
	}

	public String getRemote_addr() {
		return remote_addr;
	}

	public void setRemote_addr(String remote_addr) {
		this.remote_addr = remote_addr;
	}

	public String getRemote_user() {
		return remote_user;
	}

	public void setRemote_user(String remote_user) {
		this.remote_user = remote_user;
	}

	public String getTime_local() {
		return time_local;
	}

	public Date getTime_local_Date() throws ParseException {
		SimpleDateFormat df = new SimpleDateFormat("dd/MMM/yyyy:HH:mm:ss",
				Locale.US);
		return df.parse(this.time_local);
	}

	public String getTime_local_Date_hour() throws ParseException {
		SimpleDateFormat df = new SimpleDateFormat("yyyyMMddHH");
		return df.format(this.getTime_local_Date());
	}

	public void setTime_local(String time_local) {
		this.time_local = time_local;
	}

	public String getRequest() {
		return request;
	}

	public void setRequest(String request) {
		this.request = request;
	}

	public String getStatus() {
		return status;
	}

	public void setStatus(String status) {
		this.status = status;
	}

	public String getBody_bytes_sent() {
		return body_bytes_sent;
	}

	public void setBody_bytes_sent(String body_bytes_sent) {
		this.body_bytes_sent = body_bytes_sent;
	}

	public String getHttp_referer() {
		return http_referer;
	}

	public String getHttp_referer_domain() {
		if (http_referer.length() < 8) {
			return http_referer;
		}
		String str = this.http_referer.replace("\"", "").replace("http://", "")
				.replace("https://", "");
		return str.indexOf("/") > 0 ? str.substring(0, str.indexOf("/")) : str;
	}

	public void setHttp_referer(String http_referer) {
		this.http_referer = http_referer;
	}

	public String getHttp_user_agent() {
		return http_user_agent;
	}

	public void setHttp_user_agent(String http_user_agent) {
		this.http_user_agent = http_user_agent;
	}

	public boolean isValid() {
		return valid;
	}

	public void setValid(boolean valid) {
		this.valid = valid;
	}

	public static void main(String args[]) {
		String line = "112.97.24.243 - - [31/Jan/2012:00:14:48 +0800] \"GET /data/cache/style_2_common.css?AZH HTTP/1.1\" 200 57752 \"http://f.dataguru.cn/forum-58-1.html\" \"Mozilla/5.0 (iPhone; CPU iPhone OS 5_0_1 like Mac OS X) AppleWebKit/534.46 (KHTML, like Gecko) Mobile/9A406\"";
		System.out.println(line);
		KPI kpi = new KPI();
		String[] arr = line.split(" ");
		kpi.setRemote_addr(arr[0]);
		kpi.setRemote_user(arr[1]);
		kpi.setTime_local(arr[3].substring(1));
		kpi.setRequest(arr[6]);
		kpi.setStatus(arr[8]);
		kpi.setBody_bytes_sent(arr[9]);
		kpi.setHttp_referer(arr[10]);
		kpi.setHttp_user_agent(arr[11] + " " + arr[12]);
		System.out.println(kpi);
		try {
			SimpleDateFormat df = new SimpleDateFormat("yyyy.MM.dd:HH:mm:ss z",Locale.US);
			System.out.println(df.format(kpi.getTime_local_Date()));
			System.out.println(kpi.getTime_local_Date_hour());
			System.out.println(kpi.getHttp_referer_domain());
		} catch (ParseException e) {
			e.printStackTrace();
		}
	}
}
```



