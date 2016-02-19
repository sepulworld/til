# Distcp hftp Partition Size Limit 

If you have a very large multi terabyte source data partition that you want to copy using [hftp](https://hadoop.apache.org/docs/r1.2.1/hftp.html) 
you may run into some vague errors prior to the map tasks being submitted.

Instead of using:

```bash
hadoop distcp -overwrite hftp://namenode.example.host:50070/path/to/huge/data/paritition/ /local/hdfs/directory/
```

swap out the hftp:// protocol for the newer webhdfs:// protocol

```bash
hadoop distcp -overwrite webhdfs://namenode.example.host:50070/path/to/huge/data/paritition/ /local/hdfs/directory/
```
