# 🧪 Hadoop MapReduce Practical
## Find User with Maximum Login Time

---

## ▶️ Step 1: Start Hadoop Services
```bash
start-dfs.sh
start-yarn.sh
jps

Expected Output:

NameNode
DataNode
ResourceManager
NodeManager
📂 Step 2: Create Input File
nano log.txt
Paste:
user1 10
user2 20
user1 30
user3 50
user2 15
Save:
CTRL + X → Y → Enter
📥 Step 3: Upload File to HDFS
hdfs dfs -mkdir /input
hdfs dfs -put log.txt /input
hdfs dfs -ls /input
💻 Step 4: Create Java Program
nano MaxLogin.java
Paste Code:
import java.io.IOException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.*;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class MaxLogin {

    public static class Map extends Mapper<LongWritable, Text, Text, IntWritable> {
        public void map(LongWritable key, Text value, Context context)
                throws IOException, InterruptedException {

            String[] parts = value.toString().split(" ");
            context.write(new Text(parts[0]), new IntWritable(Integer.parseInt(parts[1])));
        }
    }

    public static class Reduce extends Reducer<Text, IntWritable, Text, IntWritable> {
        public void reduce(Text key, Iterable<IntWritable> values, Context context)
                throws IOException, InterruptedException {

            int sum = 0;
            for (IntWritable val : values) {
                sum += val.get();
            }

            context.write(key, new IntWritable(sum));
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf, "Max Login Time");

        job.setJarByClass(MaxLogin.class);
        job.setMapperClass(Map.class);
        job.setReducerClass(Reduce.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
⚙️ Step 5: Compile Program
javac -classpath `hadoop classpath` -d . MaxLogin.java
📦 Step 6: Create JAR File
jar -cvf maxlogin.jar *
❌ Step 7: Remove Old Output (IMPORTANT)
hdfs dfs -rm -r /output

Ignore error if folder does not exist.

🚀 Step 8: Run MapReduce Job
hadoop jar maxlogin.jar MaxLogin /input /output
📊 Step 9: View Output
hdfs dfs -cat /output/part-r-00000
Output:
user1 40
user2 35
user3 50
🏆 Step 10: Find Maximum User
hdfs dfs -cat /output/part-r-00000 | sort -k2 -nr | head -1
Final Result:
user3 50
🔄 Re-run Program
hdfs dfs -rm -r /output
hadoop jar maxlogin.jar MaxLogin /input /output
🧠 Explanation
Mapper → outputs (user, time)
Reducer → sums login time
Final → user with highest time
🎯 Conclusion

User user3 has maximum login time (50 minutes).


---

# 🚀 **HOW TO ADD THIS FILE**

Inside your folder:
```bash
nano README.md

👉 Paste everything → Save (CTRL + X → Y → Enter)
