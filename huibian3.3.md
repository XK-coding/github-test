# 汇编语言

## 1. 栈
- 栈是一种具有特殊的访问方式的存储空间
- 它的特殊性就在于，最后进入这个空间的数据，最先出去。即先进后出

### 1.1 栈的基本操作
- 入栈：入栈就是将一个新的元素放到栈顶
- 出栈：出栈就是从栈顶取出一个元素
- 栈顶的元素总是最后入栈，需要出栈时又最先被从栈中取出
- LIFO（Last In First Out），后进先出

## 2. CPU提供的栈机制
- push（入栈）和pop（出栈）

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/b4356a0f663c4a88bf0fb24e434d2a63.png)
- 大数据即高位在栈下面，小数据即地位在栈上面
- 任意时刻，ss:sp指向栈顶元素

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/1e544ba3ee1e4c188ddcbb0ce178ac68.png)

```
//使ss:sp初始指向1000:0010即10000h
mount c d:masm
c:
debug
r
mov ax,0123		//ax = 0123h
push ax		//把ax的数据压入栈中，大数据01在下面，小数据23在上面，此时ss:sp 指向1000EH
mov bx,2266		//bx = 2266
push bx		//把bx的数据压入栈中，大数据22在下面，小数据66在上面，此时ss:sp 指向1000CH
mov cx,1122		//cx = 1122
push cx		//同上面两步，此时ss:sp指向1000AH，即push指令时，sp = sp - 2
pop ax		//把栈顶的元素弹出并且赋值给ax，此时ax = 1122h，ss:sp指向1000CH
pop bx		//同上，此时bx = 2266h，ss:sp指向1000EH
pop cx		//此时cx = 0123h，ss:sp指向10010h。即pop指令时，sp = sp + 2

r ss		//让ss:sp指向栈顶元素
1000
r sp
0010
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7aecce24c52b4d69a6fa41d5b5ed51c0.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/f2b22afc34a5440c8e245d0c9c645ad0.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/4b8a9e33953840a48e48e97b85669b75.png)


## 3. 栈顶超界问题
- 当栈满的时候再使用push指令入栈，或栈空的时候再使用pop指令出栈，都将发送栈顶超界问题
- 栈顶超界是危险的
- 我们自己要操心栈顶超界的问题，要根据可能用到的最大栈空间，来安排栈的大小，防止入栈的数据太多而导致的超界
- 执行出栈操作的时候也要注意，以防栈空的时候继续出栈而导致的超界

## 4. push和pop也可以对内存进行操作
- push和pop指令是可以在寄存器和内存之间传送数据的
```
mov ax,1000
mov ds,ax		//内存单元的段地址要放在ds中
push [0]		//将1000:0处的字压入栈中
pop [2]			//出栈，出栈的数据送入1000:2处
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/182da767156147509b2bc548553ded0e.png)![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/087017db36c44190a6b55bec4e72ce37.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/3182e66794e3442083a32b0a66962178.png)



指令执行时，CPU要知道内存单元的地址，可以在push、pop指令中只给出内存单元的偏移地址，段地址在指令执行时，CPU从ds中取得