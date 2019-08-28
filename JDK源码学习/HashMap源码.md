以jdk8中的源码为例子



**HashMap中的hash()方法作用：**

减少hash碰撞

设HashMap中数组长度为length，由于需要根据hashcode&(length-1)来定位数组下标，只是用到了HashCode的低几位数

```java
// 将hashcode的高16位和低16位进行^运算，充分运用Hashcode来定位在数组中的位置
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```





