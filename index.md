# 深度剖析 GO 为何火热
## 为什么要学习 GO 语言
```
GO 是互联网时代的 C 语言  - 许式伟 
```
1. 天生支持高并发，goroutine, channel

2. 工程哲学，简约，非学术语言。关键字 25 个，想比 c 语言 32, java 53, c++ 62 ...
```
package import go goto defer return var const type  func
map chan interface struct range select for continue break
switch case default fallthrough if  else
```

3. 语法简单，[少即是多](https://commandcenter.blogspot.com/2012/06/less-is-exponentially-more.html)，学习曲线平坦
4. 作者 Robert Griesemer、Rob Pike、Ken Thompson， 
5. 静态，强一致类型语言，线上运行稳定安全

## 语言方面的对比
开宗明义，莫要唯语言论，没有最好，只有适合
### 从小处看工程哲学
```
# cat test.c
#include <stdio.h>

int main(){
	int a;
	printf("value: %d, sizeof: %ld\n", a, sizeof(a));
	return 0;
}
# gcc test.c && ./a.out
value: 319529014, sizeof: 4
```
```
# cat test.go
package main

import "fmt"
import "unsafe"

func main(){
	var a int
	fmt.Printf("value: %d, sizeof: %d\n", a, unsafe.Sizeof(a))
}
# go run test.go
value: 0, sizeof: 8
```
点评：大学时代，老师教我们 int 是四字节，有符号最大 21 亿，无符号 42 亿。如果 c 语言中一个变量未初始化，那么值可能是随机的， 因为创建局部变量只是 bp 指针向下移动而己。但，现在都 9012 年了，64 位系统己成主流，不必在按位去使用内存，所以 go 语言默认 64 位的 int 就是 8 字节大小。创建变量时，go 也会直接初始化成零值，防止不必要的问题，从实践中来到实践中去。

```
int binary_search(int * list,int len,int target){
    int low = 0;
    int hight = len-1;
    int middle;
    while(low <= hight){
        middle = (low + hight)/2; // mid = low - (low-hight)/2
        if(list[middle] = target)
        {
            return list[middle];
        }
        else if(list[middle] > target)
        {
            hight = middle -1;
        }
        else if(list[middle] < target)
        {
            low = middle + 1;
        }
    }
    return -1;
}
```
点评：这是 c 语言写的二分查找算法，在很长一段时间里都是不能正确工作，原因就在于 mid 求值可能会溢出

### 垃圾回收，不用关心内存分配与回收
```
```
点评：

### defer 与 RAII
```
func (o *Once) Do(f func()) {
	if atomic.LoadUint32(&o.done) == 1 {
		return
	}

	o.m.Lock()
	defer o.m.Unlock()
	if o.done == 0 {
		defer atomic.StoreUint32(&o.done, 1)
		f()
	}
}
```
```
class DBCon{
public:
  DBCon() { 
    handle = fooOpenDBConnection();
  }
  ~DBCon() {
    int err = fooCloseDBConnection();
    if(err){
      if(err == E_fooConnectionDropped){
        // do nothing.  must have timed out
      } else if(fooIsCriticalError(err)){
        // critical errors aren't recoverable.  log, save 
        //  restart information, and die
        std::clog << "critical DB error: " << err << "\n";
        save_recovery_information();
        std::terminate();
      } else {
        // log, in case we need to gather this info in the future,
        //  but continue normally.
        std::clog << "non-critical DB error: " << err << "\n";
      }
    }
    // done!
  }
};
```
点评：RAII 在 c++ 项目中应用广泛，一般用于获取锁或是竞争资源，退出作用域时自动释放。GO 常用 defer 来实现此功能，在函数返回前，按 FILO 的顺序执行注册的 defer 函数。
## GO 语言生态圈
## 小白如何入门
## 老鸟如何进阶

![](images/only_money_can_persuade_me.jpg)