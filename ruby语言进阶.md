# ruby语言进阶

## 1. 简介

- ruby是一个动态的（不用指定类型）、反射性的、面向对象的、通用的解释型语言

## 2. 优缺点

优点：

- 简单
- 快速开发
- 灵活但是不过分灵活
- 简洁
- 开源

缺点：

- 运行效率比编译语言低（c++、java），不过足够了
- 对于多线程支持不太好（Poor multithreading support），ruby有一个全局的锁，所有操作都会在锁的控制下
- 在很多地方不流行

## 3. 数字和文本

### 数字类型

![image-20220710182150648](C:\Users\lh\AppData\Roaming\Typora\typora-user-images\image-20220710182150648.png)

​                                                          数字层级结构（其中fixnum、bignum、float）比较常用

ruby会自动给数值设置类型：(旧版ruby)给一个小的整数会自动设置为fixnum、大的整数会设置为bignum；

(新版ruby)整数都会设置为Integer。例如：

![image-20220710192350041](C:\Users\lh\AppData\Roaming\Typora\typora-user-images\image-20220710192350041.png)

小数会自动设置为float，例如：

![image-20220710192532446](C:\Users\lh\AppData\Roaming\Typora\typora-user-images\image-20220710192532446.png)

### 数字对象方法及逻辑操作

1.查看类别a.class

```ruby
3.2.class
=> Float
```

2.判断奇数偶数

```ruby
3.even?
=> false
3.odd?
=> true
```

3.数字转字符

```ruby
283.to_s
=> "283"
```

4.次数

```ruby
3.times { p 'love' }
"love"
"love"
"love"
=> 3
```

5.位操作

```ruby
3 & 1
=> 1
```

6.小数取位数

```ruby
3.232323.round(2)    #取小数点后两位
=> 3.23
```

twitter企业文化：学会使用现有的库，而不是反复造轮子（don't reinvent the wheel）

### 字符串类型

1. 字符串的mutable

字符串类型是可变的（mutable），相同的字符串是不同的对象， symbol除外。例如：

```ruby
a = "abc"
b = "abc"
a.object_id
=> 180
b.object_id
=> 200
"abc".object_id
=> 220
"abc".object_id
=> 240
```

数字是不可变的（immutable），相同的数字是一个对象。例如：

```ruby
a = 3
b = 3
a.object_id
=> 7
b.object_id
=> 7
3.object_id
=> 7
```

2. 字符串定义

使用单引号或双引号定义

```ruby
'something'        #单引号
=> "something"
a = 'asdf'
=> "asdf"
"something#{a}"    #双引号，可以在双引号内使用#{}引用变量
=> "somethingasdf" 
'asdf\'sdfsd'      #特殊字符需要转译
=> "asdf'sdfsd"
%q(safaer'fder'wer') #使用%q()或%Q()定义字符串不需要转译特殊字符
=> "safaer'fder'wer'"
```

生成多行字符串，使用<<-text，text 为任意字符

```ruby
<<-text
asdfsadgfasg
asdfsadfasf
asdfsaf
text
=> "asdfsadgfasg\nasdfsadfasf\nasdfsaf\n"
```

### 字符串常用方法

 1.反转字符串

```ruby
"asdf".reverse
=> "fdsa"
```

2.是否包含字符

```ruby
"hello".include?('o')     #当方法返回值为bool类型时需要在方法名后加?
=> true
```

3.指定字符的索引

```ruby
"hello".index('l')
=> 2
```

4.字符替换

```ruby
"asdf".sub('s', 'b')
=> "abdf"
"asdf".sub!('s', 'b')  #方法名后加!表示可能会改变对象本身
=> "abdf"

a = 'asdf'      #若方法名后不加!则不会改变a的值
=> "asdf"
a.sub('s', 'b')
=> "abdf"
a
=> "asdf"
```

5.字符串长度

```ruby
a = 'asdf'
a.size
=> 4
```

6.字符串转换成符号(symbol)

```ruby
a.to_sym
=> :asdf
```

### 命名规则

- 变量，符号，方法：蛇形命名(snake_case)
- 常量：大写蛇形命名(CONST_FOO)
- 类名：驼峰命名(CamelCase)
- 文件名：file_name.rb

## 4.符号和数组

### 符号为什么查找速度快

优点：

symbol是immutable的，而且定义的symbol会被存储在symbol table中，所以通过symbol查找数据速度较快，而不使用symbol的话需要进行字符串比较取查找。

因为symbol查找快的特性，ruby中所有函数名都是symbol，哈希的key也是symbol。

缺点： 

symbol不会被回收，所以大量使用会占用过多内存。所以在常用的、不会经常改变的东西使用symbol，而经常改变的变量使用字符串。

### 数组的mutable特性

​	数组和字符串一样都是mutable的数据结构，所以创建的数组可以随意改变，当数组赋值给一个变量时该变量只是数组的引用。

![image-20220710231906016](C:\Users\lh\AppData\Roaming\Typora\typora-user-images\image-20220710231906016.png)

```ruby
a = []
a.object_id
=> 280
b = a
b.object_id
=> 280
a << 'foo'
=> ["foo"]
a.object_id
=> 280
b.object_id
=> 280               # 因为变量a、b都是数组对象的引用，所以修改的只是数组对象的内容，而变量ab对象并没有改变
```

### 数组定义

1.a = [1, 2]

2.a = Array.new(3)

生成了一个长度为3的array：[nil, nil, nil]

3.a = Array.new(3, 0)

生成了一个长度为3的array：[0, 0, 0]

4.生成长度为3的字符串数组

```ruby
a = Array.new(3, 'asdf')    #使用这种方式定义的字符串数组，每项字符串都是指向同一个字符串对象的reference，所以改变其中一个字符串的内容，其它两个也会一起改变。
=> ["asdf", "asdf", "asdf"]
a[0][0] = 'b'
a[0]
=> "bsdf"
a
=> ["bsdf", "bsdf", "bsdf"]

a = Array.new(3) { 'asdf' } #使用这种传入block的方式定义的字符串数组，每项字符串都是不同的对象，所以可以只改变其中一个字符串内容，而其它两个不会改变。
```

5.使用%w()定义array

```
arr = %w(foo bar wut wat)
=> ["foo", "bar", "wut", "wat"]
```

### 数组常用方法

1.取值

```ruby
arr[0]         #索引取值 
=> "foo"
arr[-1]
=> "wat"
arr[1..2]      #取一个区间
=> ["bar", "wut"]
arr.fetch(5, 'asdf')  #按索引取值，如果索引超出数组范围，则返回默认值'asdf'
=> "asdf"
```

2.数组长度

```ruby
arr.length
=> 4
```

3.数组中是否存在内容

```ruby
arr.include?('gergds')
=> false
arr.include?('bar')
=> true
```

4.数组是否为空

```ruby
arr.empty?()
=> false
```

5.数组末尾插入内容

```ruby
arr.push('ber')
=> ["foo", "bar", "wut", "wat", "ber"]
```

6.数组任意位置插入内容

```ruby
arr[10] = 'asdf'
=> ["foo", "bar", "wut", "wat", "ber", nil, nil, nil, nil, nil, "asdf"]
```

7.删除元素

```ruby
arr.delete_at(0)   #根据索引删除
arr.delete('wut')  #根据内容删除
```

8.去除重复元素

```ruby
arr.uniq   #不改变原数组
arr.uniq!  #改变原数组
```

9.随机打乱数组元素顺序

```ruby
arr.shuffle
```

10.将多维数组展平为一维数组

```ruby
arr = [[1,2,3],[4,5]]
arr.flatten
=> [1,2,3,4,5]
arr.flatten!
=> [1,2,3,4,5]
```

11.数组遍历（数组可枚举enumerable）

```ruby
arr = [1,-1,2,3,-4]
arr.each {|e| p e}    #使用each顺序遍历arr，每个元素用e表示，并打印
1
-1
2
3
-4
=> [1, -1, 2, 3, -4]

arr.reverse_each {|e| p e} #使用reverse_each逆序遍历arr，每个元素用e表示，并打印

arr.each_with_index {|e, i| p [e, i]} #使用each_with_index遍历所有元素和索引，并打印
[1, 0]
[-1, 1]
[2, 2]
[3, 3]
[-4, 4]
```

12.数组排序

```ruby
arr.sort
arr.sort!
```

13.条件查找数组元素

```ruby
arr.select {|e| e > 0}  #查找大于0的元素
```

14.删除所有nil元素

```
arr = [-4,-1,1,2,3,nil]
arr.compact
=> [-4,-1,1,2,3]
arr.compact!
=> [-4,-1,1,2,3]
arr.compact!     #当方法没有产生任何影响时输出nil
=> nil
```

15.判断数组是否有元素满足条件

```ruby
arr.any? {|e| e < 0}  #数组arr中是否有小于0的元素
=> true
```

## 5.哈希，集合和范围

### 哈希的mutable特性

哈希和数组、字符串一样都是mutable的数据结构，所以创建的哈希可以随意改变，当哈希赋值给一个变量时该变量只是哈希的引用。

```ruby
a = { key: 'value' }     #变量a只是hash{ key: 'value' }的引用
=> { :key=> "value"}
b = a
b.object_id
=> 70329779393420
a.object_id
=> 70329779393420
```

### 创建哈希

```ruby
h = { a: 3, b: 4}       #形式1
=> {:a=>3, :b=>4}
h = {:a => 3, :b => 4}  #形式2
=> {:a=>3, :b=>4}
h = Hash.new     #形式3，创建一个空hash
h = Hash.new("month")    #形式4，创建一个具有默认值的hash
h = Hash.new "month"
h[0]        #当key不存在时，返回默认值month
=> "month"
h = Hash.new([])  #形式5，创建一个值为数组[]的hash，和array一样，每个元素都是[]的引用，所以改变其中一个元素，其它元素也会变
h[:a] << 1
=> [1]
h[:b]
=> [1]
h = Hash.new {|h, k| h[k] = []}   #形式6，使用block功能创建一个值为数组[]的hash，每个元素都是不同的[]对象引用，所以改变其中一个元素不会改变其它元素
h = {"a"=>3, "b"=>4}    #除了symbol作为key，还可以使用string
=> {"a"=>3, "b"=>4}
```

### 哈希常见方法

1.赋值

```ruby
h = {a: 3, b: 4}
h[:c] = 5     #添加c: 5键值对
h
=> {a: 3, b: 4, c: 5}
```

2.取值

```ruby
h[:c]    #取键为c的值
=> 5
h.select {|key| key == :a}  #从h中取key为:a的元素
=> {:a=>3}
h.assoc(:c)    #输出键值对
=> [:c, 5]
```

3.删除元素

```ruby
h.delete(:c)
=> 5
```

4.判断是否为空

```ruby
h.empty?
=> false
```

5.判断是否有某键值

```ruby
h.has_value?(2)   #h哈希中是否有2值
=> true
h.has_key?(:c)
=> true           #哈希中是否有键:c
```

6.取所有键值

```ruby
h.keys    #取所有键
=> [:a, :b, :c]
h.values
=> [3, 4, 5]
```

7.转换成array

```ruby
h.to_a
=> [[:a, 3], [:b, 4], [:c, 5]]
```

8.合并哈希

```ruby
h2 = {d: 6}
h.merge(h2)   #h和h2合并
=> {:a=>3,:b=>4,:c=>5,:d=>6}
```

9.遍历hash

```ruby
h.each {|key, value| p [key, value]}    #使用each和block遍历所有元素键值对
[:a, 3]
[:b, 4]
[:c, 5]

h.each_key {|key| p key}    #只遍历key
:a
:b
:c

h.each_value {|v| p v}    #只遍历value
3
4
5
```

### 集合特性

集合是hash和array的结合体，集合的内部实现用到了hash，所以查找比较快，同时像array一样可以作为容器。

集合里不能有重复元素。

### 集合定义

```ruby
require 'set'    #因为在irb中，默认没有加载set库，所以需要手动加载（新版本ruby不用加载）
Set.new [1,2]
=> #<Set: {1, 2}>
s = _            #将创建的set赋值给变量s，_表示irb中上一个对象
```

### 集合常用方法

1.添加元素

```ruby
s.add('foo')
=> #<Set: {1, 2, "foo"}>
```

2.集合运算

```ruby
b = Set.new [2,3,4]   # 定义一个新集合b
=> #<Set: {2, 3, 4}>
s & b   #做交集
=> #<Set: {2}>
s | b   #做并集
=> #<Set: {1,2,"foo",3,4}>
s <= b  #s是否是b的子集
=> false
b <= s  #b是否是s的子集
=> false
```

### 范围

范围分为闭区间和开区间两种。

```ruby
r = 1..2   #创建一个闭区间[1,2]
r.include?(2)  #判断区间r是否包含2
=> true
r2 = 1...2  #创建一个右开区间范围[1]
```

范围的作用

```
a = [1,2,3,4]
a[1..2]     #取值
=> [2,3]
```

## 6.方法

### 方法的定义

需要知道的东西：

1.ruby方法的返回值是什么？

ruby中少用return，尤其是方法末尾的return，因为ruby方法最后一句代码是默认返回。

```ruby
#避免使用return代码举例
def is_target?(x)     #原方法判断语句有多个return
  if cond1
    if cond2
      return foo
    else
      return true
    end
  else
    return false
  end
end

def is_target?(x)     #现去掉return
  if cond1
    if cond2
      foo?
    else
      true
    end
  else
    false
  end
end

def is_target?(x)    #继续简化
  cond1 && (!cond2 || foo?)
end
```

如果不得已需要用return，那么尽量在代码一开始就写出所有return。

```ruby
def foo(x)
  return if x.blank? || x.bar? || x.baz?
  wut(x)
  wat(x)
end
```

2.ruby中的self是什么？

```ruby
str = 'Hello World'  #定义一个string对象str
def str.foo      #定义一个str对象的单例方法foo
  puts self      #self就是指str对象本身
end
```

3.什么是方法别名？

可以给一个方法名起一个别名，使用别名也可以调用该方法。

```ruby
def foo          #首先定义一个方法foo
  puts 'foo'
end
alias :bar :foo  #使用关键字alias给方法foo起一个别名bar
bar              #使用别名bar调用方法foo
foo
=>nil
```

使用别名可以使一个方法在不同场景下调用时更符合语义，例如：include?和member?，include?表示对象中是否包含某元素，member?表示对象中是否有某成员。

4.什么是运算符方法？

在c++中一个bracket运算符表示的只是运算符而不是方法，例如：

```c++
int arr[3];    #定义一个数组，其中[3]仅表示位置运算符arr+sizeof(int)*3
```

在ruby中bracket运算符表示一个方法，例如：

```ruby
arr = [1,2,3]
arr[2]     #可以使用下标[2]取值
=> 3
arr.[](2)  #使用[]方法也可以取arr中的值
=> 3
因为ruby是一门完全面向对象语言，一切皆是对象，ruby不会把对象的内部结构暴露出来，所以会给array对象定义[]方法用来取值，而起名叫[]方法是为了能像其他语言一样的方式取值。
#ruby中还有其他符号运算符作为方法
arr + [4]  #+运算符
=> [1,2,3,4]
def arr.+(num)  #重写+运算符方法，使arr可以和数字相加(运算符重写)
  self.dup << num  #self.dup表示self的一个备份，这样不会改变self本身
end
arr + 6         #arr可以和数字相加
=> [1,2,3,6]
```

5.参数的默认值？

ruby中没有函数重载，所以可以有参数默认值

```ruby
# c++ / java
foo(a, b)
foo(a, b, c)   #不同的参数需要定义不同方法实现函数重载
#ruby
foo(a, b, c = 3)   #当只传入a，b时，c会传入默认值3
```

6.可以给ruby方法传入任意多参数

```ruby
def foo(a, *b, c)     #在参数前加*表示可以传入任意多参数

#*b会把传入的多个参数转换成一个array
def foo(a, *b, c)   #定义方法foo，参数里有*b
  p a
  p b
  p c
end
foo(1,2,3,4,5)    #调用方法foo
1
[2,3,4]           #2,3,4转换为了array
5
=> 5
```

当参数太多分不清参数代表啥的时候，可以使用hash作为参数传入

```ruby
foo(a, b, c, d, e, f)  =>  foo(hash)
def bar(h)      #定义一个方法bar，使用hash作为参数传入
  h.each do |key, value|
  	p key, value  #p(key, value)省略括号，大部分方法可以省略括号，使语句读起来更像英语
  end
end
=> :bar
bar(a:3, b:4)   #调用bar，使用{a:3, b:4}作为参数时可以省略{}
:a
3
:b
4
=> {:a=>3, :b=>4}
```

### 方法的调用

1.方法调用时什么时候可以省略括号？

大部分方法可以省略括号，使语句读起来更像英语，除非去掉括号会引起歧义

```ruby
p(key, value) =>  p key, value    #可以去掉括号
```

## 7.闭包（block, proc, lambda）

### 迭代器和块(iterator、block)

```ruby
[1,2,3].each do |elem|  #遍历array元素，其中each就是iterator
  p elem                #do...end就是block，do...end可以用{}代替
end
```

**block的几种调用方式：**

一般block中有多行代码时使用do...end形式：

```ruby
arr.each do |elem|
  foo(elem)
  bar(elem)
  baz(elem)
end
```

当block中只写一行代码时一般使用{}形式：

```ruby
arr.each { |e| p e }
```

除了上述两种block调用方式，还可以使用yield调用：

```ruby
def foo
  a = 2
  yield a     #yield表示把a数出来给block使用
end

foo { |a| puts a }
```

使用&符号和call方法调用block（将block作为方法的参数传入）：

```ruby
def foo(&block)
  a = 2
  block.call(a)
end

foo { |a| puts a}
```

运行.rb文件：

```ruby
$ ruby block.rb
```

irb中运行.rb文件：

```ruby
load 'block.rb'   #load可以直接运行rb程序，并且将方法load到内存
2
2
=> true
foo { |a| p a + 3}  #因为load后将foo方法load到了内存，所以可以在irb中直接调用该方法
5
=>5
```

### block的变量scope

block使用的变量可以从当前scope中取到

```ruby
foo { |b| p b; p a }
2
NameError: undefined local variable or method 'a' for main:Object...
#报错是因为变量a在block所在的scope中没有定义
a = 4   #定义变量a
foo { |b| p b; p a }  #在block所在的scope中定义变量a后不再报错
2
4
```

### block的flow control

block中的return、break、next语句并不是对于block的流程控制，而是对于block所在的方法

因为block本身不是方法，而是ruby的一种将代码包围起来的语法结构

```ruby
#例如在irb中调用block
foo { |a| p a; return }
2
LocalJumpError: unexpected return ...   #因为没有一个方法包含这个block，所以return报错
#以上相当于在irb中直接使用return语句
return
LocalJumpError: unexpected return ...
```

irb中正确在block中使用return的例子：

```ruby
def bar     #定义方法bar
  x = 3
  yield x
  p 'end of bar'
end
def foo     #定义方法foo
  p 'start of foo'
  bar { |x| return if x > 0 }     #方法foo中使用bar的block，block中使用return语句
  p 'end of foo'
end
foo      #调用方法foo
=> "start of foo"   #block直接return给foo
```

### Proc

Proc是类，而proc和lambda都是Proc的对象。

**proc**

当我们想使用对象来表示block时，可以使用proc

```ruby
def foo(&block)   #这里&block整体是一个block参数，而这里的block是一个proc，&符号将proc转换成了block
  a = 2
  block.call(a)
end
foo { |a| puts a}

arr = %w(a b c)           #定义一个字符串数组arr
arr.map(&:capitalize)     #方法capitalize转换成proc，再通过&符号转换成block
=> ["A", "B", "C"]
arr.map {|x| x.capitalize}  #相当于在map方法后接block
```

proc定义

```ruby
proc = Proc.new { |x| x*2 }
proc.class
=> Proc
```

**lambda**

lambda也可以表示一个block

```ruby
lambda =lambda { |x| x*2 }    #创建一个lambda
lambda.class
=> Proc
```

### proc和lambda的区别

一个中心思想：

虽然proc和lambda都可以表示block，但是proc的行为更像block，lambda的行为更像方法。

两个区别：

1.proc和lambda调用时可传入的参数数量有区别

```ruby
p = Proc.new { |x, y| p x, y}   #proc更像block可以接受任意数量参数
p.call(1)   #调用proc时只传一个参数，其它参数会传入默认值
1
nil
=> [1, nil]
p.call(1, 2)
1
2
=> [1, 2]
p.call(1,2,3)   #传入大于定义的参数时，多余的参数自动丢弃
1
2
=> [1, 2]
    
l = lambda {|x, y| p x, y}   #lambda更像方法，只能接受等于定义参数数量的方法
l.call(1)        #传入少于定义的参数时报错
l.call(1,2)      #传入等于定义的参数时正常调用
1
2
=> [1, 2]
l.call(1,2,3)    #传入大于定义的参数时报错
```

2.proc和lambda的control flow有区别

```ruby
p = Proc.new {|x| return if x>0}   #定义一个proc，里面有return
p.call(1)                          #调用时报错，因为proc像一个block，但是并没有方法包含，所以return报错
=> LocalJumpError: unexpected return

l = lambda {|x| return if x>0}   #定义一个lambda，里面有return
l.call(1)                        #可正常调用，因为lambda更像一个方法，所以可以return
=>nil
```

## 8.面向对象

类：就像一个容器，容器里定义了很多方法

对象：对象就想一个接收者，可以响应这些方法

### 类定义

定义一个空类：

```ruby
class Point    #类名使用驼峰命名，且是一个constant
end

p = Point.new    #创建一个对象p
p.class          #查看对象p的类
=> Point
p.methods(false)  #查看对象p的所有方法，false表示不查找继承自祖先的方法
=> []      #现在还没有方法
Point.ancestors    #查看Point类的祖先
=> [Point, Object, Kernel, BasicObject]

class、methods、ancestors等类或对象的方法统称为内省（introspection）
```

定义构造函数：

```ruby
class Point
  def initialize(x,y)      #定义构造函数initialize，创建对象时需要传入参数x,y
    # @x instance variable
    # @@x class variable
    # $x global variable
    # x local variable
    @x, @y = x, y          #定义实例变量x,y接收参数
  end
end
p = Point.new(2,3)

class Point                #给构造函数的参数设置默认值
  def initialize(x=0,y=0)      #定义构造函数initialize，创建对象时可以不传入参数x,y
    @x, @y = x, y          #定义实例变量x,y接收参数
  end
end
p = Point.new
```

取对象里的变量：

```ruby
#ruby对象对变量封装性好，只能通过定义getter、setter方法读写变量
class Point       
  attr_accessor :x    #ruby提供该helper方法自动创建了getter、setter方法（存取器）
  attr_reader :y      #ruby提供该helper方法自动创建了getter（读取器）
  def initialize(x=0,y=0)      
    @x, @y = x, y          #定义实例变量x,y接收参数
  end
end
p = Point.new(2)
p.x
=> 2
p.y
=> 0
```

定义实例方法：

```ruby
class Point       
  attr_accessor :x    
  attr_reader :y      
  def initialize(x=0,y=0)      
    @x, @y = x, y
  end
  
  def first_quadrant?   #判断x、y坐标是否为第一象限
    x > 0 && y > 0   #其实是self.x，self.y，读变量可以省略self或@
                     #写变量则不能省略self或@，需写出self.x或@x，否则会混淆x和@x
  end
    
  def +(p2)       #定义一个符号方法，两对象相加
    Point.new(x+p2.x, y+p2.y)
  end
end
p = Point.new(2,3)
p.first_quadrant?    #调用类方法
=> true
```

定义类方法：

```ruby
class Point       
  attr_accessor :x    
  attr_reader :y      
  def initialize(x=0,y=0)      
    @x, @y = x, y
  end
  
  def first_quadrant?   
    x > 0 && y > 0                 #实例方法中的self指instance本身
  end
    
  def +(p2) 
    Point.new(x+p2.x, y+p2.y)
  end
                     
  def self.second_quadrant?(x, y)  #1.这里的self指class本身，定义一个class方法
    x < 0 && y > 0
                                   #class方法中的self指class本身
  end
    
  class << self    #2.使用class << self可以批量定义类方法，这样方法名不需要写self
    def foo
    end
    def bar
    end
  end
    
end

Point.second_quadrant?(-2, 3)      #class方法只能由class调用，不能由实例调用
=> true
```

定义变量：

```ruby
#定义在实例方法外的变量是类变量，不是实例变量，通过类来访问
class Point       
  attr_accessor :x    
  attr_reader :y    
  ORIGIN = 2      #定义一个常量，常量并没有封装在类中，直接使用Point::ORIGIN可以取值
  @@origin = 0    #定义一个class variable
  def self.get_origin   #定义取类变量@@origin的类方法，只能定义类方法取类变量，不能用getter
    @@origin
  end
  def initialize(x=0,y=0)      
    @x, @y = x, y
  end
end
```

### 继承

ruby中的子类可以继承所有base class的方法，不管方法是不是public、protected、private。其他编程语言子类一般不能继承private方法。

```ruby
class Point3d < Point
  def initialize(x=0,y=0,z=0)
    super(x,y)    #在子类中使用super可以将指定参数传给父类该方法
                  #super后不指定参数，则将所有参数传给父类该方法
    @z = z
  end
end
p = Point3d.new
p.first_quadrant?   #调用继承自父类的方法
=> false
```

方法可见

|           | 可见（visibility）      | 继承(inheritance) | 可以被class内的对象调用(call on obj) |
| --------- | ----------------------- | ----------------- | ------------------------------------ |
| public    | class内部和外部都可调用 | yes               | yes                                  |
| protected | within                  | yes               | yes                                  |
| private   | within                  | yes               | no                                   |

```ruby
class Point       
  attr_accessor :x    
  attr_reader :y      
  def initialize(x=0,y=0)      
    @x, @y = x, y
  end
  
  private            #定义private方法1，private后定义的方法都是private
  def first_quadrant?   
    x > 0 && y > 0      
  end
  private :first_quandrant?    #定义private方法2，指定方法名为private
  
  def foo
    self.first_quadrant?   #使用self调用private方法，是不能成功执行的，去掉self则可以
  end
end

p = Point.new
p.first_quadrant?    #使用self调用private方法，是不能成功执行的
NoMethodError: private method 'first_quadrant?'
```

对象的常见内省方法：

```ruby
#1.is_a?和instance_of?
Point3d < Point   #Point3d继承自Point
p = Point3d.new
p.is_a? Point
=> true
p.is_a? Point3d
=> true
p.instance_of? Point   #只有p是Point3d的实例化对象才为真
=> false
p.instance_of? Point3d
=> true

if obj.instance_of? foo   #instance_of的常用场景
  do_something
else
  do_something_else
end
```

类的常用内省方法：

```ruby
#superclass和ancestors
Point3d.superclass  #该类的父类
=> Point
Point3d.ancestors   #该类的祖先们
=> [Point3d, Point, Object, Kernel, BasicObject]
```

### 模块(module)

模块就是一个方法库(library)，模块不能像class一样实例化，只能通过import的方式来调用模块里的方法。

1.模块的定义和类方法、实例方法定义

```ruby
module Helper   #定义一个module
  def self.distance(obj1, obj2)  #定义一个class method，只能通过Helper调用
    Math.sqrt((obj1.x - obj2.x) ** 2 + (obj1.y - obj2.y) ** 2)
  end
    
  def shift_right(x,y,z=0)       #定义一个instance method，可以include或extend导入到class中
      return x+3, y, z
  end
end
```

2.模块include和extend

```ruby
#include
class Point
  include Helper   #使用include将Helper模块导入，就可以将模块中的instance method变成类中的instance method，而模块中的类方法则无法在该类中使用
end
p = Point.new
p.shift_right(1,2,3)
p.distance(p1,p2)   #报错，因为distance方法是模块中的类方法

#extend
class Point
  extend Helper   #使用extend将Helper模块导入，就可以将模块中的实例方法变成类中的类方法
end
Point.shift_right(1,2,3)
```

3.使用hook将模块中的类方法、实例方法同时导入类中

```ruby
module Helper
  def shift_right(x,y,z=0)
      return x+3, y, z
  end
  
  module ClassMethods  #首先在Helper模块中定义一个ClassMethods模块，用来定义需要变成其他类的类方法的实例方法
    def distance(obj1, obj2)
      Math.sqrt((obj1.x - obj2.x) ** 2 + (obj1.y - obj2.y) ** 2)
  end
      
  def self.include(klass)  #再定义一个hook，当include Helper到一个类klass中时，自动extend ClassMethods模块到Klass类中
    klass.extend ClassMethods
  end
end

class Point
  include Helper 
end

Point.distance(p1,p2)   #Helper模块中的distance方法已经变成为Point类的类方法
```

常用内省方法：

1.类中include了哪些module?

```ruby
Point.included_modules
=> [Helper, Kernel]
```

2.类中包含指定module吗？

```ruby
Point.include?(Helper)
=> ture
```

3.类有哪些祖先？

```ruby
Pint.ancestors
=> [Point, Helper, Object, Kernel, BasicObject]    #包括父类和模块
```

4.类有哪些父类？

```ruby
Point.ancestors - Point.included_modules
=> [Point, Object, BasicObject]
```

5.类和模块的关系

```ruby
Point.class
=> Class
Helper.class
=> Module
Point.class.superclass   #Class的父类是Module
=> Module
Point.is_a? Module       #所以类都属于Module
=> true
```

### 单例化和方法查找

每个对象都有一个单例类(singleton_class)，一个对象的单例方法其实是该对象的单例类的instance method。

```ruby
str = 'hello world'   #定义一个String类的实例
def str.foo
  puts 'foo'
end
str.singleton_class    #输出str实例的单例类
=> #<Class:#<String:0x007fc939832e08>>
str.singleton_class.class   #输出str实例的单例类的类
=> Class                    #这说明单例类其实是Class的实例
str.singleton_class.instance_methods(false)   #查看str对象的单例方法(也就是单例类的实例方法)
=> [:foo]
```

类方法是类的单例类的实例方法，因为本质上ruby中所有都是对象，所以一个类其实也是对象，而类的类方法就是对象的单例方法。

| Object | Class  | methods | Method                  |
| ------ | ------ | ------- | ----------------------- |
| str    | String | str.foo | str.singleton_class#foo |
| Foo    | Class  | Foo.foo | Foo.singleton_class#foo |

所以，include一个模块和extend一个模块的区别本质就是：

```ruby
include Helper  #将模块的实例方法引入到类中，成为类的实例对象
extend Helper   #将模块的实例方法引入到类的单例类中，成为类的类对象
```

对象方法的调用顺序：

1.对象的单例方法

2.类中定义的实例方法

3.模块中定义的实例方法

4.祖先中定义的实例方法

5.method_missing defined in Kernel module

## 9.例外处理（Exception Handling）

### raise

产生一个报错

```ruby
#两种常用的raise使用方法
raise 'some error message'     #不指定Exception子类的话默认是RunTimeError
raise ArgumentError, 'error msg', caller   #指定Exception子类是ArgumentError，caller参数是将所有报错输出
```



### rescue

处理报错

```ruby
begin       #rescue一般用begin...end包起来
  ... 
rescue ArgumentError => e   #分错误类型来作为e处理
  ...
rescue TypeError => e
  ...
else                        #如果错误都不是上面的，则另外处理
  ...
ensure                      #ensure表示无论报不报错都处理
  ...
end
```

