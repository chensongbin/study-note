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





对于HashMap

jdk1.7中

- 数组元素是Entry<K, V>
- 出现hash冲突时，新元素加在链表头，
- resize()扩容操作，只在出现hash冲突时启用

jdk8中

- 每次put会判断size>threshold，如果大于，进行扩容
- 新元素加在链表尾，大于8转化为红黑树

并发操作时出现死循环的原因：









