## [687. 最长同值路径](https://leetcode-cn.com/problems/longest-univalue-path/)

官解详解。。。

```java
class Solution {
    int ans;
    public int longestUnivaluePath(TreeNode root) {
        ans = 0;
        arrowLength(root);
        return ans;
    }

    public int arrowLength(TreeNode node) {
        if (node == null) return 0;
        int left = arrowLength(node.left);
        int right = arrowLength(node.right);
        int arrowLeft = 0, arrowRight = 0;
        if (node.left != null && node.left.val == node.val) {
            arrowLeft = left + 1;
        }

        if (node.right != null && node.right.val == node.val) {
            arrowRight = right + 1;
        }

        ans = Math.max(ans, arrowLeft + arrowRight);
        return Math.max(arrowLeft, arrowRight);

    }
}

```

1、首先，递归函数arrowLength(TreeNode node )返回的并不是以node为根的树（子树）中的最长路径长度，而是以node为根（不考虑父节点）且包含node本身所在的**单侧**“最长“同值路径的长度（单侧即题解里提到的“箭头”，即只考虑左或者右其中一个分支，将在第二点中详细说明）。

所以如果当前节点为叶子节点，或者该节点与其左右孩子节点的值都不相等时，调用函数arrowLength返回的都是0。


```java
例如
      2   <<       
     / \                                
    3   3   <<                        
         \                                   
          3 
          
这里的根节点2的调用arrowLength结果为0，因为该值与其左右子节点的值都不相等，即这个2本身所在的单侧最长同值路径只包含它自己，
没有边，所以为0。

对以第二层右边这个3为根节点的子树 3 ,对这个根3调用arrowLength()的结果应该是1。代表当前这个1所处的单侧最长同值路径包含1条边.
                             \
                              3 
另外两个叶子节点3，返回值都是0。
          
上面这颗树，调用arrowLength返回的结果依次对应为

      0 => 左右子树值与当前值都不相等
     / \
    0   1  =>  arrowRight = 0 + 1
         \
          0  => 只考虑以其为根的子树中它所在的最长路径，不考虑父节点，所以也为0。
          
          
再来两个例子,左图为构造的子图，右图为对应每个节点arrowLength的返回值
    2                               1
     \                               \
      2                               0
       \                               \
        1               =>              2 
         \                               \
          1                               1   
           \                               \
            1                               0 
            

    2                                2
     \                                \
      2                                1
       \                                \
        2                  =>            0 
         \                                \
          1                                1
           \                                \
            1                                0
```


2、其次，每个节点有一个**单侧**最长路径，和一个真正的(**双侧**)最长路径，单侧最长路径用来返回给上一个节点，也就是arrowLength
中的return的值，真正的最长路径是用来做比较 ，也就是 ans 的值。


```java
举例来说
    3
   / 
  3     <<
 / \  
3   3 
     \
      3
第二层的节点3 ，单侧最长路径是2，双侧最长路径是3
单侧最长路径： 在左右分支中选长的那条 => Math.max(arrowLeft, arrowRight)
双侧最长路径： 左右分支之和 => arrowLeft + arrowRight

为什么要这样分呢？

因为在第二层的节点3为根的子树中，对于这个根节点3本身来说，它所在的最长同值路径：是从“其左节点->其本身->其右节点”形成的3个节点的路径，
所以所包含的边是2。

但它不能把这个2传给其父节点（即return给上一层递归的函数）。
因为其父节点在计算最长路径的时候，只能选择“其父节点->其本身->其左孩子”或者“其父节点->其本身->其左孩子”，不能将其两个孩子的路径都
包含在内，最能返回左右分支中较长的那一个。

所以在做迭代计算时，每个节点的返回值对应为
    3  =>  max(arrowLeft = 2+1, arrowRight = 0)
   / 
  2   = >  max(arrowLeft = 0+1, arrowRight = 1+1)
 / \
0   1   = >  max( arrowRight = 0 + 1 ,arrowLeft = 0 )
     \ 
      0 
上层的节点是由其子节点返回值中的较大值+1得到的（父与子的值相同的情况下，值不相同则父为0）。 

而每个节点真正用来比较的res,即arrowLeft + arrowRight 为：
     3  => 3 + 0
    /
   3  => 1 + 2
  / \
 0   1   =>  0 + 1 
      \
       0  
```


3、最后比较出所有节点的res值中最大值，即为longestUnivaluePath的最终返回值。


#### 总结：

递归函数arrowLength中做了两件事：

1、计算以当前节点为根、且经过当前节点的最长**单侧**路径的边数，该值用来返回给父节点。
    
2、对每一个节点计算以当前节点为根、且经过当前节点的最长路径（**双侧**）的边数，所有节点中的最大值即是要求的结果。
