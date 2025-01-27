# 面试题

## 1.[向数组的中间插入数据，ArrayList和LinkedList哪个快](https://www.cnblogs.com/liqiu/archive/2013/05/07/3065203.html)（涂鸦智能）

ArrayList

插入位置越往中间，LinkedList效率越低，**因为它遍历获取插入位置是从两头往中间搜，index越往中间遍历越久**，因此ArrayList的插入效率可能会比LinkedList高。

插入位置的选取对LinkedList有很大的影响，因为LinkedList在插入时需要向移动指针到指定节点， 才能开始插入,，一旦要插入的位置比较远，LinkedList就需要一步一步的移动指针， 直到移动到插入位置，这就解释了， 为什么节点值越大， 时间越长， 因为指针移动需要时间。

而ArrayList是数据结构， **可以根据下标直接获得位置， 这就省去了查找特定节点的时间**，所以对ArrayList的影响不是特别大。

Demo  

```java
public class ArrayList_LinkedList_compare_demo {
    public static void main(String[] args){
        arrayAndLink();
    }
    public static void  arrayAndLink(){
        List<String> arrayList = new ArrayList<String>();
        List<String> linkedList = new LinkedList<String>();
        //生产数据
            for(int i = 0; i < 10000; i++){
                arrayList.add("arrayList" + i);
                linkedList.add("linkedList" + i);
            }
        //标记开始时间
        long startTime1 = System.currentTimeMillis();
        //在list中间插入数据
        for(int i = 0; i < 10000; i++){
            arrayList.add((5000 + i), "arrayList");

        }
        //标记结束时间
        long endTime1 = System.currentTimeMillis();
        System.out.println("arrayList time ---- " + (endTime1 - startTime1));

        //标记开始时间
        long startTime2 = System.currentTimeMillis();
        //在link中间插入数据
        for(int i = 0; i < 10000; i ++){
            linkedList.add((5000+i), "linkedList");
        }
        //标记结束时间
        long endTime2 = System.currentTimeMillis();
        System.out.println("linkedList time -----"+(endTime2 - startTime2));

    }
}
```

插入中间结果

```java
arrayList time ---- 27
linkedList time -----687
```

ArrayList明显快于LinkedList

插入开头结果

```java
arrayList time ---- 27
linkedList time -----141
```

ArrayList依然快于LinkedList

## 2.ArrayList怎么扩容（中兴）

添加元素时使用 ensureCapacityInternal() 方法来保证容量足够，如果不够时，需要使用 grow() 方法进行扩容，新容量的大小为 oldCapacity + (oldCapacity >> 1)，oldCapacity >> 1 为位运算的右移操作，右移一位相当于除以2，也就是**旧容量的 1.5 倍**。

获取newCapacity后再对newCapacity的大小进行判断，**如果仍然小于minCapacity，则直接让newCapacity 等于minCapacity**，而不再计算1.5倍的扩容。**实际扩容可能大于1.5倍**。

**①、当通过 ArrayList() 构造一个空集合，初始长度是为0的，第 1 次添加元素，会创建一个长度为10的数组，并将该元素赋值到数组的第一个位置。**

　　**②、第 2 次添加元素，集合不为空，而且由于集合的长度size+1是小于数组的长度10，所以直接添加元素到数组的第二个位置，不用扩容。**

　　**③、第 11 次添加元素，此时 size+1 = 11，而数组长度是10，这时候创建一个长度为10+10\*0.5 = 15 的数组（扩容1.5倍），然后将原数组元素引用拷贝到新数组。并将第 11 次添加的元素赋值到新数组下标为10的位置。**

　　**④、第 Integer.MAX_VALUE - 8 = 2147483639，然后 2147483639%1.5=1431655759（这个数是要进行扩容） 次添加元素，为了防止溢出，此时会直接创建一个 1431655759+1 大小的数组，这样一直，每次添加一个元素，都只扩大一个范围。**

　　**⑤、第 Integer.MAX_VALUE - 7 次添加元素时，创建一个大小为 Integer.MAX_VALUE 的数组，在进行元素添加。**

　　**⑥、第 Integer.MAX_VALUE + 1 次添加元素时，抛出 OutOfMemoryError 异常。**

　　**注意：能向集合中添加 null 的，因为数组可以有 null 值存在。**

https://blog.csdn.net/zymx14/article/details/78324464

## 3. 在ArrayList的循环中删除元素，会不会出现问题？（蘑菇街）

倒序循环

```
方法一：普通for循环正序删除，删除过程中元素向左移动，不能删除重复的元素
方法二：普通for循环倒序删除，删除过程中元素向左移动，可以删除重复的元素
方法三：增强for循环删除，使用ArrayList的remove()方法删除，产生并发修改异常 ConcurrentModificationException
方法四：迭代器，使用ArrayList的remove()方法删除，产生并发修改异常 ConcurrentModificationException
方法五：迭代器，使用迭代器的remove()方法删除，可以删除重复的元素，但不推荐
```

```
针对普通for循环的错误写法，在遍历第一个字符串b时因为符合删除条件，所以将该元素从数组中删除，并且将后一个元素移动（也就是第二个字符串b）至当前位置，导致下一次循环遍历时后一个字符串b并没有遍历到，所以无法删除。针对这种情况可以倒序删除的方式来避免。
```

方法五不能保证两个变量修改的一致性，结果具有不确定性，所以不推荐这种方法。

https://www.jianshu.com/p/a7df6082ec00

## 4. fail-fast和fail-safe的区别是什么

快速失败：在用迭代器遍历一个集合对象时，如果遍**历过程中对集合对象的内容进行了修改（增加、删除、修改），则会抛出Concurrent Modification Exception。**

安全失败：采用安全失败机制的集合容器，在遍历时不是直接在集合内容上访问的，而是先复制原有集合内容，**在拷贝的集合上进行遍历**，在遍历过程中对原集合所作的修改并不能被迭代器检测到，所以不会触发Concurrent Modification Exception。

https://www.cnblogs.com/songanwei/p/9387745.html

# ArrayList

## 1.概览

**ArrayList 是一个用数组实现的集合，支持随机访问，元素有序且可以重复。**

实现了**List接口，继承了AbstractList**，底层是数组实现的，一般我们把它认为是可以自增扩容的数组。

实现了 **RandomAccess **接口，因此**支持随机访问**，这是理所当然的，因为 ArrayList 是基于数组实现的。

实现了**Serializable**接口，因此它**支持序列化**。

实现了**Cloneable**接口，能被**克隆**。

```java
public class ArrayList<E> extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

数组的**默认大小为 10**。

```java
private static final int DEFAULT_CAPACITY = 10;
```

![](https://raw.githubusercontent.com/wuqifan1098/picBed/master/ArrayList%E5%BA%95%E5%B1%82%E5%AE%9E%E7%8E%B0.png)

继承关系

```java
java.lang.Object
   	↳     java.util.AbstractCollection<E>
         ↳     java.util.AbstractList<E>
               ↳     java.util.ArrayList<E>
```

![](https://raw.githubusercontent.com/wuqifan1098/picBed/master/Arrylist%E4%B8%8ECollection%E5%85%B3%E7%B3%BB.jpg)
## 2.序列化

基于数组实现，保存元素的数组使用 **transient 修饰**，该关键字声明数组默认**不会被序列化**。ArrayList 具有动态扩容特性，因此保存元素的数组不一定都会被使用，那么就没必要全部进行序列化。ArrayList 重写了 writeObject() 和 readObject() 来控制只序列化数组中有元素填充那部分内容。

## 3.扩容

添加元素时使用 ensureCapacityInternal() 方法来保证容量足够，如果不够时，需要使用 grow() 方法进行扩容，新容量的大小为 oldCapacity + (oldCapacity >> 1)，oldCapacity >> 1 为位运算的右移操作，右移一位相当于除以2，也就是**旧容量的 1.5 倍**。

获取newCapacity后再对newCapacity的大小进行判断，**如果仍然小于minCapacity，则直接让newCapacity 等于minCapacity**，而不再计算1.5倍的扩容。**实际扩容可能大于1.5倍**。

扩容操作需要调用 Arrays.copyOf() 把**原数组整个复制到新数组**中，这个**操作代价很高**，因此最好在创建 ArrayList 对象时就指定大概的容量大小，减少扩容操作的次数。

```java
ArrayList构造方法初始化一个空数组

private static final int DEFAULT_CAPACITY = 10;
	transient Object[] elementData; // non-private to simplify nested class access
	private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
	public ArrayList() {
   	this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
	}	

调用add方法插入数据

// JDK 1.8
	private int size;
    public boolean add(E e) {
	ensureCapacityInternal(size + 1);  // Increments modCount!!
	elementData[size++] = e;
	return true;
	}
```

size变量是用来记录数组的元素总数。当使用add方法的时候**首先调用ensureCapacityInternal方法，传入size+1进去**，检查是否需要扩充elementData数组的大小。检查完毕之后再将e赋值给elementData数组 ，size再自增1。ensureCapacityInternal源码如下。
    
```java
// 判断数组是否越界
private void ensureCapacityInternal(int minCapacity) {
if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
    minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
}
ensureExplicitCapacity(minCapacity);
}

private void ensureExplicitCapacity(int minCapacity) {
modCount++;
// overflow-conscious code
if (minCapacity - elementData.length > 0)
    grow(minCapacity);
}
```

如果elementData数组为空，则在DEFAULT_CAPACITY和minCapacity（刚才的size+1）选取最大值，赋值给minCapacity。接着进入ensureExplicitCapacity方法，**如果需要的最小容量（minCapacity）比当前数组长度还要大，则进入grow方法扩增数组。**

```java
// 扩容
private void grow(int minCapacity) {
// overflow-conscious code
int oldCapacity = elementData.length;
int newCapacity = oldCapacity + (oldCapacity >> 1); // 1.5倍
if (newCapacity - minCapacity < 0)
    newCapacity = minCapacity;
if (newCapacity - MAX_ARRAY_SIZE > 0)
    newCapacity = hugeCapacity(minCapacity);
// minCapacity is usually close to size, so this is a win:
elementData = Arrays.copyOf(elementData, newCapacity);
}
```

接下来重点讲讲这个grow方法，先通过elementData.length获取当前数组的容量大小赋值给oldCapacity。接着oldCapacity + (oldCapacity >> 1)，其中(oldCapacity >> 1)表示oldCapacity右移1位，它的值可以理解为oldCapacity除以2（至于为什么请百度二进制、移位操作等关键字）。总结起来就是在oldCapacity基础上扩增50%容量再赋值给newCapacity。最后使用Arrays工具类扩增elementData数组。

```java
public static <T> T[] copyOf(T[] original, int newLength) {
    return (T[]) copyOf(original, newLength, original.getClass());
}
public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
    @SuppressWarnings("unchecked")
    T[] copy = ((Object)newType == (Object)Object[].class)
        ? (T[]) new Object[newLength]
        : (T[]) Array.newInstance(newType.getComponentType(), newLength);
    System.arraycopy(original, 0, copy, 0,
                     Math.min(original.length, newLength));
    return copy;
}
```

整个核心就是System.arraycopy方法，**将original数组从的所有数据复制到copy数组中，返回给调用方法**。整个add方法添加数据流程就走完了。

```java
private static int hugeCapacity(int minCapacity) {
if (minCapacity < 0) // overflow
    throw new OutOfMemoryError();
return (minCapacity > MAX_ARRAY_SIZE) ? Integer.MAX_VALUE : MAX_ARRAY_SIZE;
}
```

### 总结

对于 ArrayList 集合添加元素，我们总结一下：

　　**①、当通过 ArrayList() 构造一个空集合，初始长度是为0的，第 1 次添加元素，会创建一个长度为10的数组，并将该元素赋值到数组的第一个位置。**

　　**②、第 2 次添加元素，集合不为空，而且由于集合的长度size+1是小于数组的长度10，所以直接添加元素到数组的第二个位置，不用扩容。**

　　**③、第 11 次添加元素，此时 size+1 = 11，而数组长度是10，这时候创建一个长度为10+10\*0.5 = 15 的数组（扩容1.5倍），然后将原数组元素引用拷贝到新数组。并将第 11 次添加的元素赋值到新数组下标为10的位置。**

　　**④、第 Integer.MAX_VALUE - 8 = 2147483639，然后 2147483639%1.5=1431655759（这个数是要进行扩容） 次添加元素，为了防止溢出，此时会直接创建一个 1431655759+1 大小的数组，这样一直，每次添加一个元素，都只扩大一个范围。**

　　**⑤、第 Integer.MAX_VALUE - 7 次添加元素时，创建一个大小为 Integer.MAX_VALUE 的数组，在进行元素添加。**

　　**⑥、第 Integer.MAX_VALUE + 1 次添加元素时，抛出 OutOfMemoryError 异常。**

　　**注意：能向集合中添加 null 的，因为数组可以有 null 值存在。**

如果是指定位置插入数据是怎样的呢？

```java
public void add(int index, E element) {
    if (index > size || index < 0)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    ensureCapacityInternal(size + 1); // Increments modCount!!
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);
    elementData[index] = element;
    size++;
}
```



index下标表示插入位置，**先检查下标是否越界，如果是则抛出异常。**之后调用ensureCapacityInternal方法判断是否需要扩增数组，这个方法上面已经分析过，略。最重要的是System.arraycopy方法，为了空出index的位置，将elementData数组从index开始到（size - index）位置，都后移1位。此时数组的index位置已经空出来了，最后再将新的元素放进去，完成数据插入操作。

## 4.删除元素

```java
public E remove(int index) {
    rangeCheck(index);

    modCount++;
    E oldValue = elementData(index);

    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work

    return oldValue;
}

public boolean remove(Object o) {
    if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                fastRemove(index);
                return true;
            }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}

private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work
}
```

根据下标删除的 remove() 方法，大致的步骤如下：

- 检查有没有下标越界，就是检查一下当前的下标有没有大于等于数组的长度
- 列表被修改（add和remove操作）的次数加1
- 保存要删除的值
- 计算移动的元素数量
- 删除位置后面的元素向左移动，这里是用数组拷贝实现的
- 将最后一个位置引用设为 null，使垃圾回收器回收这块内存
- 返回删除元素的值

根据元素删除的 remove() 方法，大致的步骤如下：

- 元素值分为null和非null值
- 循环遍历判等
- 调用 fastRemove(i) 函数
  - 修改次数加1
  - 计算移动的元素数量
  - 数组拷贝实现元素向左移动
  - 将最后一个位置引用设为 null
  - 返回 fase
- 返回 true

## 5.查找元素

```java
public E get(int index) {
    if (index >= size)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    return (E) elementData[index];
}
```

这是一个很简单的方法，相信很多人都看得懂，但是仔细想深一层为什么数组通过一个下标就能够查找出元素？**我们在实例化ArrayList的时候，elementData已经完成了初始化。**此时JVM虚拟机中，Java堆中则为elementData数组对象开辟一片连续的内存空间，**虚拟机栈则存储了elementData数组的引用声明，并且引用指向了Java堆的数组首地址。**因此在内存中可以通过：首地址+（元素存储单元×数组下标）=元素地址，快速的寻找对应下标的元素值。

## 6.Fail-Fast&Fail-Safe

### Fail-Fast

即快速失败机制，是java集合(Collection)中的一种**错误检测机制**。当在迭代集合的过程中该**集合在结构上发生改变的时候**，就有可能会发生fail-fast，即**抛出ConcurrentModificationException异常**。fail-fast机制并不保证在不同步的修改下一定会抛出异常，它只是尽最大努力去抛出，所以这种机制一般仅用于检测bug。

原理：迭代器在遍历时直接访问集合中的内容，并且在遍历过程中使用一个 modCount 变量。集合在被遍历期间如果内容发生变化，就会改变modCount的值。每**当迭代器使用hashNext()/next()遍历下一个元素之前，都会检测modCount变量是否为expectedmodCount值，是的话就返回遍历；否则抛出异常，终止遍历。**

场景：java.util包下的集合类都是快速失败的，不能在多线程下发生并发修改（迭代过程中被修改）。

以下两种情况下抛出ConcurrentModificationException

（1）单线程环境

集合被创建后，在遍历它的过程中修改了结构。

注意 remove()方法会让expectModcount和modcount 相等，所以是不会抛出这个异常。

（2）多线程环境

当一个线程在遍历这个集合，而另一个线程对这个集合的结构进行了修改。

如何监测的？

迭代器在遍历过程中是直接访问内部数据的，因此内部的数据在遍历的过程中无法被修改。为了保证不被修改，迭代器内部维护了一个标记 “mode” ，当集合结构改变（添加删除或者修改），标记"mode"会被修改，而迭代器每次的hasNext()和next()方法都会检查该"mode"是否被改变，当检测到被修改时，抛出Concurrent Modification Exception。

```java
private class Itr implements Iterator<E> {
        int cursor;
        int lastRet = -1;
        int expectedModCount = ArrayList.this.modCount;
 
        public boolean hasNext() {
            return (this.cursor != ArrayList.this.size);
        }
 
        public E next() {
            checkForComodification();
            /** 省略此处代码 */
        }
 
        public void remove() {
            if (this.lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();
            /** 省略此处代码 */
        }
 
        final void checkForComodification() {
            if (ArrayList.this.modCount == this.expectedModCount)
                return;
            throw new ConcurrentModificationException();
        }
    }
```

### Fail-Safe

任何对集合结构的修改都会在一个复制的集合上进行修改，因此不会抛出ConcurrentModificationException

  缺点：基于拷贝内容的优点是避免了Concurrent Modification Exception，但同样地，迭代器并不能访问到修改后的内容，即：迭代器遍历的是开始遍历那一刻拿到的集合拷贝，在遍历期间原集合发生的修改迭代器是不知道的。

  场景：java.util.concurrent包下的容器都是安全失败，可以在多线程下并发使用，并发修改。

fail-safe机制有两个问题

（1）需要复制集合，产生大量的无效对象，开销大

（2）无法保证读取的数据是目前原始数据结构中的数据。



## 7.SubList

```java
   public List<E> subList(int fromIndex, int toIndex) {
        subListRangeCheck(fromIndex, toIndex, size);
        return new SubList(this, 0, fromIndex, toIndex);
    }
```

作用是返回从 fromIndex(包括) 开始的下标，到 toIndex(不包括) 结束的下标之间的元素**视图**。如下：

```java
ArrayList<String> list = new ArrayList<>();
 list.add("a");
 list.add("b");
 list.add("c");
 
 List<String> subList = list.subList(0, 1);
 for(String str : subList){
     System.out.print(str + " ");//a
 }
```

**返回的是原集合的视图，也就是说，如果对 subList 出来的集合进行修改或新增操作，那么原始集合也会发生同样的操作。**

## 8.Demo

```java
ArrayList<String> list = new ArrayList();
for(int i = 1; i <= 11; i++){
    list.add("value" + i);
}
Integer length = getCapacity(list);
int size = list.size();
System.out.printIn("容量： " + length);
System.out.printIn("大小 " + size);
```

