---
layout: post
title: Java调用Python， 中文乱码
author: "gebaocai"
header-style: text
lang: zh
tags: [乱码]
---

Java调用Python可以写成如下形式：
------
```
// 调用Python代码
Process process = Runtime.getRuntime().exec("python python_code.py");

// 设置输入流的编码格式
InputStreamReader isr = new InputStreamReader(process.getInputStream(), "gb2312");
BufferedReader input = new BufferedReader(isr);

// 读取输出
String line;
while ((line = input.readLine()) != null) {
    System.out.println(line);
}

// 关闭流
input.close();
isr.close();
process.destroy();
```

Python代码
```
# python_code.py

import sys

def main():
    print("中文")

if __name__ == "__main__":
    main()
```

但是如果python需要传入一个中文参数呢？
------

#### 把上述代码简单修改:

Java端：
```
String arg = "我是中文"
// 调用Python代码
Process process = Runtime.getRuntime().exec("python python_code.py "+arg);

// 设置输入流的编码格式
InputStreamReader isr = new InputStreamReader(process.getInputStream(), "gb2312");
BufferedReader input = new BufferedReader(isr);

// 读取输出
String line;
while ((line = input.readLine()) != null) {
    System.out.println(line);
}

// 关闭流
input.close();
isr.close();
process.destroy();

```

Python端:
```
# python_code.py

import sys

def main():
    print(input(""))

if __name__ == "__main__":
    main()
```

在cmd执行
```
java JavaCallPython2.java
输出:
python python_code.py 鎴戞槸涓枃

java -Dfile.encoding=UTF-8 JavaCallPython2.java
输出:
python python_code.py 我是中文
```

#### 问题在哪？ 

很明显编码的问题， 在windows的cmd里， 默认编码是gb2312。

#### 如何解决?

可以在java里获取当前的编码， 然后做处理：
```
String arg = "我是中文";
String encoing = System.getProperty("file.encoding");
arg = new String(arg.getBytes(encoing), "utf-8");
```

这样程序就可以通过编码自动适配
在cmd执行
```
java JavaCallPython2.java
输出:
python python_code.py 我是中文
```

