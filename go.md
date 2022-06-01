# 1.Go语言基础入门与编程思想

## 1.1 基本语法

### 1.1.1 go变量定义

```go
package main

import "fmt"

//包内变量,无全局变量概念
//可以使用var集中定义变量
var (
   aa = 3
   bb = 4
)

//不赋值
func variableZeroValue() {
   //go语言定义变量,名字在前,类型在后
   //a的初值为0,s空
   var a int
   var s string
   fmt.Printf("variableZeroValue %d %q\n", a, s)
}

//对变量赋初值
func variableInitialValue() {
   var a, b int = 3, 4
   var s string = "abc"
   fmt.Println("variableInitialValue", a, b, s)
}

//不声明变量类型,go语言可以自行判断类型
func variableTypeDeduction() {
   var a, b, c, d = 3, 4, true, "abc"
   fmt.Println("variableTypeDeduction", a, b, c, d)
}

//第一次使用可以用冒号形式定义变量
func variableShorter() {
   a, b, c, d := 3, 4, true, "def"
   b = 5
   fmt.Println("variableShorter", a, b, c, d)
}

func main() {
   fmt.Println("Hello world!")
   variableZeroValue()
   variableInitialValue()
   variableTypeDeduction()
   variableShorter()
   fmt.Println(aa, bb)
}
```





### 1.1.2 内建变量类型

+ Go语言内建变量：

![image-20220530134640093](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220530134640093.png)

+ **强制类型转换**

  ```go
  //强制类型转换
  func sqrt() {
     var a, b int = 3, 4
     var c int
     //sqrt方法内部强制转换成float64类型,计算结果强转为int
     c = int(math.Sqrt(float64(a*a + b*b)))
     fmt.Println(c)
  }
  ```





### 1.1.3 常量与枚举

![image-20220530141458455](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220530141458455.png)

+ 枚举

  ```go
  //枚举
  func enums() {
     const (
        cpp    = 1
        java   = 2
        python = 3
        golang = 4
     )
     fmt.Println(cpp, java, python, golang)
  }
  
  //枚举
  func enums() {
  	//自增值
  	const (
  		cpp = iota
  		java
  		python
  		golang
  	)
  	fmt.Println(cpp, java, python, golang)
  }
  ```

+ 变量定义要点

  ![image-20220530143449558](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220530143449558.png)







### 1.1.4 条件、循环、函数

go语言的if语句条件不需要括号

+ if

  ```go
  func main() {
     const filename = "abc.txt"
     //go语言函数可以返回2个值
     contents, err := ioutil.ReadFile(filename)
     if err != nil {
        fmt.Println(err)
     } else {
        fmt.Printf("%s\n", contents)
     }
  
     //if的另一种形式
     if contents, err := ioutil.ReadFile(filename); err != nil {
        fmt.Println(err)
     } else {
        fmt.Printf("%s\n", contents)
     }
  }
  ```

+ switch

  ```go
  func grade(score int) string {
     g := ""
     switch {
     case score < 60:
        g = "F"
     case score < 80:
        g = "C"
     case score < 90:
        g = "C"
     case score < 0 || score > 100:
        panic(fmt.Sprintf("Wrong score: %d", score))
     default:
        g = "A"
     }
     return g
  }
  
  func main() {
     fmt.Println(grade(101))
  
     const filename = "abc.txt"
     //go语言函数可以返回2个值
     contents, err := ioutil.ReadFile(filename)
     if err != nil {
        fmt.Println(err)
     } else {
        fmt.Printf("%s\n", contents)
     }
  
     //if的另一种形式
     if contents, err := ioutil.ReadFile(filename); err != nil {
        fmt.Println(err)
     } else {
        fmt.Printf("%s\n", contents)
     }
  }
  ```

+ **循环**

  ![image-20220530154241948](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220530154241948.png)

+ **函数**

  ![image-20220531112212924](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220531112212924.png)