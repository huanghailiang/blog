---
title: printf的实现
date: 2017-02-07 15:07:24
tags: 操作系统
---
printf函数首先调用write函数，write函数再调用write系统调用
```
/*
 *  linux/lib/write.c
 *
 *  (C) 1991  Linus Torvalds
 */

#define __LIBRARY__
#include <unistd.h>

_syscall3(int,write,int,fd,const char *,buf,off_t,count)
```
在`linux/include/unistd.h`中有
```
#define __NR_write 4
#define _syscall3(type,name,atype,a,btype,b,ctype,c) \
type name(atype a,btype b,ctype c) \
{ \
long __res; \
__asm__ volatile ("int $0x80" \
	: "=a" (__res) \
	: "0" (__NR_##name),"b" ((long)(a)),"c" ((long)(b)),"d" ((long)(c))); \
if (__res>=0) \
	return (type) __res; \
errno=-__res; \
return -1; \
}
```
即接受三个参数的系统调用，这个宏被展开为
```
int write(int fd,const char * buf,off_t count){
long __res;
__asm__ volatile ("int $0x80" 
	: "=a" (__res) //系统调用返回后eax中存放有返回值，把eax中的值赋值给__res
	: "0" (__NR_write),"b" ((long)(a)),"c" ((long)(b)),"d" ((long)(c))); //这里把__NR_write赋值给eax，fd赋值给ebx，buf赋值给ecx，count赋值给edx
if (__res>=0) 
	return (type) __res; //返回eax
errno=-__res; 
return -1; 
}
```
因为都是通过int 0x80中断调用系统调用，所以区分不同的系统调用需要通过eax。
P.S. 系统调用是80号中断在`/kernel/sched.c`中定义
```
// 内核调度程序的初始化子程序
void sched_init(void)
{
	.
	.
	.
	set_system_gate(0x80,&system_call);
}
```
系统在`/include/asm/system.h`中初始化idt表
```
#define _set_gate(gate_addr,type,dpl,addr) \
__asm__ ("movw %%dx,%%ax\n\t" \
	"movw %0,%%dx\n\t" \
	"movl %%eax,%1\n\t" \
	"movl %%edx,%2" \
	: \
	: "i" ((short) (0x8000+(dpl<<13)+(type<<8))), \
	"o" (*((char *) (gate_addr))), \
	"o" (*(4+(char *) (gate_addr))), \
	"d" ((char *) (addr)),"a" (0x00080000))

#define set_system_gate(n,addr) \
	_set_gate(&idt[n],15,3,addr)
```
