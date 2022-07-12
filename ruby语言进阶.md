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
```

### 哈希常见方法

