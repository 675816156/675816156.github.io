---
title: java特性-java内部类
date: YYYY-MM-DD HH:mm:ss
categories: 
- JAVA开发
tags:
- java内部类
---
```java
class outer {
	public void print() {
		System.out.println("outer class " + (this instanceof outer));
	}

	public class inner {
		public void print() {
			System.out.println("Inner class " + (this instanceof inner));
		}
	}
}

public class test {
	public static void main(String args[]) {
		outer a = new outer();
		outer.inner b = a.new inner();
		a.print();
		b.print();
	}
}
```

***注意：*** 在方法内的内部类想访问方法的参数时，参数必须是final修饰。

```java
class outer {
	public void print(final int num) {
		System.out.println("outer class ");
		class inner {
			public void print() {
				System.out.println("Inner class " + num);
			}
		}
		new inner().print();
	}
}

public class test {
	public static void main(String args[]) {
		outer a = new outer();
		a.print(123);
	}
}
```