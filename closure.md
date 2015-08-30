# 闭包

当我们编程语言中，谈论闭包特性的时候， 和数学集合论中的闭包，并没有关系。

编程语言中的**闭包**，描述了一种在**内部函数**定义时，获取**外部函数**（函数嵌套！）中的变量的手段。

##但是闭包长什么样呢？

我们知道一般情况下，要获得一个函数中的**局部变量**，只能通过返回值，并通过参数传递。

```c++
string inner() {
    string x = "hello world";
    return x;
};

void outer(string x) {
	std::cout << x << std::endl;
}

outer(inner()) // hello world
```

但是有了闭包我们就可以，将函数定义在另一个函数内部，并能获取局部变量
(由于C++不支持nest function, 这里用lambda表达式代替)

```c++

auto outer() {
	const std::string name = "hello world";
	return [name] () { std::cout << name << endl; }; //lambda expression
};

outer()(); // hello world
```

##但是，有什么卵用？

有用!有用， 自然是有用

比如实现各种集合
```c++

auto myPair(int first, int second) {
	return [=](string pos) {
		if (pos == "first") return first;
		if (pos == "secod") return second;
	};

```

比如缓存已经计算过的变量，

```c++

auto cachedAdd() {

	std::map<pair<int, int>, int>* cache = new std::map<pair<int, int>, int>;

	return [=](int a, int b) {
	std:pair<int, int> key{ a, b };
		if (cache->find(key) == cache->end()) {
			cache->insert(pair<pair<int, int>, int> {key, a + b});
		}
		return (*cache)[key];

	};
}

```

也比如也可以惰性序列

```c++

auto lazySeq(int init) {
	return [init]() mutable {
		return init++;
	};
}

int main()
{
	auto seq = lazySeq(2);

	for (int i = 0; i < 5; i++) {       // 根据循环次数产生元素
		std::cout << seq() << std::endl;
	}
}

```

其实以上例子都有个共同特点， 就是保存信息， 在需要的时候在拿出来。


##等等， 什么是惰性？！

惰性如同字面意思，就是很懒。。。懒到什么地步呢？懒到我们真正需要结果的时候，
才会被求值，并返回结果。


用上面的例子来讲就是如果我们要产生2-6序列，我们可以这样写

```c++

int* eagerSeq(int start, int end) {
	const int length = end - start;
	int* seq = new int[length];
    for (int i = 0; i < length; i++) {
		seq[i] = start + i;
	}
	return seq;
}

eagerSeq(2, 7);
```

很明显的可以看出第二个例子中，为了构建整个序列，我们进行了length次求值，
在一般情况下这不是问题，当我们需要构建一个较大或者无穷数列的时候，这种方式就不能在用。