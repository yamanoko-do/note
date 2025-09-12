---
created: 2025-01-30 09:07
modified: 2025-01-30 09:07
tags:
---


# Conda
Conda 是一个跨平台、跨语言的包管理与环境管理工具，通过创建隔离的独立环境，解决不同项目的依赖冲突问题。由于部分 Python 包是用 C 语言实现的(如NumPy)，Conda 需要模拟某些系统目录，以支持它们的运行和编译，例如：
- **`bin/`**：包含可执行工具，如 `python`、`pip`，以及 `gcc`、`cmake` 等用于构建 C/C++ 扩展的工具。
- **`lib/`**：提供共享库（`.so` / `.dll`），供 Python 运行时调用
- **`include/`**：存放头文件（`.h`），用于编译 C 语言实现的 Python 扩展
## 安装
conda有多种版本，官方的annaconda和更精简的miniconda，使用社区源的[Miniforge](https://conda-forge.org/miniforge/)、和更快的[Mamba](https://mamba.readthedocs.io/en/latest/installation/mamba-installation.html)

- [安装miniconda](https://www.anaconda.com/docs/getting-started/miniconda/install#linux-terminal-installer)
- [卸载](https://blog.csdn.net/xdg15294969271/article/details/119858168):`conda init --reverse bash`,然后删除conda路径即可
- 关闭conda的自动激活环境：
	```shell
	conda config --set auto_activate_base false
	```

## 配置

### channel
软件包是从channel中下载的，官方的`defaults`包含了3个channel：`main`(大部分软件包)、`r`(R语言相关库)、`msys2`(为window提供linux的工具链)，此外还有由开源社区维护的频道`conda-forge`
- 查看配置文件路径和内容：`conda config --show-sources`
- 显示url信息：`conda config --set show_channel_urls true`
- 清除现有channel配置：
```shell
conda config --remove-key channels
conda config --remove-key default_channels
conda config --remove-key custom_channels
```
- 添加channel：参考[conda国内源大全](https://zhuanlan.zhihu.com/p/584580420)，只有南科大有nvidia的镜像
```shell
conda config --add channels defaults
conda config --add default_channels https://mirrors.sustech.edu.cn/anaconda/pkgs/main
conda config --add default_channels https://mirrors.sustech.edu.cn/anaconda/pkgs/msys2 #为windows提供shell环境
conda config --set custom_channels.pytorch https://mirrors.sustech.edu.cn/anaconda/cloud
conda config --set custom_channels.nvidia https://mirrors.sustech.edu.cn/anaconda-extra/cloud
```
- 更新索引缓存:
```shell
conda clean -i
conda clean --all#清除全部缓存，包括下载的pkg
```

- else：不建议在powershell使用加载conda，不然很慢
	[windows打开powershell报错](https://blog.csdn.net/frighting_ing/article/details/128860838)：无法加载文件 ，因为在此系统上禁止运行脚本。通过更改的执行策略来解决
	  ```shell
	  Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
	  ```
	  此外C:\Windows\System32\WindowsPowerShell\v1.0与C:\Users\Yama\Documents\WindowsPowerShell下都有profile.ps1，为了加快终端开启速度保留后面目录的conda初始化文件即可

## 命令
[conda命令大全](https://blog.csdn.net/chenxy_bwave/article/details/119996001)
- 创建环境：conda create -n env_name python=3.9
- 复制已有环境：conda create -n 新环境名称 --clone 已有环境名称
- 激活xx环境：conda activate xx
- 退出环境：conda deactivate
- 删除环境：conda remove -n env_name --all
- 迁移环境(未验证该命令有效性)：
	```shell
	conda activate myenv
	conda env export > environment.yaml
	conda env create -n mynewenv -f environment.yaml
	```
- 设置虚拟环境存储位置：
	1. 查看当前虚拟环境所在位置：`conda info --envs`
	2. 添加/删除虚拟环境虚拟环境的存储位置：`conda config --add/remove envs_dirs D:\conda_envs`
	3. [conda环境迁移](https://zhuanlan.zhihu.com/p/661869072)：迁移envs路径下的内容即可

# pip
pip是python的包管理工具
- pip安装的Python包在：`/usr/local/lib/python3.X/dist-packages/`中,也就是解释器的dist-packages中
- pip包的格式：`.whl` 、`.tar.gz` 或 `.zip`
### 配置
- 设置源：配置文件有3个级别、系统(global)、用户(user)、虚拟环境(site)，优先级顺序：`site` > `user` > `global`
1. 查看配置文件路径和内容：
	```shell
	pip config debug
	```
2. [pip源汇总](https://blog.csdn.net/u014451778/article/details/146163709)：
	- 清华源：qinghua
	```bash
	 -i https://pypi.tuna.tsinghua.edu.cn/simple
	```
	- 使用：
	```shell
	# 临时使用
	pip install numpy -i url
	# 永久配置
	pip config set global.index-url url
	```
	- 或者通过环境变量临时启用：qinghuahuanjing
```bash
export PIP_EXTRA_INDEX_URL=https://mirrors.aliyun.com/pypi/simple/
```
3. 查看生效的源：`pip -v config list`

### 命令
- 查看本地安装的包：
	```shell
	pip list
	```
- 查看特定包的信息：
	```shell
	pip show <package_name>
	```
- 查看某个包的可用版本：
	```shell
	pip index versions 包名
	```
- pip下载包到本地（不安装）：
	```shell
	pip download <package_name> -d <download_directory>
	```
- 网络中断时，自动尝试恢复中断的下载，最多重试100次
	```shell
	pip install --resume-retries 100 <package_name>
	```
	或者使用：
	```shell
	PIP_TIMEOUT=60 PIP_RETRIES=100 pip install -r requirements.txt
	```
- 从依赖列表安装包：
	```shell
	pip install -r requirements.txt
	```
- 寻找兼容 B=1.5.0 的 A 版本
	```
	pip install A B==1.5.0
	```
- 安装A时不要处理它的依赖
	```
	pip install A --no-deps
	```
- pip show 显示的“包名”和 import 使用的“模块名”为什么可能不同？它们到底指的是什么？
	- 包名：在Python包索引（PyPI）里注册的项目名称。当你打算安装一个包时，得使用这个名称，就像这样：
		```bash
		pip install requests
		```
		这里的 `requests` 就是包名，它是项目对外的称呼，也是用户在安装时需要输入的名称。
	- 模块名：在代码里引入功能时实际使用的标识符，比如：
		```python
		import requests
		```
		这里的 `requests` 就是模块名，它指向的是Python环境中可导入的对象。
# Python
- [python学习路线](https://www.bilibili.com/video/BV1y3411P73s/?spm_id_from=333.337.search-card.all.click&vd_source=ca17e8e44a1bddd49f39d4bc7c4ad336)
- [python 官方教程](https://docs.python.org/zh-cn/3.10/tutorial/index.html)
- [Python 简介 - W3Schools 中文教程](https://w3schools.org.cn/python/python_intro.asp)
- [一个看起来不错的付费视频课程，有基础、进阶、高级三个集合](https://www.bilibili.com/cheese/play/ep240314?csource=common_hp_history_null&t=15&spm_id_from=333.337.top_right_bar_window_history.content.click)
该python笔记尚不包含以下内容：
- c如何为python写库

- 多线程
- 生成器和[迭代器](https://w3schools.org.cn/python/python_iterators.asp)
- 图片的处理
	```python
	from PIL import Image
	image = Image.fromarray(image_array)
	image.save(output_path)
	```
## 基础知识
### 变量命名规范
[和python开发人员用同一套命名系统](https://www.bilibili.com/video/BV1RT411G73z/?spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=ca17e8e44a1bddd49f39d4bc7c4ad336)
- 模块和包：小写字母下划线，`data_processing.py`
- 常量：大写字母下划线，`UPPER_UDERSCORE`
- 变量、function、method：小写字母下划线，`lower_underscore`
- 类名：驼峰命名，`CamelCase`



- 单下划线：`_`表示占位符，表示那些不会被使用的变量
- 尾部单下划线：避免与 Python 的关键字或内置名称冲突，如`str_`
- 前置单下划线：`_name` 表示弱私有，类外仍可使用
- 前置双下划线：`__name` 表示强私有，类外不可使用
- dunderscore：`__name__ `表示魔法函数，不要使用
### 变量作用域
变量的作用域指的是变量的可访问范围。变量只能在其被定义的特定区域内被访问和使用
1. **局部作用域（Local, L）**：函数内部定义的变量，只能在函数内部访问。函数执行结束后，这些变量就会被销毁。
2. **闭包作用域（Enclosing, E）**：在嵌套函数中，外层函数的变量作用域。内层函数可以访问这些变量，即使外层函数已经执行完毕。
3. **全局作用域（Global, G）**：模块（文件）顶层定义的变量，可以在整个模块内访问。
4. **内置作用域（Built-in, B）**：Python 内置的变量和函数，如`print`、`len`等，在任何地方都可以直接使用。
下面是一个例子：
```python
# 全局作用域变量
global_var = "全局变量"

def outer_function():
    # 闭包作用域变量
    enclosing_var = "闭包变量"
    
    def inner_function():
        # 局部作用域变量
        local_var = "局部变量"
        
        # 内部函数可以访问所有外层作用域的变量
        print(local_var)     # 输出：局部变量
        print(enclosing_var) # 输出：闭包变量
        print(global_var)    # 输出：全局变量
    
    # 外部函数无法访问内部函数的局部变量
    # print(local_var)  # 报错：NameError
    
    return inner_function

# 创建闭包
closure = outer_function()
closure()  # 调用内部函数，仍然可以访问闭包变量

# 修改全局变量
def modify_global():
    global global_var
    global_var = "修改后的全局变量"

modify_global()
print(global_var)  # 输出：修改后的全局变量
```
### 带类型声明的赋值
带类型声明的赋值是指在变量赋值时显式指定变量的类型,可以提高代码的可读性和可维护性
```python
# 声明一个整数类型的变量
x: int = 10
# 声明一个字符串类型的变量
name: str = "Alice"
# 声明一个浮点数类型的变量
pi: float = 3.14
# 声明一个布尔类型的变量
is_active: bool = True
# 声明一个整数列表
numbers: list[int] = [1, 2, 3, 4, 5]
# 声明一个字典，键为字符串，值为整数
ages: dict[str, int] = {"Alice": 25, "Bob": 30}
# 声明一个元组，包含一个整数和一个字符串
person: tuple[int, str] = (1, "Alice")
# 实例化一个对象
brid: Animal = Animal(species="birds")
```
### 注释写法
单行：`#`
多行：`'''`
### 运算符号
- 逻辑算符：与`and`、或`or`、非`not`
- 算术运算符：`+`、`-`、`*`、`/`、`%`、`**`、`//`(除法并取整)
- 自操作：`+=`、`-=`、`*=`、`/=`、`%=`...
- 判断：`is`(比较内存地址)、`in`、`==`、`not`
### 内置函数
- `type()`：
  1. `type()`返回一个变量的类型
  2. `type(class_name, bases, dict)`：动态地创建类，类名，元组，表示新类的父类，字典，表示类的命名空间，包含类的属性、方法等
- `len()`：返回容器类型的长度
- `max()`和`min()`：返回容器中的最大值和最小值
- `range`：range(stop)、range(start, stop)、range(start, stop, step)返回可迭代的range对象，参数只支持`int`类型
- `iter`：用于从可迭代对象中获取迭代器(迭代器是一个对象，它实现了 `__next__()` 方法)
- 类型转换：`dict()`、`list()`、`tuple()`、`set()`、`str()`、`int()`、`float()`
- `id()`：返回变量的地址
- `eval()`：将字符串作为表达式（表达式是一个可以计算并返回值的代码片段）来执行，并返回表达式的结果
- `exec()`：执行语句（语句是执行某些操作的代码，例如赋值、循环、条件判断等），无返回值
- `repr()`：返回一个对象的字符串表示形式。这个返回的字符串应当是一个有效的 Python 表达式，理想情况下可以用于重新创建该对象
- zip：用于将多个可迭代对象（如列表、元组等）中的元素打包成一个个元组，然后返回由这些元组组成的迭代器
```python
numbers = [1, 2, 3]
letters = ['a', 'b', 'c']
zipped = zip(numbers, letters)
print(type(zipped))  # 输出：<class 'zip'>
# 转换为列表查看结果
print(list(zipped))  # 输出：[(1, 'a'), (2, 'b'), (3, 'c')]

```

### 关键字
#### 控制流
- 条件语句：`if`, `elif`, `else`
	```python
	if test_expr1: #必选
		statement1#必选
	elif test_expr2: #可选
		statement2
	else: #可选
		statement3
	```
- 循环语句：`for`, `while`,`break`, `continue`，`pass`
	```python
	#while
	while test_expr:
		statement1
	else:#可选,在正常离开循环时执行（break是非正常离开循环）
		statement2
	#for
	for target_var in iter_obj:
		statement1
	else:#可选,在正常离开循环时执行（break是非正常离开循环）
		statement2
	#break:立即终止当前循环（完全跳出循环），继续执行循环外的代码
	#continue:跳过当前循环的剩余代码，直接进入下一次循环迭代
	```
#### 异常处理
- 异常捕获：`try`, `except`, `finally`
	```python
	try:#尝试执行可能引发异常的代码
		result = 10 / 0  # 引发 ZeroDivisionError
	except ZeroDivisionError as e:#捕获并处理异常
		print(f"捕获到异常: {e}")
	finally:无论是否发生异常，都会执行的代码（通常用于释放资源）
		print("执行 finally 块")
	```
- 抛出异常：`raise`
	```python
	raise ExceptionType("异常信息")
	```
	- 内置异常类别有：
	```python
	TypeError
	KeyError
	ValueError
	ZeroDivisionError
	KeyboardInterrupt
	```
	- 自定义异常：
	```python
	class MyCustomError(Exception):
	    pass
	
	def check_age(age):
	    if age < 0:
	        raise MyCustomError("年龄不能为负数")
	
	check_age(-5)
	```
- 断言：`assert`
	```python
	#用于断言某个条件是否为真。如果条件为真，则程序正常运行；如果条件为假，则会抛出 `AssertionError` 异常
	assert condition, message
	x = 10
	y = 20
	assert x < y, "x 应该小于 y"
	```
#### 上下文管理
用于管理资源，确保资源在使用后正确释放：`with`,`as`

```python
	with 上下文管理器 as 变量:  #上下文管理器必须实现 __enter__ 和 __exit__ 方法
	    statement1  # 使用资源的代码块
	# 资源会在 with 块结束后自动释放
	
	# 示例
	with open('example.txt', 'r') as file:  # 自动管理文件资源
	    content = file.read()
```


## 内置数据类型
python有六大标准数据类型，数字、字符串、列表、元组、字典、集合，使用`type()`可以查看一个变量的数据类型
### 数字
- 整数（int）：表示整数，可以是正整数、负整数或零，没有大小限制，只要计算机的内存足够大。例如 `123`、`-456`、`0` 等。
- 浮点数（float）：用于表示小数，也可以表示正负无穷大和非数字（NaN）。例如 `3.14`、`-2.5`、`1.23e-10`（科学计数法表示的浮点数）等。
- 复数（complex）：由实部和虚部组成，用 `a + bj` 的形式表示，其中 `a` 是实部，`b` 是虚部，`j` 是虚数单位。例如 `3 + 4j`、`-2.5 - 1.2j` 等
- 生成随机数：
```python
import random  
print(random.randrange(1, 10))
```

### 字符串
[Python之字符串操作大全（29种方法）](https://blog.csdn.net/yueguang8/article/details/136682871)
Python语言中的字符串是一种不可变序列
- 创建：字符串用单引号、双引号、三引号（可以包含换行符）作为定界符。
```python
str_a="开始施法,"\
"这是第一句咒语,"\
"这是第二句咒语"
```
- 查：
```python
#索引
str_a[0]#取出一个字符

#遍历
for index,char in enumerate(str_a):
	print(index,char)
```
- 方法：
```python
#split方法将字符串按sep分割成多个子字符串，分割maxsplit次并返回一个列表，-1表示不限制分割次数
str.split(sep=None, maxsplit=-1)
s = "apple,banana,cherry"
result = s.split(",")  # 使用逗号作为分隔符
print(result)#['apple', 'banana', 'cherry']
```
### 列表
列表是 Python 中的一种序列类型，属于容器类型，可以容纳多种数据类型。列表是可变对象，支持动态修改其内容。
- 创建：
```python
list_empty1=[]
list_empty2=list()
list_a=["item1","item2","item3"]
list_b=["item4","item5"]
list_c=[i for i in range(0,10)]
```
- 方法：增删改查
  1. 增：
	```python
	#1.增
	#增加一个元素到列表末尾
	list_a.append("new_item1")
	#在索引之前插入一个元素
	list_a.insert(index,"new_item2")
	#在列表的末尾追加另一个列表
	list_a.extend(list_b)
	```
  2. 删：
	```python
	#移除列表中的一个元素,默认为最后一个元素,并返回这个元素
	list_a.pop(index)
	#移除列表中与某值匹配的第一个元素
	list_a.remove("item2")
	#删除列表中所有元素，但保留列表对象
	list_a.clear()
	```
  3. 查：
```python
#切片和索引
#查询某一个元素出现的个数
list_a.count()
```
  4. 迭代
```python
for item in list_a:
	pass

for index, item in enumerate(list_a):
    print(f"index:{index}, item:{item}")
```
### 元组
元组是 Python 中的一种序列类型，属于容器类型，可以容纳多种数据类型。元组是不可变对象，创建后其内容无法修改
- 创建：
```python
tuple_empty1=()
tuple_empty2=tuple()
tuple_a=("a","b","c")
```
- 迭代
```python
for item in tuple_a:
	pass
```
- 查看长度
```python
len(tuple_a)
```
### 字典
字典是 Python 中的一种映射类型，属于容器类型，以键值对的形式存储数据。字典是可变对象，它的元素的值的类型不限，它的元素的键类型是不可变类型
- 创建
```python
dict_empty1=dict()
dict_empty2={}
dict_a={"k0":"v0"}
dict_a={i : i*2for i in range(0,10)}
```
- 方法：增删改查
  1. 增：
	```python
	#1.增
	dict_a["k1"]="v1"
	#合并两个字典，原地更新dict_a的值
	dict_a.update({"k2":"v2"})
	#设置键的默认值，不会更改已存在键的值
	dict_a.setdefault("k3","v3")
	```
  2. 删：
	```python
	#从字典中删除k1并返回该元素的值
	dict_a.pop("k1")
	```
  3. 查：
```python
	#返回字典的keys
	dict_a.keys()
	#返回字典的values
	dict_a.values()
	#返回字典的items
	dict_a.items()
	#迭代字典
	for key, value in dict_a.items():
	    print(f"{key}: {value}")
```
### 集合
`set`类型是集合内元素值唯一、元素值不可变的无序集
- 创建
```python

set_a={i for i in range(0,10)}
```
### 切片
切片用于从序列类型中提取子序列。它通过指定索引范围来实现对数据的快速访问和操作。支持切片操作的有：列表、元组、字符串等，该操作返回的是copy而不是view，不影响原对象
- 基本语法为`list_a[start : end : step]`,左闭右开
	- start：起始索引，为负值时表示从末尾开始计数，默认0
	- end：终止索引，默认为该维度的长度
	- step：步长，默认1

```python
my_list = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
# 提取第 2 到第 5 个元素
print(my_list[2:5])  # 输出 [2, 3, 4]
# 提取前n个元素
print(my_list[:n])
# 提取后n个元素
print(my_list[-n:])
# 提取所有元素，步长为2
print(my_list[::2])  # 输出 [0, 2, 4, 6, 8]
# 提取前n个元素，步长为2
print(my_list[:n:2])
# 逆序
print(my_list[::-1])  # 输出 [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

#对于多个维度，用逗号分隔
matrix = [
    [1, 2, 3, 4],
    [5, 6, 7, 8],
    [9, 10, 11, 12]
]
#选取所有行，对第二个维度（列）进行切片，从索引 0 开始步长为2
matrix[:, ::2]

```

## 函数
### 参数
在 Python 中，函数的所有带有默认值的参数（即默认参数）必须位于不带默认值的参数（即位置参数）之后。
- **`*args`**：接收任意数量的位置参数，打包为元组。
- **`**kwargs`**：接收任意数量的关键字参数，打包为字典。
	```python
	def print_args(*args, **kwargs):
	    print("位置参数:", args)
	    print("关键字参数:", kwargs)
	print_args(1, 2, 3, name="Alice", age=25)
	```
### 匿名函数
用于定义一些简单的、一次性使用的函数
```python
lambda arg1,arg2,...argN: expression using args
# 普通函数，提取一个字符串最后出现的数字
def extract_last_number(s):
    # 使用正则表达式找到所有数字
    numbers = re.findall(r'\d+', s)
    # 如果找到数字，返回最后一个数字
    if numbers:
        return numbers[-1]
    else:
        return None
# 匿名函数
extract_last_number = lambda s: re.findall(r'\d+', s)[-1] if re.findall(r'\d+', s) else None
#调用
last_number = extract_last_number(s)
print(f"锤子的柄部对应idx={last_number}")  # 输出: 锤子的柄部对应idx=3
```
### 返回值
- return：正常返回
- yield：生成器函数专用，生成器对象可以通过 `next()` 函数或 `for` 循环来逐个获取值
### 闭包
闭包是指 内部函数访问了 外部函数的局部变量，并且 外部函数返回了这个内部函数，这样即使外部函数执行完毕，内部函数仍然可以访问外部变量
闭包的三个条件：
1. 必须有嵌套函数（函数内部定义函数）
2. 内部函数引用了外部函数的变量
3. 外部函数返回了内部函数，而不是直接执行它
```python
def outer_function(x):
    def inner_function(y):
        return x + y  # 内部函数访问外部函数变量 x
    return inner_function  # 返回内部函数

closure = outer_function(10)  # closure 现在是 inner_function
print(closure(5))  # 输出: 15
```
### 函数注解
函数注解用于为函数的参数和返回值添加元数据，注解不会影响函数的执行，仅用于提供额外的信息
- 在函数定义中，使用 `:` 为参数添加注解
- 使用 `->` 为返回值添加注解
```python
#单个返回值注解
def greet(name: str = "name") -> str:
    return f"Hello, {name}"
print(greet("Alice"))  # 输出: Hello, Alice
#多个返回值注解，使用Tuple
from typing import Tuple
def get_user_info() -> Tuple[str, int, bool]:
    name = "Kimi"
    age = 30
    is_active = True
    return name, age, is_active
#泛型类型注解，Optional[Type]表示该类型是可选的，可以是 `Type` 或者 `None`
from typing import Optional 
def get_number() -> Optional[int]:
	pass
```
### 文档字符串
文档字符串是函数、类或模块的说明性文本，用于描述其功能、参数、返回值等信息
文档字符串用`'''`或`"""`开始和结束，文档字符串有最常见的风格是Google 风格和 NumPy风格
```python
#Google风格
def divide_and_remainder(a, b):
    """
    计算两个数的商和余数。
    Args:
        a (int): 被除数。
        b (int): 除数。
    Returns:
        tuple: 包含两个元素的元组，分别是：
            - quotient (int): 商，表示 a 除以 b 的整数结果。
            - remainder (int): 余数，表示 a 除以 b 的余数。
    """
    quotient = a // b
    remainder = a % b
    return quotient, remainder
#Numpy风格
def add(a, b):
    """
    Adds two numbers and returns the result.
    Parameters
    ----------
    a : int
        The first number.
    b : int
        The second number.
    Returns
    -------
    int
        The sum of a and b.
    """
    return a + b
```
## 类
- 封装：将数据和方法隐藏在一个类中，只暴露必要的接口，保护内部实现细节
- 继承：子类可以继承父类的属性和方法，实现代码复用
- 多态：子类可以重写父类方法，实现不同的行为
### 属性
- **实例属性**（实例变量）：在 `__init__` 方法中定义，属于具体对象
- **类属性**（类变量）：在类体中定义，属于整个类，所有实例共享
```python
class Cat:
    species = "Feline"  # 类属性，所有实例共享
    def __init__(self, name):
        self.name = name  # 实例属性，每个对象独立
    def show_class_attribute(self):#类属性可以通过类名和实例访问
        print(Cat.species)# 通过类名访问
        print(self.species)# 通过实例访问
    def show_instance_attribute(self):#实例属性必须通过实例访问。
        print(self.name)

cat1 = Cat("Kitty")
cat2 = Cat("Tom")
#类属性可以通过类名直接访问
Cat.species
#类属性也可以通过实例访问(不推荐)
cat1.species
#实例属性必须通过实例
cat2.name
#所有实例共享类属性
print(cat1.species, cat2.species)  # 输出: Feline Feline
Cat.species = "Big Cat"
print(cat1.species, cat2.species)  # 输出: Big Cat Big Cat
```
### 方法
#### 实例方法
默认接受一个参数 `self`，指向调用该方法的实例对象，通过类的实例对象调用
```python
class Dog:
    def __init__(self, name):
        self.name = name

    def bark(self):
        print(f"{self.name} is barking!")

my_dog = Dog("Buddy")
my_dog.bark()  # 输出: Buddy is barking!
```
#### 类方法
类方法使用 `@classmethod` 装饰器定义，第一个参数是 `cls`（类本身）, 类方法可以访问类属性，但不能访问实例属性。
```python
class Dog:
    species = "Canine"
    @classmethod
    def get_species(cls):
        return cls.species

print(Dog.get_species())  # 输出: Canine
dog=Dog()
print(dog.get_species())
```

#### 静态方法
静态方法使用 `@staticmethod` 装饰器定义。它们不接受默认参数（如 `self` 或 `cls`），类似于普通函数，可以通过类或实例调用
```python
class Dog:
    @staticmethod
    def bark():
        print("Woof!")

	def say(self):
		Dog.bark()#推荐
		self.bark()#也可以


Dog.bark()  # 输出: Woof!
```
#### 魔术方法
[【python】魔术方法大全（一）——基础篇_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1b84y1e7hG/?spm_id_from=333.337.search-card.all.click&vd_source=ca17e8e44a1bddd49f39d4bc7c4ad336)
- `__init__`：用于在创建对象时初始化实例
- `__call__`：允许一个类的实例像函数一样被调用
- `__repr__`：结果应尽可能接近重建对象的有效代码，若无法重建，返回包含关键信息的描述性字符串
- `__str__`：定义对象的字符串表示（用于 `print()/str()`，未定义时回退到`__repr__`）
- `__len__`：定义对象的长度（用于 `len()`）
- `__getitem__`：定义对象的索引访问（用于 `obj[key]`)
- `__setitem__`：`obj[index] = value`
- `__iter__`：`for x in obj:`
- `__next__`：`for x in obj:`
- 运算符重载
- 比较运算符重载
### 继承与多态
#### 继承
- 是一种面向对象编程（OOP）的概念。它允许一个类（称为子类或派生类）继承另一个类（称为父类或基类）的属性（字段）和方法
```python
class Animal:
	def __init__(self.name):
		self.name = name
	
    def speak(self):
        print(f"{self.name} makes a sound")

# 继承 Animal
class Dog(Animal):  
	def __init__(self, name): 
		super().__init__() # 调用父类构造方法
    def speak(self):  # 方法重写
        print("Dog barks")

class Cat(Animal):
    def speak(self):
        print("Cat meows")


```
#### 多态
多态是指允许不同类的对象对同一消息做出响应，即一个接口，多种实现。它有两种主要表现形式：编译时多态（方法重载）和运行时多态（方法覆盖）
- 方法覆盖（Override）：子类重新实现父类中已有的方法
	```python
	# 多态
	animals = [Dog(), Cat(), Animal()]
	for animal in animals:
	    animal.speak()  
	```
- 方法重载（Overload）：在同一个类中定义多个同名但参数列表不同的方法
	python是动态语言不支持方法重载，但是可以变相实现类似效果
	1. 使用默认参数：
		```python
		class Calculator:
		    def add(self, a, b, c=0):  # 通过默认参数实现不同参数数量
		        return a + b + c
		
		calc = Calculator()
		print(calc.add(1, 2))     # 输出: 3 (使用两个参数)
		print(calc.add(1, 2, 3))  # 输出: 6 (使用三个参数)
		```
	2. 使用可变参数：
		```python
		class Printer:
		    def print_data(self, *args):
		        if len(args) == 1:  # 根据参数数量分支处理
		            print(f"打印单数据: {args[0]}")
		        else:
		            print(f"打印多数据: {args}")
		
		printer = Printer()
		printer.print_data(10)          # 打印单数据: 10
		printer.print_data('a', [1,2])  # 打印多数据: ('a', [1, 2])
		```
	3. 使用类型检查：
		```python
		class Converter:
		    def convert(self, data):
		        if isinstance(data, int):   # 检查类型
		            return hex(data)
		        elif isinstance(data, str):
		            return data.upper()
		        else:
		            raise ValueError("不支持的类型")
		
		conv = Converter()
		print(conv.convert(255))    # 输出: 0xff
		print(conv.convert("hello"))# 输出: HELLO
		```

### 装饰器
装饰器本质上就是一个闭包，装饰器是一个函数，它接受一个函数作为参数，并返回一个新的函数。
- `@property`：用于将方法转换为只读属性
- `@staticmethod`：定义静态方法，不依赖实例
- `@classmethod`：定义类方法，第一个参数是 `cls`
- `@overload` ：与多态函数的声明配合使用，不提供实现，声明后需要提供一个实际的函数实现，其仅用于帮助静态类型检查工具提供类型提示​，不会影响 Python 的运行时行为。
	```python
	from typing import overload
	@overload
	def func(x: int) -> int: ...
	@overload
	def func(x: str) -> str: ...
	
	#​​实际的函数实现
	def func(x):
	    if isinstance(x, int):
	        return x * 2
	    elif isinstance(x, str):
	        return x.upper()
	```
- `@lru_cache`：根据函数的参数来区分(可哈希)不同的调用，如果输入的参数一致相同，函数会直接返回缓存的结果，而不会重新计算
**自定义函数装饰器**：
```python
def decorator(func):
    def wrapper():
        print("执行前")
        func()  # 调用原函数
        print("执行后")
    return wrapper  # 返回新函数

@decorator  # 等价于 func = decorator(func)
def say_hello():
    print("Hello, World!")

say_hello()
# 输出：
# 执行前
# Hello, World!
# 执行后
```
- `@decorator` 使 `say_hello` 变成 `wrapper`，但 `wrapper` 仍然记住 `func = say_hello`，即 **闭包的作用**。
- 每次调用 `say_hello()`，实际上执行的是 `wrapper()`。
## 模块和包
### module
模块就是一个 Python 代码文件，模块名就是文件名（去掉 `.py` 后缀）,模块可以包含函数、类、变量和可执行代码
- 模块的name属性：当 Python 文件被直接运行时，`__name__` 变量会被设置为 `"__main__"`。通过 `if __name__ == "__main__"` 判断，可以将某些代码块指定为仅在该模块作为主程序运行时执行。这使得模块既可以被直接运行，也可以被其他模块导入，而不会在导入时执行一些仅在主程序中需要运行的代码
```python
# mymodule.py
def my_function():
    print("这是一个函数")

if __name__ == "__main__":
    print("这是主程序")
    my_function()
```
### package
包是一个包含多个模块的目录，目录中须包含一个 `__init__.py` 文件（即使是空文件），用于标识该目录是一个包，`__init__.py` 文件可以控制包的初始化行为，例如定义包级别的变量、函数或类，或者控制哪些模块可以被外部导入。包可以嵌套，形成多层次的包结构
- `__init__.py` 文件的格式如下
```python
#相对导入，这样可以让包的用户直接from package import function，而无需知道它具体在哪个子模块中
from .module1 import function1
from .module2 import class2
#__all__是一个列表，包含包中希望公开的模块或属性的名称。当你使用 `from package import *` 时，`__all__` 列表中的内容会被导入。如果 `__all__` 未定义，则默认导入包中的所有模块和属性
__all__ = [
"function1",
"class2",
]
#包的元信息
__name__ = "mypackage"  # 包的名称
__doc__ = "This is my custom package."  # 包的文档字符串
__version__ = "1.0.0"  # 版本信息
__author__ = "Your Name"
#初始化
print(f"Initializing {__name__} version {__version__}...")
def init():
    print("Running package initialization code.")

```
### import
import用来导入模块，或从包中导入模块或特定属性
**import规则怪谈**：
- 相对导入只能在包内使用
- 主模块所在文件夹不会被视为包
- 使用相对导入的文件不能作为主模块运行
#### 绝对导入
- 从模块中导入
```python
#直接导入整个模块，可以通过module_name.访问模块中的所有属性
import module_name
#从模块中导入特定属性，可以直接使用这些属性，而不需要通过模块名前缀
from module_name import attribute1, attribute2
#从模块中导入所有属性，不推荐，这会污染命名空间
from module_name import *
#导入模块并重命名
import module_name as alias
```
- 从包中导入
```python
#从包中导入模块
from my_package import module1
#从包中导入特定属性
from my_package import function1, function2
```

##### 绝对导入的路径设置
绝对导入从sys.path搜索模块，一般在入口程序使用绝对导入
配置import搜索路径：
- 获取当前脚本文件的绝对路径：
	```python
	base_dir = os.path.dirname(os.path.abspath(__file__))
	```
- 将一个路径加入到python的import搜索路径：
```python
import sys
if new_path not in sys.path:
	sys.path.append(new_path)
```
- 打印import的搜索路径：
	```python
	import sys
	# 打印 import 的搜索路径
	print("Import search paths:")
	for path in sys.path:
		print(path)
	```
#### 相对导入
相对导入从module的`__package__`计算绝对路径，而当一个模块作为main被运行时，他的`__package__`为None,因此使用相对导入的模块不能被直接执行,会报错`ImportError: attempted relative import with no known parent package`
- 相对导入必须以from开头
```python
#从当前目录导入
from . import ModuleB
#从上级导入
from .. import ModuleB
```
## 一些常见的库
- Gradio 是一个用于快速创建和分享机器学习模型交互界面的 Python 库。它允许开发者通过简单的代码构建用户友好的 Web 界面，方便用户与模型进行交互，而无需编写复杂的前端代码。
- plotly
- pandas
- matplotlab
### os
```python
weights_path = './weights/autoencoder_weights.pth'
if os.path.exists(weights_path):
```
### 枚举类
```python
class Status(Enum):
        """状态枚举及对应emoji"""
        DONE = ('done', '✅')
        WAIT = ('wait', '⏳')
        FAILURE = ('failure', '❌')
        PROCESSING = ('processing', '🔄')
        
        def __init__(self, value: str, emoji: str):
            self._value_ = value
            self.emoji = emoji
            
        @classmethod
        def from_str(cls, value: str) -> TaskNode.Status:
            """从字符串创建Status"""
            for member in cls:
                if member.value == value:
                    return member
            raise ValueError(f"Invalid Status value: {value}")
```
- 每个枚举成员（如 `Status.DONE`）本质上是枚举类的实例对象
- 当 Python 解释器执行枚举类的定义代码时，会为每个枚举成员自动调用 __init__ 方法
- 枚举中cls可迭代
### 抽象基类
不能直接实例化，必须由子类实现其抽象方法，使用 `abc` 模块定义抽象方法
```python
from abc import ABC, abstractmethod
class Animal(ABC):  # 抽象类
    @abstractmethod #抽象方法装饰器，强制子类必须实现该方法，否则无法实例化子类
    def make_sound(self):
        pass

class Dog(Animal):
    def make_sound(self):
        return "Woof!"

d = Dog()
print(d.make_sound())  # 输出: Woof!

```
### threading
```python
import threading
def worker():
    while True:
        print("Worker thread is running...")
        time.sleep(1)
# 创建一个线程
thread = threading.Thread(target=worker)
#设置线程为守护线程,守护线程通常用于后台任务，当主线程结束时，守护线程会立即被终止
thread.daemon = True
# 启动线程
thread.start()
```

### numpy
参考自[Python Numpy入门精华_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1xK411X7ZQ/?spm_id_from=333.337.search-card.all.click)。
`<class 'numpy.ndarray'>` 是 NumPy 库中表示多维数组的类，数组中的数字必须是统一的数据类型
- 创建：
```python
#从列表创建
array_1D=np.array([1,2,3,4,5,6])
array_2D=np.array([[1,2,3],[4,5,6]])
array_3D=np.array([[[1,2,3],[4,5,6]],[[1,2,3],[4,5,6]]])
#类似range，但支持float
arr=np.arange(0,100,2.5)
#创建全0数组
arr_zeros=np.zeros((2,3,4))
#创建全1数组
arr_ones=np.ones((2,3,4))
#full填充
arr_full=np.full((2,3,4),fill_value)
#创建nxm主对角线为1的矩阵
arr_eye=np.eye(n,m)
#创建等间隔的数值序列
arr_line=np.linspace(start, stop, num)
#返回数组的视图(连续)或副本(不连续),m*n需要等于size,可使用-1自动计算某个维度大小
array_new=array_3D.reshape(m,n)
#创建0-1均匀分布数组
array_random=np.random.random((2,3,4))
#标准正太分布
array_random=np.random.randn(2,3,4)
#创建随机整数数组，范围(0~99)
array_random=np.random.randint(0,100,size=(2,3,4))
#创建数组时可以使用dtype指定数据类型
arr_ones=np.ones((2,3,4),dtype=np.int16)
#创建数组的视图，修改视图会影响到原数组
arr_view = array_3D.view()
#创建数组的拷贝，修改视图不影响原数组
arr_copy = array_3D.copy()
```
- 查：切片（与内置数据类型的切片操作不同，list、str、tuple的切片返回copy）、index索引、reshape、转置等操作返回的是原数组的视图，修改视图会影响原数组，列表索引和布尔索引返回的是新数组（副本），修改副本不会影响原数组
```python
#index索引
array_3D[0,1,2]
#布尔索引,返回满足条件的数值组成的一维数组
array_1D[array_1D > 3]
#使用整数列表索引
array_1D[[1, 3, 4]]
#切片,类似一维的切片,不同维的索引用逗号间隔
array_3D[:,:,::2]
```
- 属性和方法：
```python
#维度，输出:3
array_3D.ndim
#返回reshape的视图
array_3D.shape
#查看数据类型
array_3D.dtype
#查看数组的数字数量
array_3D.size
#获取数组所有元素之和
array_3D.sum()
#计算数组的逆
B_inv = np.linalg.inv(B)
#获取数组某一维度最大值，如0表示数组的第一个维度(最外层),同样的有min
array_3D.max(axis=0)
#condition 为 True，则选择 x 中的值；否则选择 y 中的值。
np.where(condition, x, y)
#将大于3的元素替换为原值，其余替换为0
arr = np.where(array_1D > 3, array_1D, 0)
#返回复合条件的索引列表
arr = np.argwhere(array_1D > 3)
#返回数组中的唯一值，并按升序排序
arr=np.unique(arr)
#将数组元素限制在 [2, 4] 范围内
clipped_arr = np.clip(arr, 2, 4)
#求模长
np.linalg.norm(axis)
#保存为txt
np.savetxt("verts.txt", texcoords, delimiter=" ", fmt="%f")
```

#### array的运算
- 逻辑运算：`==`、`>`、`>=`等，返回一个dtype为bool类型的数组
	```python
	a = np.array([1, 2, 3])
	b = np.array([2, 2, 2])
	# 比较运算
	print(a == b)  # 输出：[False  True False]
	print(a >= 2)  # 输出：[False  True  True]
	```

#### 数据类型
NumPy 提供了多种内置的数据类型，包括整型，浮点型，复数，bool类型，字符串和任意python对象、时间和日期的类型，甚至可以自定义dtype类型

|     数据类型      |         描述          |
| :-----------: | :-----------------: |
|     `nan`     |  特殊的浮点类型，表示不是一个数字   |
|     `inf`     |     特殊的浮点类型，无穷大     |
|    `int8`     |       8位有符号整数       |
|    `int16`    |      16位有符号整数       |
|    `int32`    |      32位有符号整数       |
|    `int64`    |      64位有符号整数       |
|    `uint8`    |       8位无符号整数       |
|   `uint16`    |      16位无符号整数       |
|   `uint32`    |      32位无符号整数       |
|   `uint64`    |      64位无符号整数       |
|   `float16`   |       16位浮点数        |
|   `float32`   |       32位浮点数        |
|   `float64`   |       64位浮点数        |
|  `complex64`  | 64位复数（32位实部+32位虚部）  |
| `complex128`  | 128位复数（64位实部+64位虚部） |
|    `bool_`    |  布尔类型（True/False）   |
|   `object_`   |     Python对象类型      |
|   `string_`   |     固定长度的字符串类型      |
|  `unicode_`   |  固定长度的Unicode字符串类型  |
| `datetime64`  |       日期时间类型        |
| `timedelta64` |        时间差类型        |

- 自定义dtype数据类型：
	```python
	dtype = np.dtype([("name", np.str_, 10), ("age", np.int32)])
	arr = np.array([("Alice", 25), ("Bob", 30)], dtype=dtype)
	print(arr.dtype)  # 输出: [('name', '<U10'), ('age', '<i4')]
	```
#### 存储
- `.npy`：NumPy 数组通过`data = np.load('your_file.npy')`加载
- `.npz`：NumPy 的压缩存档格式，可以存储字典、列表、数组、数组、字符等等，类似于一个字典，其中键是数据的名称，值是对应的数据：
```python
import numpy as np
data = np.load('your_file.npz')
# 查看所有数组的名称（键）
print(data.files)  # 输出：['arr_0', 'arr_1', ...] 或其他自定义键名
# 查看单个数组的结构（形状和数据类型）
for key in data.files:
    print(f"数组名称: {key}")
    print(f"形状: {data[key].shape}")
    print(f"数据类型: {data[key].dtype}")
    print("------")
```
### matplotlib
- 画矩阵：
```python
import matplotlib.pyplot as plt
plt.matshow(matrix)
plt.show()
```
### tqdm
用来显示进度条的库,[tqdm：程序显示进度条_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV186421Z7fH/?spm_id_from=333.337.search-card.all.click&vd_source=ca17e8e44a1bddd49f39d4bc7c4ad336)
- 使用
```python
import time
from tqdm import tqdm
for _ in tqdm(range(10))
	time.sleep(0.001)
```

### pytransform3d
PyTransform3D 是一个专门用于处理 3D 变换（旋转、平移、坐标系） 的 Python 库，它支持多种姿态表示形式（如四元数、轴角、欧拉角、旋转矩阵、变换矩阵等），并提供 可视化工具，功能比`scipy.spatial.transform.Rotation` 更全面，PyTransform3D 专注于可读性和调试，而不是计算效率
[Examples — pytransform3d 3.14.0 documentation](https://dfki-ric.github.io/pytransform3d/_auto_examples/index.html)
### pdb
[来学学debugger吧，不能只会用print调试呀](https://www.bilibili.com/video/BV1La4y1T7Y5/?spm_id_from=333.337.search-card.all.click&vd_source=ca17e8e44a1bddd49f39d4bc7c4ad336)
- 调试xx文件：
```python
python3 -m pdb xx.py
```
- 打断点：
```python
breakpoint()# 方式1
import pdb; pdb.set_trace()# 方式2
```
`n`：next，执行当前行，如果当前行包含函数调用，不会进入函数内部
`s`：step，执行当前行，如果当前行包含函数调用，会进入函数内部
`c`：continue，到下个断点
`until`：跳出循环
`r`：return，执行到当前函数返回
`l`：list，列出附近行，`ll`forlonglist，查看当前函数全部代码
`w`：where，查看调用栈
`u`：向上移动调用栈，也就是从当前正在执行的函数，切换到调用它的上一层函数
`d`：向下移动调用栈
`retval`：查看最近一次函数返回的结果
	-  当函数执行到 `return` 语句时，预先查看返回值
	- 函数返回后，查看最后一次返回的结果

`b`：添加断点
	- `<line number>`：在指定行添加断点
	- `<function_A>`：在函数A中添加断点
`break`：打印断点
`q`：退出debugger
输入变量名，打印变量
### hydra
[github官方库](https://github.com/facebookresearch/hydra?tab=readme-ov-file)
[doc](https://hydra.cc/docs/1.3/intro/)
```python
import hydra
from omegaconf import DictConfig
@hydra.main(config_path="../conf", config_name="config_autoencoder", version_base=None)
def main(cfg: DictConfig):
	pass
if __name__ == "__main__":
    main()
```


## else
[vscode无法识别python代码中的相对路径](https://blog.csdn.net/weixin_47139649/article/details/126870299)
[python 使用代理的几种方式](https://blog.csdn.net/whatday/article/details/112169945)

- 查看python版本：python3 --version

### python项目
#### 目录结构
[Python 项目布局大揭秘：src 布局与扁平布局深度对比 - 知乎](https://zhuanlan.zhihu.com/p/24184783363)
- 扁平布局：
```python
py_project/               #项目根目录
├── py_project/           # 包目录（同名于项目）  
│   ├── __init__.py       # 标识包的文件（Python 3.3+可省略）  
│   ├── module1.py        # 模块1  
│   ├── sub_pkg1/         # 子包1  
│   │   ├── __init__.py
│   │   ├── submod_a.py   #from .submod_b import *
│   │   └── submod_b.py   #from ..sub_pkg2.submod_c import *
│   ├── sub_pkg2/         # 子包2
│   │   ├── __init__.py
│   │   ├── submod_c.py
│   └── utils/            # 工具模块  
│       ├── __init__.py  
│       └── helper.py  
├── tests/                # 测试目录  
│   ├── test_module1.py  
│   └── test_module2.py  
├── data/                 # 数据
│   ├── inputs/          
│   └── outputs/
├── third_part/           # 依赖的第三方包
│   ├── pkg1/          
│   └── pkg2/
├── requirements.txt      # 依赖列表  
├── setup.py              # 打包脚本（用于pip安装）  
└── README.md             # 项目说明  
```
#### requirements.txt：
```bash
pip install pipreqs
pipreqs . --encoding=utf8
```
#### setup.py
- `setup.py` 是 Python 包的安装脚本，基于 `setuptools` 或 `distutils` 进行构建和安装
- `pyproject.toml` 是 Python **现代化打包标准**，它用来取代 `setup.py`
- 两种安装方式：
	1. `pip install .`：直接安装，该命令在当前目录下查找 `setup.py` 或`pyproject.toml`文件，将当前目录下的项目安装到 Python 环境中。
	2. `pip install -e .`：以可编辑模式安装，此方式安装后，Python 会直接引用项目目录中的源代码，而不是将代码复制到 `site-packages`。修改代码后无需重新安装，立即生效，存在一个问题，通过这种方式安装的包无法通过import导入，但代码可以正常执行，参考[为什么PyCharm和Pylance不能检测到以可编辑模式安装的软件包](https://www.saoniuhuo.com/question/detail-2675895.html)解决，通过`pip install -e . --config-settings editable_mode=compat`安装包，并在设置中重启语言服务器`Restart Language Server`
- 一个模板：
```python
from setuptools import setup, find_namespace_packages

# 读取requirements.txt中的依赖
with open("requirements.txt", "r", encoding="utf-8") as f:
    requirements = f.read().splitlines()

# 查找并打印包
packages=find_namespace_packages()
print("找到的包:")
for package in packages:
    print(f"- {package}")

setup(
    name="learn_pytorch",
    version="0.1.0",
    packages=packages, 
    install_requires=requirements,
)
```
# 设计模式
- [设计模式](https://www.bilibili.com/video/BV19541167cn/?spm_id_from=333.1007.top_right_bar_window_history.content.click)
## 工厂模式
工厂模式的核心是将创造对象与使用对象的过程分开