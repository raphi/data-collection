# Getting Data From Csv Into S3 Using Fluentd

Looking to get data out of csv into s3? You can do that with [fluentd](//fluentd.org) in **10 minutes**!

<table>
  <td>![](/images/plugin_icon/csv.png)</td>
  <td><span style="font-size:50px">&#8594;</span></td>
  <td>![](/images/plugin_icon/s3.png)</td>
</table>

Here is how:

```bash
$ gem install fluentd
$ gem install fluent-plugin-s3
$ touch fluentd.conf
```

`fluentd.conf` should look like this (just copy and paste this into fluentd.conf):


    <source>
      type tail
      path /var/log/httpd-access.log #...or where you placed your Apache access log
      pos_file /var/log/td-agent/httpd-access.log.pos # This is where you record file position
      tag foobar.csv #fluentd tag!
      format csv
      keys key1, key2, key3 # e.g., user_id, timestamp, action
      time_key key2 # Specify the column that you want to use as timestamp
    </source>

    <match **>
      type s3
      path <s3 path> #(optional; default="")
      time_format <format string> #(optional; default is ISO-8601)
      aws_key_id <Your AWS key id> #(required)
      aws_sec_key <Your AWS secret key> #(required)
      s3_bucket <s3 bucket name> #(required)
      s3_endpoint <s3 endpoint name> #(required; ex: s3-us-west-1.amazonaws.com)
      s3_object_key_format <format string> #(optional; default="%{path}%{time_slice}_%{index}.%{file_extension}")
      auto_create_bucket <true/false> #(optional; default=true)
      check_apikey_on_start <true/false> #(optional; default=true)
      proxy_uri <proxy uri string> #(optional)
    </match>

After that, you can start fluentd and everything should work:

```bash
 $ fluentd -c fluentd.conf
```

Of course, this is just a quick example. If you are thinking of running fluentd in production, consider using [td-agent](//docs.treasure-data.com/articles/td-agent), the enterprise version of Fluentd packaged and maintained by [Treasure Data, Inc.](//www.treasure-data.com).
