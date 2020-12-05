---
title: java特性-可变参数和foreach循环
date: YYYY-MM-DD HH:mm:ss
categories: 
- JAVA开发
tags:
- java 可变参数实例：
- java foreach
---
# java 可变参数实例：

```java
public class test {
	public static void func(int ... args) {
		for (int i = 0; i < args.length; ++i) {
			System.out.println("args[" + i + "] = " + args[i]);
		}
	}

	public static void main(String args[]) {
		int a[] = {1, 3, 4, 6};
		func(a);
	}
}
```

# java foreach

```java
public class test {
	public static void func(int ... args) {
		for (int a : args) {
			System.out.println("a = " + a);
		}
	}

	public static void main(String args[]) {
		int a[] = {1, 3, 4, 6};
		func(a);
	}
}
```