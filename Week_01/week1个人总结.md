### 1.用 add first 或 add last 这套新的 API 改写 Deque 的代码

```java
Deque<String> deque = new LinkedList<>();

// 双端队列 实现 队列 先进先出
deque.addLast("a");
deque.addLast("b");
deque.addLast("c");
System.out.println(deque);

String peek = deque.peekFirst();
System.out.println(peek);
System.out.println(deque);

while (deque.size() > 0) {
    System.out.println(deque.pollFirst());
}

System.out.println(deque);

// 双端队列 实现 栈 后进先出
deque.addFirst("a");
deque.addFirst("b");
deque.addFirst("c");
System.out.println(deque);

String peek2 = deque.peekFirst();
System.out.println(peek2);
System.out.println(deque);

while (deque.size() > 0) {
    System.out.println(deque.pollFirst());
}

System.out.println(deque);

```

### 2.分析 Queue 和 Priority Queue 的源码

#### 2.1 Queue

Java中的`Queue`是一个接口，继承了`Collection`接口

```
// 添加一个元素，添加失败会抛出异常 IllegalStateException
boolean add(E e);

// 添加一个元素，添加失败返回false
boolean offer(E e);

// 删除队首元素，删除失败会抛出异常 NoSuchElementException
E remove();

// 删除队首元素，删除失败返回null
E poll();

// 查看队首元素，不存在则抛出异常 NoSuchElementException
E element();

// 查看队首元素，不存在返回null
E peek();

```

#### 2.2 Priority Queue

Java中的`PriorityQueue`继承了`AbstractQueue`

**入队方法**`offer(e)`

```
public boolean offer(E e) {
    if (e == null)
        throw new NullPointerException();
    // 修改次数加一，无论添加还是删除，这个值都会加一
    modCount++;
    int i = size;
    // 判断是否需要扩容，默认数组大小是11
    // 扩容会比较是否原始大小是否小于64，如果小于扩容一倍大小，否则扩容50%
    if (i >= queue.length)
        grow(i + 1);
    siftUp(i, e);
    // 大小加一
    size = i + 1;
    return true;
}

// 扩容
private void grow(int minCapacity) {
    int oldCapacity = queue.length;
    // Double size if small; else grow by 50%
    int newCapacity = oldCapacity + ((oldCapacity < 64) ? (oldCapacity + 2) : (oldCapacity >> 1));
    // overflow-conscious code
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    queue = Arrays.copyOf(queue, newCapacity);
}

// 将元素x插入到最小二叉堆的k位置
private void siftUp(int k, E x) {
    if (comparator != null)
        siftUpUsingComparator(k, x, queue, comparator);
    else
        siftUpComparable(k, x, queue);
}

private static <T> void siftUpComparable(int k, T x, Object[] es) {
    Comparable<? super T> key = (Comparable<? super T>) x;
    while (k > 0) {
        // 找到插入位置的父结点
        int parent = (k - 1) >>> 1;
        Object e = es[parent];
        // 如果插入结点比父结点大，则不用调整位置
        if (key.compareTo((T) e) >= 0)
            break;
        // 如果插入数据比父结点大，将插入结点跟父结点数据互换
        es[k] = e;
        k = parent;
    }
    // 最后找到了调整后的位置
    es[k] = key;
}

// 和siftUpComparable相同，只不过使用传入的比较器
private static <T> void siftUpUsingComparator(int k, T x, Object[] es, Comparator<? super T> cmp) {
    while (k > 0) {
        int parent = (k - 1) >>> 1;
        Object e = es[parent];
        if (cmp.compare(x, (T) e) >= 0)
            break;
        es[k] = e;
        k = parent;
    }
    es[k] = x;
}

```

**出队方法**`poll()`

```
public E poll() {
    final Object[] es;
    final E result;

    if ((result = (E) ((es = queue)[0])) != null) {
        modCount++;
        final int n;
        // 先减少堆大小，把最后位的数据缓存在x里
        final E x = (E) es[(n = --size)];
        es[n] = null;
        if (n > 0) {
            // 把x跟0位置及其子结点循环比较，直到x插入到了合适位置
            final Comparator<? super E> cmp;
            if ((cmp = comparator) == null)
                siftDownComparable(0, x, es, n);
            else
                siftDownUsingComparator(0, x, es, n, cmp);
        }
    }
    return result;
}

private static <T> void siftDownComparable(int k, T x, Object[] es, int n) {
    // assert n > 0;
    Comparable<? super T> key = (Comparable<? super T>)x;
    int half = n >>> 1;           // loop while a non-leaf
    while (k < half) {
        int child = (k << 1) + 1; // assume left child is least
        Object c = es[child];
        int right = child + 1;
        if (right < n && ((Comparable<? super T>) c).compareTo((T) es[right]) > 0)
            c = es[child = right];
        if (key.compareTo((T) c) <= 0)
            break;
        es[k] = c;
        k = child;
    }
    es[k] = key;
}

private static <T> void siftDownUsingComparator(
    int k, T x, Object[] es, int n, Comparator<? super T> cmp) {
    // assert n > 0;
    int half = n >>> 1;
    while (k < half) {
        int child = (k << 1) + 1;
        Object c = es[child];
        int right = child + 1;
        if (right < n && cmp.compare((T) c, (T) es[right]) > 0)
            c = es[child = right];
        if (cmp.compare(x, (T) c) <= 0)
            break;
        es[k] = c;
        k = child;
    }
    es[k] = x;
}

```

### 