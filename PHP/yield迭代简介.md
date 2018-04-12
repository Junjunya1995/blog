## yield 随笔
感谢  [PHP的生成器、yield和协程](https://www.cnblogs.com/tingyugetc/p/6347286.html)
这篇博文的讲解。本博客原文出自此处。附带我自己的个人理解

yield 是php5.5开始提供的新特性  名字叫做`生成器` 功能类似 `迭代器`
官网的例子是  实现PHP自带的` range()`函数
```php
<?php
//默认$start < $limit
function xrange($start, $limit) {
    for ($i = $start; $i <= $limit; $i ++) {
        yield $i;
    }
}
foreach (xrange(1,3) as  $value) {
	echo $value;
} ;

//输出 123
```

在说 `yield` 之前 先说下`foreach` 和迭代
迭代是指反复执行一个过程，每执行一次叫做迭代一次。比如普通的遍历便是迭代：
```
$arr = [1,2,3];
foreach ($arr as  $value) {
	echo $value;
}
```
通过`foreach`对数组遍历 输出其内容的过程。在`foreach`内部，每次循环都会将当前的元素的值赋给`$value`并将数组的指针移动指向下一个元素，为下一次迭代坐准备，从而实现顺序遍历。
像这样能够让外部的函数迭代自己内部数据的接口就是`迭代器接口`，对应的那个被迭代的自己就是`迭代器对象`

PHP提供了统一的迭代器接口：`Iterator`
```
Iterator extends Traversable {
    // 返回当前的元素
    abstract public mixed current(void)
    // 返回当前元素的键
    abstract public scalar key(void)
    // 向下移动到下一个元素
    abstract public void next(void)
    // 返回到迭代器的第一个元素
    abstract public void rewind(void)
    // 检查当前位置是否有效
    abstract public boolean valid(void)
}
```

通过实现`Iterator接口`，我们可以自行的决定如何遍历对象。比如通过实现`Iterator接口`我们可以观察迭代器的调用顺序。
```
class MyIterator implements Iterator {
    private $position = 0;
    private $arr = [
        'first', 'second', 'third',
    ];

    public function __construct() {
        $this->position = 0;
    }

    public function rewind() {
        var_dump(__METHOD__);
        $this->position = 0;
    }

    public function current() {
        var_dump(__METHOD__);
        return $this->arr[$this->position];
    }

    public function key() {
        var_dump(__METHOD__);
        return $this->position;
    }

    public function next() {
        var_dump(__METHOD__);
        ++$this->position;
    }

    public function valid() {
        var_dump(__METHOD__);
        return isset($this->arr[$this->position]);
    }

}

$it = new MyIterator();

foreach($it as $key => $value) {
    echo "\n";
    var_dump($key, $value);
}
```
通过这个例子能够清楚的看到了`foreach`循环中调用的顺序。从例子也能看出通过迭代器能够将一个普通的对象转化为一个可被遍历的对象

### yield和生成器

相比较`迭代器`，`yield`提供了一种更容易的方法来实现简单的对象迭代，性能开销和复杂性都大大降低。

一个`生成器函数`看起来像一个`普通的函数`，不同的是`普通函数`返回一个值，而一个生成可以yield生成许多它所需要的值，并且每一次的生成返回值只是暂停当前的执行状态，当下次调用生成器函数时，PHP会从上次暂停的状态继续执行下去。

```
function func() {
    yield 1;
    yield 2;
    yield 3;
    yield 4;
}
foreach (func() as  $value) {
    echo $value;
} ;
//输出 1234
```
可以看到 在上面代码中，每次循环返回一个yield的值，更多语法查看[生成器语法](http://php.net/manual/zh/language.generators.syntax.php)。
实际上生成器函数返回的是一个`Generator`对象，这个对象不能通过new实例化，并且实现了`Iterator接口`
```
Generator implements Iterator {
    public mixed current(void)
    public mixed key(void)
    public void next(void)
    public void rewind(void)
    // 向生成器传入一个值
    public mixed send(mixed $value)
    public void throw(Exception $exception)
    public bool valid(void)
    // 序列化回调
    public void __wakeup(void)
}
```
可以看到出了实现`Iterator`的接口之外`Generator`还添加了`send`方法，用来向生成器传入一个值，并且当做`yield`表达式的结果，然后继续执行生成器，直到遇到下一个`yield`后会再次停住。
```
function printer() {
    while(true) {
        echo 'receive: ' . yield . "\n";
    }
}

$printer = printer();
$printer->send('Hello');
$printer->send('world');
//输出
// receive: Hello
// receive: world
```
在上面的例子中，经过第一个`send()`方法，`yield`表达式的值变为`Hello`，之后执行`echo`语句，输出第一条结果`receive: Hello`，输出完毕后继续执行到第二个`yield`处，只不过当前的语句没有执行到底，不会执行输出。

关于yield 迭代的讲解就到此 ，下次有时间写关于yield实现协程的部分
