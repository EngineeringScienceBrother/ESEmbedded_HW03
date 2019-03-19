HW03
===
This is the hw03 sample. Please follow the steps below.

# Build the Sample Program

1. Fork this repo to your own github account.

2. Clone the repo that you just forked.

3. Under the hw03 dir, use:

	* `make` to build.

	* `make clean` to clean the ouput files.

4. Extract `gnu-mcu-eclipse-qemu.zip` into hw03 dir. Under the path of hw03, start emulation with `make qemu`.

	See [Lecture 02 ─ Emulation with QEMU] for more details.

5. The sample is a minimal program for ARM Cortex-M4 devices, which enters `while(1);` after reset. Use gdb to get more details.

	See [ESEmbedded_HW02_Example] for knowing how to do the observation and how to use markdown for taking notes.

# Build Your Own Program

1. Edit main.c.

2. Make and run like the steps above.

3. Please avoid using hardware dependent C Standard library functions like `printf`, `malloc`, etc.

# HW03 Requirements

1. How do C functions pass and return parameters? Please describe the related standard used by the Application Binary Interface (ABI) for the ARM architecture.

2. Modify main.c to observe what you found.

3. You have to state how you designed the observation (code), and how you performed it.

	Just like how you did in HW02.

3. If there are any official data that define the rules, you can also use them as references.

4. Push your repo to your github. (Use .gitignore to exclude the output files like object files or executable files and the qemu bin folder)

[Lecture 02 ─ Emulation with QEMU]: http://www.nc.es.ncku.edu.tw/course/embedded/02/#Emulation-with-QEMU
[ESEmbedded_HW02_Example]: https://github.com/vwxyzjimmy/ESEmbedded_HW02_Example

--------------------

- [] **If you volunteer to give the presentation next week, check this.**

--------------------

**★★★ Please take your note here ★★★**
在我們做實驗之前有些基本觀念要先知道一下：
1. 子程式間通過暫存器R0～R3來傳遞引數。這時，暫存器R0～R3可記作a0～a3。被呼叫的子程式在返回前無需恢復暫存器R0～R3的內容。
2. 在子程式中，使用暫存器R4～R11來儲存區域性變數。這時，暫存器R4～R11可以記作v1～v8。如果在子程式中使用了暫存器v1～v8中的某些暫存器，則子程式進入時必須儲存這些暫存器的值，在返回前必須恢復這些暫存器的值。在Thumb程式中，通常只能使用暫存器R4～R7來儲存區域性變數。
3. 暫存器R12用作過程呼叫中間臨時暫存器，記作IP。在子程式之間的連線程式碼段中常常有這種使用規則。
4. 暫存器R13用作堆疊指標，記作SP。在子程式中暫存器R13不能用作其他用途。暫存器SP在進入子程式時的值和退出子程式時的值必須相等
5. 暫存器R14稱為連線暫存器，記作LR。它用於儲存子程式的返回地址。如果在子程式中儲存了返回地址，暫存器R14則可以用作其他用途。
6. 暫存器R15是程式計數器，記作PC。它不能用作其它用途。
資料參考來源: https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/423687/

首先從以下code可以看出，宣告"a=1"時的流程：
   8:	b580      	push    {r7, lr} // r7幫忙儲存在stack pointer中裡面，而link regitser則是會先儲存原本指令的位置
   a:	b084      	sub	sp, #16  // stack pointer 要用到memory中4個位置(16/4=4)
   c:	af00      	add	r7, sp, #0 // 歸回stack memory的空間
   e:	2301      	movs	r3, #1

其他宣告也似同樣概念

接者從以下code可以看出，執行"加法"時的流程：
00000038 <plus>:
  38:	b480      	push	{r7}
  3a:	b083      	sub	sp, #12
  3c:	af00      	add	r7, sp, #0
  3e:	6078      	str	r0, [r7, #4] //重點在於這邊
  40:	6039      	str	r1, [r7, #0]
  42:	687a      	ldr	r2, [r7, #4]
  44:	683b      	ldr	r3, [r7, #0]
  46:	4413      	add	r3, r2       //若是減法則是subs
  48:	4618      	mov	r0, r3
  4a:	370c      	adds	r7, #12      
  4c:	46bd      	mov	sp, r7
  4e:	f85d 7b04 	ldr.w	r7, [sp], #4
  52:	4770      	bx	lr

最後從以下code可以看出"return"的流程:
  22:	f000 f809 	bl	38 <plus>
  26:	6878      	ldr	r0, [r7, #4] 
  28:	6839      	ldr	r1, [r7, #0]
  2a:	f000 f813 	bl	54 <minus>
  2e:	bf00      	nop
  30:	3710      	adds	r7, #16
  32:	46bd      	mov	sp, r7
  34:	bd80      	pop	{r7, pc}   //stack 回到最初的位置
  36:	bf00      	nop













