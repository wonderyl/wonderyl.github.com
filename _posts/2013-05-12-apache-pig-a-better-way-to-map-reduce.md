---
layout: post
title: "Apache Pig, a better way to map reduce"
description: ""
category: 
tags: [Apache Pig, big data, pig]
---
{% include JB/setup %}
<span style="font-family: Courier New, Courier, monospace;">中文版：<a href="http://blog.csdn.net/wonderyl/article/details/8226340">Apache Pig, 便捷的map reduce之道</a></span>
<br />
<span style="font-family: Courier New, Courier, monospace;">Have used <a href="http://pig.apache.org/">Apache Pig</a> for about a year before I start to write anything about it.&nbsp;</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><b>First</b>, you won't want to use pig or map reduce if your data is in the scale of MB or even GB, it will no more efficient&nbsp;than python. But for big data, it's beyond the ability of single machine, so you have to think about&nbsp;parallel process, which is map reduce used for, and pig is the tool that make MR more easier and efficient.&nbsp;&nbsp;</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><b>Second</b>, I have to say it's very handy and&nbsp;elegant that you can make simple statistical or data transformation with only several lines of code. eg. you want to count how many query view for each query through a search log, the code looks like below:</span>
<br />
<span style="font-family: 'Courier New', Courier, monospace;">---pig example for qv---</span>
<br />
<span style="font-family: Courier New, Courier, monospace;">log = load 'xxx' as (query);</span>
<br />
<span style="font-family: Courier New, Courier, monospace;">grp = group log by query;</span>
<br />
<span style="font-family: Courier New, Courier, monospace;">qv = for each grp generate group as query, COUNT(log) as qv;</span>
<br />
<span style="font-family: Courier New, Courier, monospace;">------</span>
<br />
<span style="font-family: Courier New, Courier, monospace;">If you write map reduce, you have to follow the&nbsp;routines and copy-paste tens of lines of code before you start to write your logic. eg. in java on hadoop is append at the end, because it's really too long.</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><b>Third,</b> pig like any other script languages, is very convenient to test or run, just modify the script and run, but with java, you have to modify, compile, package src as jar and then execute.</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><b>Fourth</b>, when using java or even streaming on hadoop, you have to run you code on real cluster to test, but with pig, you can use "-x local" to run locally with local file system, which is not only faster but also more easier to check out results than using hdfs. you can use this feature to process small data if you're willing to.</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><b>Fifth</b>, grammar check with "-c" makes me more comfortable while coding pig, you don't have to be worried about yourself missing one letter will cause you test again or even miss to tell the bug because of lack of unit test like with python.</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><b>Sixth</b>, pig has a very extendable API, with which you can implement your own UDF(user defined function) to deal with more complex data element process. eg. make string transform, calculate variance of a bag of data, or read/store data into/from a DIY format. further, java functions from jdk can be directly used without additional code.</span>
<span style="font-family: Courier New, Courier, monospace;">
<br /></span>
<span style="font-family: Courier New, Courier, monospace;">---mr example for qv---</span>
<br />
<span style="font-family: Courier New, Courier, monospace;">import xxxx;</span>
<br />
<span style="font-family: Courier New, Courier, monospace;">...</span>
<br />
<span style="font-family: Courier New, Courier, monospace;">import xxxx;</span>
<br />
<span style="font-family: Courier New, Courier, monospace;">public class QueryCount extends Configured implements Tool{</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span>public static class Map extends Mapper&lt;LongWritable, Text, Text, IntWritable&gt; {</span>
<br />

<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;">  </span>@Override</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;">  </span>public void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException{</span>
<br />

<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span><span class="Apple-tab-span" style="white-space: pre;"> </span><span class="Apple-tab-span" style="white-space: pre;">  </span>//map logic</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;">  </span>}</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span>}</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span>public static class Reduce extends Reducer&lt;Text, IntWritable, Text, IntWritable&gt;{</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;">  </span>@Override</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;">  </span>public void reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context) throws IOException, InterruptedException{</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;">   </span>//reduce logic</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;">  </span>}</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span>}</span>
<br />

<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span>@Override</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span>public int run(String[] args) throws Exception {</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span><span class="Apple-tab-span" style="white-space: pre;"> </span>Job job = new Job(getConf());</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span><span class="Apple-tab-span" style="white-space: pre;"> </span>//some settings on job</span>
<br />

<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span><span class="Apple-tab-span" style="white-space: pre;"> </span>boolean success = job.waitForCompletion(true);</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;">  </span>return success?0:1;</span>
<br />

<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span>}</span>
<br />

<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span>public static void main(String[] args) throws Exception{</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;">  </span>int ret = ToolRunner.run(new QueryCount(), args);</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;">  </span>System.exit(ret);</span>
<br />
<span style="font-family: Courier New, Courier, monospace;"><span class="Apple-tab-span" style="white-space: pre;"> </span>}</span>
<br />

<br />

<br />
<span style="font-family: Courier New, Courier, monospace;">}</span>
<br />
<span style="font-family: Courier New, Courier, monospace;">------</span>
<br />

<br />