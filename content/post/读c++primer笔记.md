---
title: "读c++primer笔记"
categories: [ "c++" ]
tags: [ "c++","读书笔记","笔记" ]
draft: false
slug: "cpp/reading-notes-of-cpp-primer"
date: "2019-06-25 17:11:00"
---

##  chapter01

- cerr

  标准错误，用于输出警告和错误信息

- clog

  输出运行时一般性信息

- 读取数量不定数据

  ~~~c++
  while(std::cin>>variable)
  {
      //code
  }
  ~~~

  判断的是std::cin对象的状态，输入遇到文件结束符时（end of file)或者无效输入，条件为假。

  Windows输入文件结束符：ctrl+z,再按enter或者return

  Unix输入文件结束符：ctrl+d



- for循环顺序

  ~~~c++
  for(1; 2; 4)
  {
      3
  }
  //重复2、3、4
  ~~~

  

- 转义序列

  ~~~c++
  '\12'	//换行符，八进制数
  '\x4d' 	//字母M,十六进制数
  
  //转义序列搭配八进制和十六进制，无十进制
  ~~~

## chapter02

- c++11新增long long类型

  ~~~c++
  long long //长整形，64bit
  ~~~

- 选择类型

  - 明知数值不可能为负数，选用无符号
  - 整数运算一般选int,超过范围用long long
  - 算术表达式不要使用char或者bool
  - 浮点型选double,与float速度其实无差别

- 给带符号类型赋值超出范围

  结果是未定义的

  ~~~c++
  signed char c = 256;//假设char占8bit，c的值是未定义的
  ~~~

- 字面值常量

  ~~~c++
  42;
  'a';
  "Hello World!";
  ~~~

- 初始化与赋值

  两者不一样，初始化是创建变量时赋予其一个初始值，而赋值的含义是把对象的当前值擦除，用新值代替

  **定义在任何函数体之外的变量初始化为0，在函数体内需自行初始化，否则变量的值就是未定义的**， 推荐所有变量应当初始化

- 变量声明与定义

  ~~~c++
  extern int i;	//声明
  int j;		    //声明并定义
  ~~~

  变量能且并且只能被定义一次，但是可以被多次声明

- 作用域

  ~~~c++
  int var = 42;	//全局变量
  int main()
  {
      int var = 0;	//重新定义，覆盖
      std::cout<<var<<endl;//输出0
      std::cout<<::var<<endl;//输出42
  }
  ~~~

- 指针

  &取地址符，*解引用符

  ~~~c++
  int *p1 = nullptr;	//等价于int *p1 = 0,c++11新标准
  int *p2 = 0;		//直接初始化p2为字面常量0
  
  //需先#include<cstdlib>
  int *p3 = NULL;		//等价于int *p3 = 0，使用预处理变量赋值，在cstdlib中有定义，值为0
  
  //尽量使用nullptr,避免使用NULL
  //建议初始化所有指针
  ~~~

  void* 指针可以存放任意对象的地址，但c++11不建议使用

  不能和别的指针作比较、作为函数的输入或者输出

  ~~~c++
  int* p;	//合法但是容易误导，不建议使用
  int* p1, p2; //误导，p1为 int*, p2为int
  int *p1, *p2;
  ~~~

- const

  ~~~c++
  const int i = get_size();	//正确，运行时初始化
  const int j = 0;			//正确，编译时初始化
  const int k;				//错误
  ~~~

  **默认状态下，const对象仅在文件内有效**

  文件共享const对象方法

  ~~~c++
  //file1.cc定义并初始化
  extern const int var = fcn();
  //file1.h
  extern const int var;//与file.cc中定义的var为同一个
  ~~~

- 常量表达式

  值不会改变、编译过程就得到计算结果的

  ~~~c++
  const int var1 = 20;	//var1是常量表达式
  const int var2 = var1 + 1;//var2是常量表达式
  int var3 = 10;			//var2不是常量表达式
  const int var4 = get_size();//var4不是常量表达式
  
  //采用constexpr验证是否为常量表达式,c++11新增
  constexpr int var5 = 9；	//是
  constexpr int var6 = var5 + 1; //是
  constexpr int var7 =  get_size();//除非get_size()为一个constexpr函数，否则此语句不正确
  //所以认定变量为常量表达式,建议采用constexpr
  
  //const修饰的是类型，constexpr修饰的是用来算出值的那段代码--轮子哥
  ~~~

- 类型别名

  ~~~c++
  //方法一
  typedef double wages;	//wages与double同义
  typedef wages base, *p;	//base与double同义， p与double*同义
  
  //方法二（c++11新标准)
  using SI =  Scales_item;//SI与Scales_item同义
  ~~~

- auto

  auto定义变量必须初始化

  ~~~c++
  int i = 0, &r = i;
  auto a = r;//a为整数，r为别名
  ~~~

- decltype

  返回操作数的类型，可以推断表达式返回类型但不用该表达式的值初始化变量

  ~~~c++
  decltype(f())	sum = x;	//sum的类型为函数f返回值的类型
  const int ci = 0;
  decltype(ci) x = 0;		//x类型就是const int
  ~~~

- 头文件预处理

  头文件通常包含只能被定义一次的实体，如类、const和constexpr变量

  头文件一旦改变，相关源文文件必须重新编译以获取更新过的声明

  ~~~c++
  #ifndef HEAD_NAME_H
  #define HEAD_NAME_H
  	//防止重复包含
  #endif
  ~~~

## chapter03

- 命名空间

  ~~~c++
  using namespace namepace_name;//如
  using namespace std;
  using namespace_name::funciton(object); //如
  using std::cin;
  ~~~

  头文件不应该使用using声明，可能会引起命名冲突

- getline()

  读取一整行，连同换行符也会读取进来，但是换行符不会存到string对象，如果一开始输入就是换行符，那么得到的就是一个空的string对象

- size()

  一般返回的是usigned类型，一个表达式中如果有size()函数，就不使用int

- 由于某些历史原因和为了与c兼容，c++中的字符串字面值并不是标准库中的string对象。字符串字面值与string是不同类型

- 使用cname形式头文件代替name.h形式头文件

- 新标准for循环

  ~~~c++
  //语法形式
  for(declaration : expression)
      statement
  //例
  string str("some string");
  for(auto c : str)
      std::cout<<c<<std::endl;
  ~~~

- vector

  ~~~c++
  vector<string> s1{"a", "b", "c"};	//正确
  vector<string> s2("a", "b", "c");	//错误
  vector<int> ivec(10, -1);	//10个int元素，都为-1
  vector<string> svec(10, "hi!");	//10个string元素，都为"hi!"
  //默认初始化
  vector<int> ivec(10);	//10个int元素都为0
  vector<int> ivec{10};	//1个int元素,值为10
  vector<string> svec(10);	//10个空的string对象
  vector<string> svec{10};	//10个空的string对象
  ~~~

- 迭代器

  如果容器为空，则begin和end返回同一个迭代器，都是尾后迭代器

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/cpp/reading-notes-of-cpp-primer.html