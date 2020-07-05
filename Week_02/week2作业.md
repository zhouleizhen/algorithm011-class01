### 1.有效字母的异位词

法一：排序
算法：
通过将字符串 s 的字母重新排列成 t 来生成变位词。因此，如果 T 是 S 的变位词，对两个字符串进行排序将产生两个相同的字符串。此外，如果 s 和 t 的长度不同，t 不能是 s 的变位词，我们可以提前返回。

```
class Solution {
    public boolean isAnagram(String s, String t) {
        if(s.length() != t.length()) {
            return false ;
        }
        char[] str1 = s.toCharArray() ;
        char[] str2 = t.toCharArray() ;
        Arrays.sort(str1) ;
        Arrays.sort(str2) ;
        return Arrays.equals(str1,str2) ;
    }
}
```

复杂度分析

时间复杂度：O(nlogn)，假设 n 是 s 的长度，排序成本 O(logn) 和比较两个字符串的成本 O(n)。排序时间占主导地位，总体时间复杂度为O(nlogn)。

空间复杂度：O(1)，空间取决于排序实现，如果使用 heapsort，通常需要 O(1) 辅助空间。注意，在 Java 中，toCharArray() 制作了一个字符串的拷贝，所以它花费 O(n)额外的空间，但是我们忽略了这一复杂性分析，因为：
这依赖于语言的细节。这取决于函数的设计方式。例如，可以将函数参数类型更改为 char[]。

方法二：哈希表
算法：

为了检查 t是否是 s 的重新排列，我们可以计算两个字符串中每个字母的出现次数并进行比较。因为 S 和 T都只包含 A-Z 的字母，所以一个简单的 26 位计数器表就足够了。
我们需要两个计数器数表进行比较吗？实际上不是，因为我们可以用一个计数器表计算 s字母的频率，用 t 减少计数器表中的每个字母的计数器，然后检查计数器是否回到零。

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if(s.length() != t.length()) {
            return false ;
        }
        int[] counter = new int[26] ;
        for(int i = 0 ; i < s.length() ; i++) {
            counter[s.charAt(i) - 'a']++ ;
            counter[t.charAt(i) - 'a']-- ;
        }
        for(int count : counter) {
            if(count != 0) {
                return false;
            } 
        }
         return true ;
    }
}
```

复杂度分析

时间复杂度：O(n)。时间复杂度为 O(n)因为访问计数器表是一个固定的时间操作。
空间复杂度：O(1)。尽管我们使用了额外的空间，但是空间的复杂性是 O(1)，因为无论 N 有多大，表的大小都保持不变。

进阶：
如果输入字符串包含 unicode 字符怎么办？你能否调整你的解法来应对这种情况？

解答：
使用哈希表而不是固定大小的计数器。想象一下，分配一个大的数组来适应整个 Unicode 字符范围，这个范围可能超过 100万。哈希表是一种更通用的解决方案，可以适应任何字符范围。



### 2.N叉树的前序遍历

方法1.递归

```
class Solution {
    public List<Integer> list = new ArrayList<>();
    public List<Integer> preorder(Node root) {
        if (root == null) {
            return list;
        }
        list.add(root.val);
        for(Node node: root.children) {
        	preorder(node);
        }      
        return list;
    }
}
```



方法2：迭代

```
class Solution {
    public List<Integer> preorder(Node root) {
        List<Integer> list = new ArrayList<>();
        if (root == null) return list;
        
        Stack<Node> stack = new Stack<>();
        stack.add(root);
        
        while (!stack.empty()) {
            root = stack.pop();
            list.add(root.val);
            for (int i = root.children.size() - 1; i >= 0; i--)
                stack.add(root.children.get(i));
        }
        
        return list;
    }
}
```



