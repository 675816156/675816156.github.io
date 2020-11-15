---
title: java设计模式-工厂模式
date: YYYY-MM-DD HH:mm:ss
categories: 
- JAVA开发
tags:
- java设计模式-工厂模式
---
```java
interface Fruit {
	public abstract void eat();
}

class Apple implements Fruit {
	public void eat() {
		System.out.println("*** eat Apple ***");
	}
}

class Orange implements Fruit {
	public void eat() {
		System.out.println("*** eat Orange ***");
	}
}

class Factory {
	public Fruit getInstance(String s) {
		if ("Apple".equals(s)) {
			return new Apple();
		} else if ("Orange".equals(s)) {
			return new Orange();
		} else {
			return null;
		}
	}
}
/* 好处是这部分代码不用修改 */
public class test {
	public static void main(String args[]) {
		Fruit f = null;
		f = new Factory().getInstance(args[0]);
		if (f != null) {
			f.eat();
		}
	}
}
```