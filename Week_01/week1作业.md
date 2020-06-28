### 1.删除排序数组中的重复项

```
/*要求删除重复元素，实际上是将不重复的元素移到数组的左侧。
考虑用 2 个指针，一个在前记作 p，一个在后记作 q，算法流程如下：
1.比较 p 和 q 位置的元素是否相等。
2.如果相等，q 后移 1 位
3.如果不相等，将 q 位置的元素复制到 p+1 位置上，p 后移一位，q 后移 1 位
4.重复上述过程，直到 q 等于数组长度。
5.返回 p + 1，即为新数组长度。

注：若数组中没有重复元素，按照上面的方法，每次比较时 nums[p] 都不等于 nums[q]，
因此就会将 q 指向的元素原地复制一遍，这个操作其实是不必要的。
可以加一个小判断，当 q - p > 1 时，才进行复制。
*/

class Solution {
	public int removeDuplicates(int[] nums) {
		//判空条件
		if(nums ==null || nums.length == 0) return 0 ;
		//定义两个指针，p在前，q在后一个
		int p = 0 ;
		int q = 1 ;
		while(q < nums.length) {
			if (nums[p] != nums[q]) {
				if (q - p > 1) {
					nums[p + 1] = nums[q] ;
				}
				p++ ;
			}
			q++ ;
		}
		return p + 1 ;
	}
}
```

### 2.旋转数组

```
//1.暴力
class Solution {
    public void rotate(int[] nums, int k) {
        int temp ;
        for (int i = 0 ; i < k ; i++) {
            for (int j = nums.length - 1 ; j > 0 ; j--) {
                temp = nums[j] ;
                nums[j] = nums[j - 1] ;
                nums[j - 1] = temp ;
            }
        } 
    }

//方法 2：使用额外的数组
/*我们可以用一个额外的数组来将每个元素放到正确的位置上，
也就是原本数组里下标为 i的我们把它放到 (i+k)%数组长度 的位置。
然后把新的数组拷贝到原数组中*/。
public class Solution {
    public void rotate(int[] nums, int k) {
        int[] a = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            a[(i + k) % nums.length] = nums[i];
        }
        for (int i = 0; i < nums.length; i++) {
            nums[i] = a[i];
        }
    }
}
/*复杂度分析

时间复杂度： O(n)
空间复杂度： O(n)。另一个数组需要原数组长度的空间。不符合题意*/


//方法3方法 4：使用反转
/*旋转数组 k 次， k%n 个尾部元素会被移动到头部，剩下的元素会被向后移动。
首先将所有元素反转，然后反转前 k 个元素，再反转后面 n-k个元素，就能得到想要的结果。
public class Solution {
    public void rotate(int[] nums, int k) {
        k %= nums.length;
        reverse(nums, 0, nums.length - 1);
        reverse(nums, 0, k - 1);
        reverse(nums, k, nums.length - 1);
    }
    public void reverse(int[] nums, int start, int end) {
        while (start < end) {
            //先保存start的值，再交换start和end，再start++,end--进行下一轮
            int temp = nums[start];
            nums[start] = nums[end];
            nums[end] = temp;
            start++;
            end--;
        }
    }
}
/*复杂度分析

时间复杂度：O(n)。 n个元素被反转了总共 3 次。
空间复杂度：O(1) 。 没有使用额外的空间。*/

//方法4 环状替代---最大公约数
/*
其实是这样的，对于一个长度为 nn 的数组，整体移动 kk 个位置
当 n 和 k 的最大公约数 等于 1 的时候：1 次遍历就可以完成交换；比如 n = 5, k = 3
当 n 和 k 的最大公约数 不等于 1 的时候：1 次遍历是无法完成的所有元素归位，需要 m (最大公约数) 次
那么如何判断所有的分组都执行归位了呢？ 可以有两种方法来控制
第一种：我们就用最大公约数 m 来控制外循环，代表总共有 m 轮循环
第二种：由于n个元素归位需要n次交换，所以我们定义一个count代表交换次数，当 count = n 时完成
*/
class Solution {
   public void rotate(int[] nums, int k) {
        k=k%nums.length;
        int count = 0;  //移动总次数
         //nums.leng为偶数时，会形成多个换环 1 2 3 4（1 3 1,2 4 2,3,1,3） ，
         //为奇数时则只有一个环 1 2 3 4 5 （1 3 5 2 4 1），i=0不会增加
        for(int i=0;count<nums.length;i++){
            int current=i;//当前坐标
            int pre=nums[i];//前一个坐标
            do{
                int next=(current+k)%nums.length;//下一个坐标
                //交换
                int temp=nums[next];
                nums[next]=pre;
                pre=temp;
                current=next;
                count++;
            }while(current!=i );
            
        }
    }
}
```

**注：******方法4环装替代不是很看的懂，希望老师空的时候能帮忙****

### 3.合并两个有序数组

```
  // 三指针 指针一p1、nums1有效元素尾部；指针二p2、nums2尾部；指针三p3、最终数组尾部
  // 1.当，p1>=0时，nums[p1],nums[p2]对比
  // 1.1 nums[p1]大，将nums[p1]放入p3位置。p1--,p3--
  // 1.2 nums[p2]大于等于nums[p1]，将nums[p2]放入p3位置。p2--,p3--
  // 2.当，p1<0时，将nums[p2]放入p3位置。p2--,p3--
  // 循环结束条件：p2<0 
class Solution {
  public void merge(int[] nums1, int m, int[] nums2, int n) {
        int p1=m-1,p2=n-1,p3=m+n-1;
        while(p2 >= 0){
            if(p1 >= 0 && nums1[p1] > nums2[p2]){
                nums1[p3--] = nums1[p1--];
            } else {
                nums1[p3--] = nums2[p2--];
              }
        }
  }
}
```

### 4.合并两个有序链表

```java
//法一：迭代
class Solution {
	public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
		ListNode dummyHead = new ListNode(-1) ;
		ListNode tail = dummyHead ;
		while (l1 != null && l2 != null) {
			if (l1.val < l2.val) {
				tail.next = l1 ;
				l1 = l1.next ;
			}
			else {
				tail.next = l2 ;
				l2 = l2.next ;
			}
			tail = tail.next;
		}
		tail.next = l1 ==null ? l2 : l1 ;  //然后把那个不为空的链表挂到新的链表中
		return dummyHead.next ;
	}
}

//法二：递归
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        }
        else if (l2 == null) {
            return l1;
        }
        else if (l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        }
        else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }

    }
}
```

### 5.两数之和

```
//两数之和 暴力解法 ： hashset法未学
//二重循环
class Solution {
	public int[] twoSum (int[] nums , int target) {
		for (int i = 0 ; i < nums.length - 1 ; i++){
			for (int j = i +1 ; j < nums.length ; j++) {
				if (nums[i] + nums[j] == target) {
					a[0] = i ;
					a[1] = j ;
					return a ;
				}
			}
		}
	}
}


//哈希映射
/*思路
标签：哈希映射
1.这道题本身如果通过暴力遍历的话也是很容易解决的，时间复杂度在 O(n2)
2.由于哈希查找的时间复杂度为 O(1)，所以可以利用哈希容器 map 降低时间复杂度
3.遍历数组 nums，i 为当前下标，每个值都判断map中是否存在 target-nums[i] 的 key 值--下标
4.如果存在则找到了两个值，如果不存在则将当前的 (nums[i],i) 存入 map 中，继续遍历直到找到为止
5.如果最终都没有结果则抛出异常
时间复杂度：O(n)*/

class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < nums.length; i++) {
            if(map.containsKey(target - nums[i])) {
                return new int[] {map.get(target-nums[i]),i};
            }
            map.put(nums[i], i);
        }
        throw new IllegalArgumentException("No two sum solution");
    }
}

```

**注：hashmap,hashset自己看了还不是很会**

### 6.移动零

```
class Solution {
    public void moveZeroes(int[] nums) {
        int j = 0 ;  //记录非零的下标
        for ( int i = 0 ; i < nums.length ; ++i ) {
            if (nums[i] != 0) {
                nums[j] = nums[i] ; 
                if (i != j) {
                    nums[i] = 0 ;
                }
                j++ ;
            }
        }
    }
}
```



### 7.加一

```
class Solution {
	public int [] plusOne (int [] digits){
		for (int i = digits.length - 1 ; i >= 0 ; i--) {
			digits[i]++ ;
			digits[i] %= 10 ;
			if (digits[i] != 0) return digits ;
		}
		digits = new int [digits.length + 1] ;
		digits[0] = 1 ;
		return digits ;
	}
}
```



### 8.设计循环双端队列

```
class MyCircularDeque {
    int size;
    int k;
    DoubleListNode head;
    DoubleListNode tail;
    /** Initialize your data structure here. Set the size of the deque to be k. */
    public MyCircularDeque(int k) {
        head = new DoubleListNode(-1);
        tail = new DoubleListNode(-1);
        head.pre = tail;
        tail.next = head;
        this.k = k;
        this.size = 0;
    }
    
    /** Adds an item at the front of Deque. Return true if the operation is successful. */
    public boolean insertFront(int value) {
        if (size == k)
            return false;
        DoubleListNode node = new DoubleListNode(value);
        node.next = head;
        node.pre = head.pre;
        head.pre.next = node;
        head.pre = node;
        size++;
        return true;
    }
    
    /** Adds an item at the rear of Deque. Return true if the operation is successful. */
    public boolean insertLast(int value) {
        if (size == k)
            return false;
        DoubleListNode node = new DoubleListNode(value);
        node.next = tail.next;
        tail.next.pre = node;
        tail.next = node;
        node.pre = tail;
        size++;
        return true;
    }
    
    /** Deletes an item from the front of Deque. Return true if the operation is successful. */
    public boolean deleteFront() {
        if (size == 0)
            return false;
        head.pre.pre.next = head;
        head.pre = head.pre.pre;
        size--;
        return true;
    }
    
    /** Deletes an item from the rear of Deque. Return true if the operation is successful. */
    public boolean deleteLast() {
        if (size == 0)
            return false;
        tail.next.next.pre = tail;
        tail.next = tail.next.next;
        size--;
        return true;
    }
    
    /** Get the front item from the deque. */
    public int getFront() {
        return head.pre.val;
    }
    
    /** Get the last item from the deque. */
    public int getRear() {
        return tail.next.val;
    }
    
    /** Checks whether the circular deque is empty or not. */
    public boolean isEmpty() {
        return size == 0;
    }
    
    /** Checks whether the circular deque is full or not. */
    public boolean isFull() {
        return size == k;
    }
}

class DoubleListNode {
    DoubleListNode pre;
    DoubleListNode next;
    int val;
    public DoubleListNode(int val) {
        this.val = val;
    }
}
```



## 本周小结：

老师的一些方法很实用，也在努力去学，但是最近才学java，有些吃力，继续努力。

本周刷了23道算法题，多种解法看的太多了，不知道记哪个好。