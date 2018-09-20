## C++ string类的实现 ##
### 1. 常见的三种实现方式 ###
#### 1.1 eager copy （直接拷贝）####
1. 代码主体

```
class string {
public:

	
private:
	char* start;
	char* finish;
    char* end_of_storage;
}
```


### 参考资料 ###

1. [C++的std:string的“读时也拷贝”技术！](https://coolshell.cn/articles/1443.html "C++的STD::STRING的“读时也拷贝”技术！") 
2. [C++ STL string的copy_on_write技术](https://coolshell.cn/articles/12199.html)
https://zhuanlan.zhihu.com/p/23016264
string 的三种常见实现方案:
各个方案的优势劣势
copy_on_write 详解，实验 https://coolshell.cn/articles/12199.html
为何 C++11取消了copy_on_write技术