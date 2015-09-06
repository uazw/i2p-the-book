# 闭包

当我们编程语言中，谈论闭包特性的时候， 和离散数学中的闭包，并没有关系。

编程语言中的**闭包**，描述了一种在**内部函数**定义时，获取**外部函数**（函数嵌套！）中的变量的手段。

##形象点？

我们知道一般情况下，要获得一个函数中的**局部变量**，只能通过返回值，并通过参数传递。

```c++
string outer() {
    string x = "hello world";
    return x;
};

void inner(string x) {
	std::cout << x << std::endl;
}

inner(outer()); // hello world

但是以上方法可能存在的问题在于，如果inner和outer需要传递的参数较多，
那么这样写就比较麻烦了。有没有一种方法特别好的能解决这种问题呢？
比如：

```c++

void inner(string v1, string v2, string v3, string v4) {
    // do some thing
}
```

铛铛铛~！闭包就可以，例如如上代码可以改造为
(由于C++不支持nest function, 这里用lambda表达式代替)

```c++

auto outer() {
	const std::string name = "hello world";
	return [name] () { std::cout << name << endl; }; //lambda expression
}

//因为返回的是一个lambda 所以需要额外调用一次
outer()(); // hello world
```
```c++
auto outer() {
    const std::string name = "hello world";
    return ([name] () { std::cout << name << std::endl; })();
}

//在内部调用一次我们就不用调用两次
outer()

更多关于lambda的详情请移步。

##鸡肋？

不不不， 因为这种捕获变量的能力，让我们可以做更多的事。

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
才会被求值，并返回结果。(在上面的例子中，只有调用seq才会求值。


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

很明显的可以看出第二个例子中，在构建整个序列，我们进行了length次求值，
在一般情况下这不是问题，当我们需要构建一个较大或者无穷数列的时候，这种方式就不能在用。


[lambda]: 
