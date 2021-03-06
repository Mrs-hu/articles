红黑树和AVL树的思想是类似的，都是在插入过程中对二叉排序树进行调整，从而提升性能，它的增删改查均可以在**O(lg n)**内完成。

本文会从定义到实现一棵红黑树展开，还会简单介绍其与AVL树的异同。

# 定义

红黑树是一棵二叉排序树。且满足以下特点：
1. 每个节点或者是黑色，或者是红色。
2. 根节点是黑色。
3. 每个叶子节点（NIL）是黑色。 [注意：这里叶子节点，是指为空(NIL或NULL)的叶子节点！]
4. 如果一个节点是红色的，则它的两个儿子都是黑色的。
5. 从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点。

下图就是一棵简单的红黑树示例：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_1.png"/>

示例中每个结点最后都是一个**NIL**结点，它是黑色的，不过我们画图时通常会省略它。所以下文以及后续文章中绘制时都会省略**NIL**结点，大家记得还有它就可以。

# 实现原理
红黑树的插入与删除和AVL树类似，也是每插入一个结点，都检查是否破坏了树的结构，然后进行调整。红黑树每个结点插入时默认都为红色，这样做可以降低黑高，也可以减少调整的次数。

## 插入元素

红黑树的概念理解起来较为复杂，我们以一个简单的示例，看看如何构造一棵红黑树。

现有数组`int[] a = {1, 10, 9, 2, 3, 8, 7, 4, 5, 6};`我们要将其变为一棵红黑树。

首先插入1，此时树是空的，1就是根结点，根结点是黑色的：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_2.png"/>

然后插入元素10，此时依然符合规则，结果如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_3.png"/>

当插入元素9时，这时是需要调整的第一种情况，结果如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_4.png"/>

红黑树规则4中强调不能有两个相邻的红色结点，所以此时我们需要对其进行调整。调整的原则有多个相关因素，这里的情况是，父结点**10**是其祖父结点**1**（父结点的父结点）的右孩子，当前结点**9**是其父结点**10**的左孩子，且没有叔叔结点（父结点的兄弟结点），此时需要进行两次旋转，第一次，以父结点**10**右旋：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_5.png"/>

然后将父结点**（此时是9）**染为黑色，祖父结点**1**染为红色，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_6.png"/>

然后以祖父结点**1**左旋：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_7.png"/>

下一步，插入元素2，结果如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_8.png"/>

此时情况与上一步类似，区别在于父结点**1**是祖父结点**9**的左孩子，当前结点**2**是父结点的右孩子，且叔叔结点**10**是红色的。这时需要先将叔叔结点**10**染为黑色，再进行下一步操作，具体做法是将父结点**1**和叔叔结点**10**染为黑色，祖父结点**9**染为红色，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_9.png"/>

由于结点**9**是根节点，必须为黑色，将它染为黑色即可：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_10.png"/>

下一步，插入元素3，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_11.png"/>

这和我们之前插入元素10的情况一模一样，需要将父结点**2**染为黑色，祖父结点**1**染为红色，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_12.png"/>

然后左旋：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_13.png"/>

下一步，插入元素8，结果如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_14.png"/>

此时和插入元素**2**有些类似，区别在于父结点**3**是右孩子，当前结点**8**也是右孩子，这时也需要先将叔叔结点**1**染为黑色，具体操作是先将**1**和**3**染为黑色，再将祖父结点**2**染为红色，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_15.png"/>

此时树已经平衡了，不需要再进行其他操作了，现在插入元素7，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_16.png"/>

这时和之前插入元素**9**时一模一样了，先将**7**和**8**右旋，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_17.png"/>

然后将**7**染为黑色，**3**染为红色，再进行左旋，结果如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_18.png"/>

下一步要插入的元素是4，结果如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_19.png"/>

这里和插入元素2是类似的，先将**3**和**8**染为黑色，**7**染为红色，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_20.png"/>

但此时**2**和**7**相邻且颜色均为红色，我们需要对它们继续进行调整。这时情况变为了父结点**2**为红色，叔叔结点**10**为黑色，且**2**为左孩子，**7**为右孩子，这时需要以**2**左旋。这时左旋与之前不同的地方在于结点**7**旋转完成后将有三个孩子，结果类似于下图：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_21.png"/>

这种情况处理起来也很简单，只需要把**7**原来的左孩子**3**，变成**2**的右孩子即可，结果如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_22.png"/>

然后再把**2**的父结点**7**染为黑色，祖父结点**9**染为红色。结果如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_23.png"/>

此时又需要右旋了，我们要以**9**右旋，右旋完成后**7**又有三个孩子，这种情况和上述是对称的，我们把**7**原有的右孩子**8**，变成**9**的左孩子即可，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_24.png"/>

下一个要插入的元素是5，插入后如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_25.png"/>

有了上述一些操作，处理5变得十分简单，将**3**染为红色，**4**染为黑色，然后左旋，结果如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_26.png"/>

最后插入元素6，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_27.png"/>

又是叔叔结点**3**为红色的情况，这种情况我们处理过多次了，首先将**3**和**5**染为黑色，**4**染为红色，结果如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_28.png"/>

此时问题向上传递到了元素4，我们看**2**、**4**、**7**、**9**的颜色和位置关系，这种情况我们也处理过，先将**2**和**9**染为黑色，**7**染为红色，结果如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_29.png"/>

最后7是根结点，染为黑色即可，最终结果如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_30.png"/>

可以看到，在插入元素时，叔叔结点是主要影响因素，待插入结点与父结点的关系决定了是否需要多次旋转。可以总结为以下几种情况：

* 如果父结点是黑色，插入即可，无需调整。

* 如果叔叔结点是红色，就把父结点和叔叔结点都转为黑色，祖父结点转为红色，将不平衡向上传递。

* 如果叔叔结点是黑色或者没有叔叔结点，就看父结点和待插入结点的关系。如果待插入结点和父结点的关系，与父结点与祖父结点的关系一致，比如待插入结点是父结点的左孩子，父结点也是祖父结点的左孩子，就无需多次旋转。否则就先通过相应的旋转将其关系变为一致。

## 删除元素

要从一棵红黑树中删除一个元素，主要分为三种情况。

### 情况1：待删除元素没有孩子

没有孩子指的是没有值不为**NIL**的孩子。这种情况下，如果删除的元素是红色的，可以直接删除，如果删除的元素是黑色的，就需要进行调整了。

例如我们从下图中删除元素1：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_31.png"/>

删除元素1后，2的左孩子为**NIL**，这条支路上的黑色结点数就比其他支路少了，所以需要进行调整。

这时，我们的关注点从叔叔结点转到兄弟结点，也就是结点**4**，此时**4**是红色的，就把它染为黑色，把父结点**2**染为红色，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_32.png"/>

然后以**2**左旋，结果如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_33.png"/>

此时兄弟结点为**3**，且它没有红色的孩子，这时只需要把它染为红色，父结点**2**染为黑色即可。结果如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_34.png"/>

### 情况2：待删除元素有一个孩子

这应该是删除操作中最简单的一种情况了，根据红黑树的定义，我们可以推测，如果一个元素仅有一个孩子，那么这个元素一定是黑色的，而且其孩子是红色的。

假设我们有一个红色节点，它是树中的某一个节点，且仅有一个孩子，那么根据红色节点不能相邻的条件，它的孩子一定是黑色的，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_35.png"/>

但这个子树的黑高却不再平衡了（**注意每个节点的叶节点都是一个NIL节点**），因此红色节点不可能只有一个孩子。

而若是一个黑色节点仅有一个孩子，如果其孩子是黑色的，同样会打破黑高的平衡，所以其孩子只能是红色的，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_36.png"/>

只有这一种情况符合红黑树的定义，这时要删除这个元素，只需要使用其孩子代替它，仅代替值而不代替颜色即可，上图的情况删除完后变为：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_37.png"/>

可以看到，树的黑高并没有发生变化，因此也不需要进行调整。

## 情况3：待删除元素有两个孩子

我们在讨论二叉排序树时说过，如果删除一个有两个孩子的元素，可以使用它的前驱或者后继结点代替它。因为它的前驱或者后继结点最多只会有一个孩子，所以这种情况可以转为情况1或情况2处理。

# 总结

删除元素最复杂的是情况1，这主要由其兄弟结点以及兄弟结点的孩子颜色共同决定。这里简要做下总结。

我们以**N**代表当前待删除节点，以**P**代表父结点，以**S**代表兄弟结点，以**SL**代表兄弟结点的左孩子，**SR**代表兄弟结点的右孩子，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_38.png"/>

**根据红黑树定义，这种情况下S要么有红色的子结点，要么只有NIL结点，以下对S有黑色结点的情况均表示NIL**

主要有以下几种：

1. S是红色，P一定是黑色，S也不会有红色的孩子，如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_39.png"/>

此时把**P**和**S**颜色变换，再左旋，如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_40.png"/>

这样变换后，**N**支路上的黑色结点并没有增加，所以依然少一个，

2. P，S以及S的全部孩子都是黑色

无论**S**有几个孩子，或者没有孩子，只要不是红色都是这种情况，此时情况如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_41.png"/>

我们把**S**染为红色，这样一来，**N**和**S**两个支路都少了一个黑色结点，所以可以把问题向父结点转移，通过递归解决。染色后如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_42.png"/>

3. P为红（S一定为黑），S的孩子都为黑

这种情况最为简单，只需要把P和S颜色交换即可。这样N支路多了一个黑色元素，而S支路没有减少，所以达到了平衡。

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_43.png"/>

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_44.png"/>

4. P任意色，S为黑，N是P的左孩子，S的右孩子SR为红，S的左孩子任意

如下所示

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_45.png"/>

此时将**S**改为**P**的颜色，**SR**和**P**改为黑色，然后左旋，结果如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_46.png"/>

可以发现，此时**N**支路多了一个黑色结点，而其余支路均没有收到影响，所以调整完毕。

5. P任意色，S为黑，N是P的左孩子，S的左孩子SL为红，S的右孩子SR为黑，如下所示：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_47.png"/>

此时变换**S**和**SL**的颜色，然后右旋，结果如下：

<img src="https://github.com/LtLei/articles/blob/master/java/collection/image/img_6_48.png"/>

这时，所有分支的黑色结点数均没有改变，但情况5转为了情况4，再进行一次操作即可。

还有一些情况与上述是对称的，我们进行相应的转换即可。

#总结
红黑树的操作比较复杂，插入元素可能需要多次变色与旋转，删除也是。这些操作的目的都是为了保证红黑树的结构不被破坏。这些复杂的插入与删除操作希望大家可以亲手尝试一下，以加深理解。

红黑树是JDK中**TreeMap**、**TreeSet**的底层数据结构，在JDK1.8中**HashMap**也用到了红黑树，所以掌握它对我们后续的分析十分重要。

关于红黑树与AVL树的区别，以及为何选用红黑树，已经不属于我们的讨论范围，大家可以查阅相关资料进一步了解。

本文到此就结束了，如果您喜欢我的文章，可以关注我的微信公众号： **大大纸飞机** 

或者扫描下方二维码直接添加：

<img src ="https://github.com/LtLei/articles/blob/master/qrcode.jpg" />

您也可以关注我的简书：https://www.jianshu.com/u/9ee83a8ee52d

编程之路，道阻且长。唯，路漫漫其修远兮，吾将上下而求索。