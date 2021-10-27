> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/hanxt/javacrazy/1825288)

> 当你用十几行代码，完成别人两三行就搞定的问题，你不觉得自己有问题么？ 当别人面对一个老问题，用一个新的方法解决了，你不想一探究竟么？ 当别人完成该工作用了一小时，你用了一天，你不想多休息休息么？ 期望能够让读者攫取到一些有价值的，能够提高工作效率的东西。让你的写代码，一看上去就透漏着一种高级的味道；让你的设计，一看上去就经过专业的学习与训练。

本文中为大家介绍使用 java8 Stream API 逐行读取文件，以及根据某些条件过滤文件内容

1. Java 8 逐行读取文件
----------------

在此示例中，我将按行读取文件内容并在控制台打印输出。

```
Path filePath = Paths.get("c:/temp", "data.txt");
 
//try-with-resources语法,不用手动的编码关闭流
try (Stream<String> lines = Files.lines( filePath )) 
{
    lines.forEach(System.out::println);
} 
catch (IOException e) 
{
    e.printStackTrace();//只是测试用例，生产环境下不要这样做异常处理
}


```

复制

上面的程序输出将在控制台中逐行打印文件的内容。

```
Never
store
password
except
in mind.


```

复制

2.Java 8 读取文件–过滤行
-----------------

在此示例中，我们将文件内容读取为 Stream。然后，我们将过滤其中包含单词 "password" 的所有行。

```
Path filePath = Paths.get("c:/temp", "data.txt");
 
try (Stream<String> lines = Files.lines(filePath)){
 
     List<String> filteredLines = lines
                    .filter(s -> s.contains("password"))
                    .collect(Collectors.toList());
      
     filteredLines.forEach(System.out::println);
 
} catch (IOException e) {
    e.printStackTrace();//只是测试用例，生产环境下不要这样做异常处理
}


```

复制

程序输出。

```
password


```

复制

我们将读取给定文件的内容，并检查是否有任何一行包含 "password" 然后将其打印出来。

3.Java 7 –使用 FileReader 读取文件
----------------------------

Java 7 之前的版本，我们可以使用 FileReader 方式进行逐行读取文件。

```
private static void readLinesUsingFileReader() throws IOException 
{
    File file = new File("c:/temp/data.txt");
 
    FileReader fr = new FileReader(file);
    BufferedReader br = new BufferedReader(fr);
 
    String line;
    while((line = br.readLine()) != null)
    {
        if(line.contains("password")){
            System.out.println(line);
        }
    }
    br.close();
    fr.close();
}


```

复制