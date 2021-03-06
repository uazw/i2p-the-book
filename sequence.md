# 序列

你应该记得，我们在编码那一张提过如何表示多个数字的，没错，定长编码可以帮我们解决这个问题，帮我们界定数字之间的边界。但是，当我们要表示的数据不只是一组的时候，又该怎么来界定边界？

比如，我们有两组数，分别是$$1,3,5,7,9,11$$和$$0,2,4,6,8,10$$，假设以一个字节（十六进制）表示的话，就是下面这个样子的：
```c++
0x01030507 0x090B0002 0x0406080A
```
好的，接下来请完成以下任务：
 - 找到第二个序列中的第三个数。

嗯，是不是感觉自己傻逼了，如果不知道第一组有几个的话你是不可能准确找到这个数字的。

于是我们得到了一个简单的界定序列边界的方法，就是要知道各序列对应的长度。

比如我们知道这两个序列是连续放在一起的，第一个序列长度是$$6$$，同时也知道要找的是第二个序列的第三个数，那么这个数就应该是第$$6+3$$个数字。

当然，这个数字要从$$1$$数过去才行，否则从任意其他的位置数，都不能得到我们想要的结果。

## 长度

我们可以看到，界定一个序列，必须要知道的是两点，第一个是他的起始元素，第二个就是这个序列的长度。

一般来说，当这个序列的长度固定不变，并且每个元素是挨在一起的时候，我们常把它叫做array[^1]。

> 关于array，我非常不喜欢“数组”这个翻译，因为这里面并不一定是数，也有可能是符，所以我会尽可能地使用array来表示它。

在C++中，定义一个[array](http://en.cppreference.com/w/cpp/container/array)如下：
```c++
// to import "array" symbol
#include <array>
#include <cstdio>

using namespace std;

int main() {
    array<int, 5> nums = {1, 2, 3, 4, 5};
    for(int i = 0; i < 5; i++) {
        printf("%d ", nums[i]);
    }
}
```
上面那段代码中，我们定义了一个长度为5的int array，然后遍历并将其每一个元素输出。（在有些地方，创建array的时候你可能也会看到`int nums[5] = {1, 2, 3, 4, 5};`这种古典的写法）。

对于array来讲，初始元素是方括号中的取值为0的时候的值。在方括号中的值我们称之为index（**索引**，奇葩翻译一般叫做**下标**）。这些索引以数字的形式出现，作为序号来表示数据在序列中的位置。

有个重点是，这里的索引是从$$0$$开始，最大是$$序列长度-1$$，这其实也就是为什么我们在第三章讲过程的时候提到循环的写法的一个原因。

不过对于遍历序列来说还支持另外一种写法。
```c++
array<int,5> nums = {1, 2, 3, 4, 5};
for(auto item: nums) {
    printf("%d ", item);
}
```
不需要你去管什么索引了，这样子写就像是在说
```c++
对于 nums 里面的 item
    输出 item 的值
```
当然，在Python[^2]中看起来更自然：
```python
for item in nums:
    print(item)
```

## 串

然而很多时候并不是那么理想。比如在获取到数据的时候我们并不能够准确的知道他的长度。这种时候，就需要另外的方式来确定了。

之前我们说过，通过起始位置和长度可以界定一个序列了，另外考虑一种情况，如果这个序列是连续的，知道长度的话其实是为了方便我们确认序列的最终位置。

所以，即便没有长度，如果我们知道了序列的最终位置的话，也能确定这整个序列的边界。

串（String[^3]）就是这样一种结构。特别地，虽然C++对其做了一层包装，本质上string还是Null-terminated，也就是说使用空字符（NUL，ASCII 0x00）来标示串的结尾。
```c++
// import "string"
#include <string>
#include <cstdio>

using namespace std;

int main() {
    char shit[] = {'s', 'h', 'i', 't', '\0'};
    string hello = "hello world";
    printf("%s ", shit);
    printf("%s ", hello.c_str());
}
```
第一个串`shit`是比较古典的创建一个**字符**串[^4]的方法，其实就是一个字符组成的array，然后最后一个'\0'（表示ASCII为0的字符，也就是NUL）标记了这是字符串的结尾。

第二个`hello`是直接创建一个string，对于`"hello world"`，只是`{'h', 'e', ..., 'r', 'l', 'd', '\0'}`的简化后的更便捷的写法（也叫语法糖 Syntactic Sugar[^5]，意为使用起来更加方便简单的语法）。

其实你可以试试把`shit`里面的那个`\0`去掉，再编译运行看看结果，这个时候你们离打印出“烫烫烫”不远了。

多试几次的话你会发现可能每次在`shit`输出后的东西是不固定的，随时可能变成任何值。为什么会出现这种问题呢？因为我们之前提到过了，关于串，是需要通过末尾的那个NUL（空字符，'\0'）来确定是否终止的。而当你把NUL去掉以后，并不知道`shit`后面的内存里是什么东西，所以只好一直print下去直到找到那个NUL字符为止。

这样也就很方便的在不确定长度的情况下也能表示一串数据了。

一个实际的例子，比如我们想要把两个字符串连接起来，于是我们可以这样定义一个函数来处理：
```c++
void string_cat(char a[], char b[]) {
    int i = 0;
    while(a[i++]) {}
    i--;
    int j = 0;
    while((a[i++]=b[j++])) {}
}
```
是不是被萌哭了？

没关系，你暂时理解不了的话我不会怪你的。只要看看我们用它得到的结果就知道了：
```c++
char a[256] = "hello ";  // just for avoid index out of range
char b[] = "world";
string_cat(a,b);
printf("%s",a);
```
Bingo！屏幕上显示出了"hello world"！

我们的`string_cat`函数在没有任何“长度”概念的情况下完成了两个字符串的链接。这就是“串”这种序列结构的魅力。

但是你有没有注意到我们测试`string_cat`的代码中的那个数字，$$256$$，很突兀的显示在那里。

为什么会是这样的呢？首先，串这种结构跟array一样，要求存储是连续的，所以当我们想要给串添加元素时，就要考虑他是否有充足的容量，于是在刚刚这个例子中，我把这个容量设置成了256。

但其实很多时候这个并不符合我们的要求，就像容量大于256的序列多得是，比如图书的页码、你高中时写的作文的字数等等，而且还有很多的长度是随时在变化的，比如你写文章的时候会不停的往正文的内容里添加文字。

这种时候，如果我们随便指定了序列的最大长度，很有可能就会不符合需求。

那么遇到这种情况我们该怎么办呢？

## 可变长序列

试想你在一个笔记本上记录内容，每一行算是一个条目，当一页记录满以后，并不能连续地往下面记了，就需要翻到下一页纸。

连续不连续并不重要，重要的是我们能从这一页翻到下一页。

对应到我们的序列表示上，只要当前元素能够记录下一个元素的位置，是不是就表示我们的序列就能从第一个元素全部找出来了呢？

就像这张图片描述的：

![list](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Cons-cells.svg/320px-Cons-cells.svg.png)

这种结构被称为链接列表（也叫链表，Linked List[^6]，在C++中称为forward_list）在上世纪五十年代就已经设计用于实际的程序。链表这种结构非常的灵活，可以很轻松的在任意位置添加或者删除数据。因为并不需要内存结构上的连续，所有的位序关系都是靠前一个元素和后一个元素之间的链接关系来确定，而添加或者删除数据只要改变一下这种链接关系就可以了。

但是对于链表来说一个很大的问题就是访问数据。比如我们要访问链表的第n个元素的时候，对于array和串这种结构来说，因为内存空间上是连续的，直接做一次加法就能找到，但是对于链表来说每次都要从第一个元素开始找起。而且，这种形式的链表又称作单链表（Singly Linked List），是没有办法从尾部往头部走的。

所以，在单链表的基础上，又构造了双链表（Double Linked List，C++中对应的是list，又叫列表），每一个元素除了纪录自己对应的下一个元素之外，还要保存自己对应的上一个元素。这种时候如果做一些相邻元素的访问，就比单链表高效了一些。

比如，当我访问列表中第7个元素，然后再去访问第6个的时候，只要从第7个元素向前查找一次就可以了。

也许以后你可能还会听说到循环链表（Circular Linked List），或者是其他的形式，这些都离不开最初链表的思想：把数据和位置做关联。

## 线性结构

我们简单的做一个总结。

对于array，string和list我们都称为序列，原因其实是显而易见的。

首先，他们满足这样一点：所有的都是从头到尾连续地组织在一起，从任何地方出发都能找到下一个元素或者是到序列尾部。就像是在头和尾之间有一条线把他们串起来一样，而且在逻辑表示上，我们也是把他们放成一串。

另外，对于每一个元素，他们在序列中的位置是固定的，换句话说，序列中的元素是有前后顺序的。当我任意交换两个元素的位置，得到的新序列就跟原来的序列是不同的。

第三点就是，当我们从第一个元素开始找下去的时候，总是能找到最后一个元素（对于没有最后一个元素的情况，我们会在以后进行探讨）。

总结下来就是，序列是线性的（Linear）、有序的（Ordered，类比“排序过的（Sorted）”）和有穷的（Finite）。

## 迭代器
既然所有的序列都满足线性有序有穷这些特性，那么也就意味着我们能够把他们的一些类似的操作统一起来进行处理。

比如我们期望有这样一种操作，能够把一个序列中的所有元素的值输出：
```c++
list<int> aList = { 2, 8, 3, 1 };
array<int, 4> anArray = { 9, 5, 0, 7 };
string aString = "hello world";

print(aList);
print(anArray);
print(aString);
```
迭代器（Iterator）就是这样一种东西，让你忽略不同类型的序列之间的差异，只利用他们的迭代器做一些公共的操作。

比如我们的print函数就可以这样实现。
```c++
#include <iostream>

template <typename Sequence>
void print(const Sequence& sequence) {
    for(auto iter = begin(sequence); iter != end(sequence); iter++)
        std::cout << *iter << std::endl;
}
```
其中，`template <typename Sequence>`表示Sequence可能是任意一种类型，至于具体会是什么类型，会在print调用的时候推导出来。比如`print(aList)`的时候，`Sequence`就会是`std::list<int>`，在另外两种情况下，会是`std::array<int, 4>`或`std::string`。

于是这样我们就能够拿`Sequence`来表示这些调用的时候可能会遇到的不同的序列，来定义`print`函数。其中，`const Sequence& sequence`是说，`print`函数接收的是一个Sequence类型的常引用（`const` Reference，关于引用的更多话题，我们会在后面讨论），这样做是为了提高传递参数的效率。

接下来就是我们的迭代器出场了，我们知道，根据有穷性，我们是可以获得到序列的开始位置（begin，第一个元素）和结束（end，最后一个元素的下一个位置）位置的。这个对应的位置就是该序列的迭代器。然后根据序列的线性，我们可以让迭代器移动到下一个元素的位置（`iter++`），这样反复执行，就可以顺利地遍历一个序列。

另外需要注意的是`std::cout`，提供了一套比较简单的输出方法，请参阅C++ Reference的相关资料。

## Iterator Categories

很多时候，只有向前移动到下一个元素这种操作并不能满足我们的要求（所以才有双链表比单链表更常用），有时候我们更需要迭代器会倒带（`iter--`）。但是在有些序列（像forward_list，单链表）上，这种操作根本不能实现。所以，对于迭代器，也就要有一种分类。

 - 第一种迭代器就是最简单的，可以向下一个元素迭代，叫做**ForwardIterator**。

 - 第二种当然就是能够向上一个元素迭代，一个迭代器既是Forward又是Backward，那么就叫做**BidirectionalIterator**（双向迭代器）。

 - 第三种能够直接访问到当前迭代器对应向下第n个元素，这个时候迭代器也可以直接用索引进行访问，一个迭代器既可以双向迭代，又可以直接索引访问，就叫做**RandomAccessIterator**。

这种分类的关键在于不同的算法的适用范围。

比如`std::sort`，必须要求是RandomAccessIterator。所以如果你直接`sort(begin(aList), end(aList))`，会得到满满的正常人根本无法阅读的编译错误。

### 练习：最长公共子序列（Longest Common Subsequence）

通常我们会对两个序列的内容进行对比，比较这两个序列有什么地方不同。这就是典型的LCS问题。LCS的应用很多，大部分版本控制系统的核心逻辑中就有它的存在。

请参阅Wikipedia对LCS问题的分析和讨论，尝试实现求LCS的算法，并试分析该算法要求迭代器至少要属于哪个category。

[^1]: https://en.wikipedia.org/wiki/Array_data_structure

[^2]: https://en.wikipedia.org/wiki/Python_%28programming_language%29

[^3]: https://en.wikipedia.org/wiki/String_%28computer_science%29

[^4]: 大部分情况下，我们所遇到的“串”都是字符串，虽然C++里面你也可以拿[其他的东西](http://en.cppreference.com/w/cpp/string/basic_string)作为“串”来玩。

[^5]: https://en.wikipedia.org/wiki/Syntactic_sugar

[^6]: https://en.wikipedia.org/wiki/Linked_list
