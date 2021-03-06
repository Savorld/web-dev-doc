# 红黑树

### 红黑树与AVL的比较：红黑树与AVL的比较：

AVL是严格平衡树，因此在增加或者删除节点的时候，根据不同情况，旋转的次数比红黑树要多；

红黑是用非严格的平衡来换取增删节点时候旋转次数的降低；

所以简单说，如果你的应用中，搜索的次数远远大于插入和删除，那么选择AVL，如果搜索，插入删除次数几乎差不多，应该选择RB。

红黑树详解: [https://xieguanglei.github.io/blog/post/red-black-tree.html](https://xieguanglei.github.io/blog/post/red-black-tree.html)

教你透彻了解红黑树: [https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/03.01.md](https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/03.01.md)

