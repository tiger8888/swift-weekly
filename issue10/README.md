WORK IN PROGRESS
===
Swift Weekly - Issue 10 - The Swift Runtime (Part 8) - Switch Statement
===
	Vandad Nahavandipoor
	http://www.oreilly.com/pub/au/4596
	Email: vandad.np@gmail.com
	Blog: http://vandadnp.wordpress.com
	Skype: vandad.np

Introduction
===
I was thinking of writing an article this week about the [switch statement](http://goo.gl/hWuAna) but i almost fell asleep half way through the article. that's super boring. to write it even! so I thought I'll do something more exciting and that is looking at how the `switch` statement and its complementary `case` statement works.

just to give you a heads up, i'm on swift 1.2 so the output assembly on my version of llvm may be different from what you might get depending on the version of xcode and the sdk that is installed on your system:

```bash
xcrun xcodebuild -version
Xcode 6.3
Build version 6D520o
```

Constructing Tuples Inside a `switch` Statement
===
let's compile this swift code:

```swift
func example1(){
   
   let p = CGPoint(x: 0xaaaaaaaa, y: 0xbbbbbbbb)
   let f = view.frame
   let fs = f.size
   
   switch (p.x, p.y){
   case (0xabcdefc, 0):
       println(0xabcdefc)
   case (fs.width, 0xabcdefe):
       println(0xabcdefe)
   case (0xabcdeff, fs.height):
       println(0xabcdeff)
   default:
       println(0xffffffff)
   }
   
}
```

and get the output assembly:

```asm
0000000100001b50   push       rbp                                         ; Objective C Implementation defined at 0x100005260 (instance)
0000000100001b51   mov        rbp, rsp
0000000100001b54   push       r15
0000000100001b56   push       r14
0000000100001b58   push       r12
0000000100001b5a   push       rbx
0000000100001b5b   sub        rsp, 0xc0
0000000100001b62   mov        r14, rdi
0000000100001b65   mov        r15d, 0xaaaaaaaa
0000000100001b70   lea        rsi, qword [ds:r15+0x11111111]
0000000100001b77   mov        edi, 0xaaaaaaaa
0000000100001b7c   call       imp___stubs___TFE12CoreGraphicsVSC7CGPointCfMS0_FT1xSi1ySi_S0_
0000000100001b81   movsd      xmmword [ss:rbp+var_C8], xmm0
0000000100001b89   movsd      xmmword [ss:rbp+var_D0], xmm1
0000000100001b91   mov        rbx, qword [ds:0x100005e40]                 ; @selector(view)
0000000100001ba0   mov        rdi, r14                                    ; argument "instance" for method imp___stubs__objc_msgSend
0000000100001ba3   mov        rsi, rbx                                    ; argument "selector" for method imp___stubs__objc_msgSend
0000000100001ba6   call       imp___stubs__objc_msgSend
0000000100001bb3   mov        rbx, rax
0000000100001bb6   test       rbx, rbx
0000000100001bb9   je         0x100001de4

0000000100001bbf   mov        rdx, qword [ds:0x100005e48]                 ; @selector(frame), argument "selector" for method imp___stubs__objc_msgSend_stret
0000000100001bc6   lea        rdi, qword [ss:rbp+var_40]                  ; argument "stret" for method imp___stubs__objc_msgSend_stret
0000000100001bca   mov        rsi, rbx                                    ; argument "instance" for method imp___stubs__objc_msgSend_stret
0000000100001bcd   call       imp___stubs__objc_msgSend_stret
0000000100001bd2   movsd      xmm0, qword [ss:rbp+var_30]
0000000100001bd7   movsd      xmmword [ss:rbp+var_D8], xmm0
0000000100001bdf   movsd      xmm0, qword [ss:rbp+var_28]
0000000100001be4   movsd      xmmword [ss:rbp+var_E0], xmm0
0000000100001bfc   mov        rax, 0x41a579bdf8000000
0000000100001c06   mov        qword [ss:rbp+var_48], rax
0000000100001c0a   movsd      xmm0, qword [ss:rbp+var_C8]
0000000100001c12   movsd      xmmword [ss:rbp+var_50], xmm0
0000000100001c17   mov        r12, qword [ds:imp___got___TWPV12CoreGraphics7CGFloatSs9EquatableS_] ; imp___got___TWPV12CoreGraphics7CGFloatSs9EquatableS_
0000000100001c1e   mov        rbx, qword [ds:imp___got___TMdV12CoreGraphics7CGFloat] ; imp___got___TMdV12CoreGraphics7CGFloat
0000000100001c25   add        rbx, 0x8
0000000100001c29   lea        rdi, qword [ss:rbp+var_48]
0000000100001c2d   lea        rsi, qword [ss:rbp+var_50]
0000000100001c31   mov        rdx, rbx
0000000100001c34   mov        rcx, rbx
0000000100001c37   call       qword [ds:r12]
0000000100001c3b   test       al, 0x1
0000000100001c3d   je         0x100001c98

0000000100001c3f   mov        qword [ss:rbp+var_B0], 0x0
0000000100001c4a   movsd      xmm0, qword [ss:rbp+var_D0]
0000000100001c52   movsd      xmmword [ss:rbp+var_B8], xmm0
0000000100001c5a   lea        rdi, qword [ss:rbp+var_B0]
0000000100001c61   lea        rsi, qword [ss:rbp+var_B8]
0000000100001c68   mov        rdx, rbx
0000000100001c6b   mov        rcx, rbx
0000000100001c6e   call       qword [ds:r12]
0000000100001c72   test       al, 0x1
0000000100001c74   je         0x100001c98

0000000100001c76   mov        qword [ss:rbp+var_C0], 0xabcdefc
0000000100001c81   mov        rsi, qword [ds:imp___got___TMdSi]           ; imp___got___TMdSi
0000000100001c88   add        rsi, 0x8
0000000100001c8c   lea        rdi, qword [ss:rbp+var_C0]
0000000100001c93   jmp        0x100001dc7

0000000100001c98   movsd      xmm0, qword [ss:rbp+var_D8]                 ; XREF=-[_TtC12swift_weekly14ViewController example1]+237, -[_TtC12swift_weekly14ViewController example1]+292
0000000100001ca0   movsd      xmmword [ss:rbp+var_58], xmm0
0000000100001ca5   movsd      xmm0, qword [ss:rbp+var_C8]
0000000100001cad   movsd      xmmword [ss:rbp+var_60], xmm0
0000000100001cb2   lea        rdi, qword [ss:rbp+var_58]
0000000100001cb6   lea        rsi, qword [ss:rbp+var_60]
0000000100001cba   mov        rdx, rbx
0000000100001cbd   mov        rcx, rbx
0000000100001cc0   call       qword [ds:r12]
0000000100001cc4   test       al, 0x1
0000000100001cc6   je         0x100001d27

0000000100001cc8   mov        rax, 0x41a579bdfc000000
0000000100001cd2   mov        qword [ss:rbp+var_98], rax
0000000100001cd9   movsd      xmm0, qword [ss:rbp+var_D0]
0000000100001ce1   movsd      xmmword [ss:rbp+var_A0], xmm0
0000000100001ce9   lea        rdi, qword [ss:rbp+var_98]
0000000100001cf0   lea        rsi, qword [ss:rbp+var_A0]
0000000100001cf7   mov        rdx, rbx
0000000100001cfa   mov        rcx, rbx
0000000100001cfd   call       qword [ds:r12]
0000000100001d01   test       al, 0x1
0000000100001d03   je         0x100001d27

0000000100001d05   mov        qword [ss:rbp+var_A8], 0xabcdefe
0000000100001d10   mov        rsi, qword [ds:imp___got___TMdSi]           ; imp___got___TMdSi
0000000100001d17   add        rsi, 0x8
0000000100001d1b   lea        rdi, qword [ss:rbp+var_A8]
0000000100001d22   jmp        0x100001dc7

0000000100001d27   mov        rax, 0x41a579bdfe000000                     ; XREF=-[_TtC12swift_weekly14ViewController example1]+374, -[_TtC12swift_weekly14ViewController example1]+435
0000000100001d31   mov        qword [ss:rbp+var_68], rax
0000000100001d35   movsd      xmm0, qword [ss:rbp+var_C8]
0000000100001d3d   movsd      xmmword [ss:rbp+var_70], xmm0
0000000100001d42   lea        rdi, qword [ss:rbp+var_68]
0000000100001d46   lea        rsi, qword [ss:rbp+var_70]
0000000100001d4a   mov        rdx, rbx
0000000100001d4d   mov        rcx, rbx
0000000100001d50   call       qword [ds:r12]
0000000100001d54   test       al, 0x1
0000000100001d56   je         0x100001dad

0000000100001d58   movsd      xmm0, qword [ss:rbp+var_E0]
0000000100001d60   movsd      xmmword [ss:rbp+var_80], xmm0
0000000100001d65   movsd      xmm0, qword [ss:rbp+var_D0]
0000000100001d6d   movsd      xmmword [ss:rbp+var_88], xmm0
0000000100001d75   lea        rdi, qword [ss:rbp+var_80]
0000000100001d79   lea        rsi, qword [ss:rbp+var_88]
0000000100001d80   mov        rdx, rbx
0000000100001d83   mov        rcx, rbx
0000000100001d86   call       qword [ds:r12]
0000000100001d8a   test       al, 0x1
0000000100001d8c   je         0x100001dad

0000000100001d8e   mov        qword [ss:rbp+var_90], 0xabcdeff
0000000100001d99   mov        rsi, qword [ds:imp___got___TMdSi]           ; imp___got___TMdSi
0000000100001da0   add        rsi, 0x8
0000000100001da4   lea        rdi, qword [ss:rbp+var_90]
0000000100001dab   jmp        0x100001dc7

0000000100001dad   add        r15, 0x55555555                             ; XREF=-[_TtC12swift_weekly14ViewController example1]+518, -[_TtC12swift_weekly14ViewController example1]+572
0000000100001db4   mov        qword [ss:rbp+var_78], r15
0000000100001db8   mov        rsi, qword [ds:imp___got___TMdSi]           ; imp___got___TMdSi
0000000100001dbf   add        rsi, 0x8
0000000100001dc3   lea        rdi, qword [ss:rbp+var_78]

0000000100001dc7   call       imp___stubs___TFSs7printlnU__FQ_T_          ; XREF=-[_TtC12swift_weekly14ViewController example1]+323, -[_TtC12swift_weekly14ViewController example1]+466, -[_TtC12swift_weekly14ViewController example1]+603
0000000100001dd4   add        rsp, 0xc0
0000000100001ddb   pop        rbx
0000000100001ddc   pop        r12
0000000100001dde   pop        r14
0000000100001de0   pop        r15
0000000100001de2   pop        rbp
0000000100001de3   ret
```

let's see what happened. i will explain the code thoroughly here, because, well, i have time and i think it's fun:

1. first we have `mov        r15d, 0xaaaaaaaa` that moves the value of the `x` position of our `CGPoint` into a GPR (General Purpose Register) named `r15d`. I have to admit, i personally had not seen this gpr before, so in search for that, i checked out some resources including [NASM pages](http://goo.gl/5wvq4R) and it turns out that in 32-bit mode, as we know, we have `eax`, `ebx` and other gprs. the equivalent of `eax` in 64-bit mode is `rax`. now, in 32-bit mode, there are 8 new 32/64-bit gprs that have been made available only on x86_64 cpus, and those 32-bit gprs are named `r8d` to `r15d` inclusive. now, these 32-bit gprs are really the lower 32-bits of their 64-bit parents who are named `r8` to `r15` so when we see `mov        r15d, 0xaaaaaaaa` in our code, it just means that the lower-32 bits of the `r15` 64-bit register is now set to `0xaaaaaaaa`. great. that's the x position of our point.

2. then we have `lea        rsi, qword [ds:r15+0x11111111]`. wait... what? where is the `0xbbbbbbbb` value that we designated as the y position of our `CGPoint`? if you look through the output assembly, our `0xbbbbbbbb` is nowhere to be found. where did it go? this is one hell of a complicated code that swift wrote instead of just `mov rsi, 0xbbbbbbbb`. This is really strange but i'll explain what is happening. 

	1. `ds` at this point is `0x00000000`, trust me, i attached a debugger to the output assembly and found this out. i don't exactly know why `ds` is zero at this point. i thought it could be specified in the brief specifications for [System V AMD64 ABI](http://goo.gl/mBdSoG) on wikipedia or on the official documentaton of [System V Application Binary Interface, AMD64 Architecture Processor Supplement, Draft Version 0.99.6](http://goo.gl/lCBYu) but none of these had specified (through my brief look) anything about a caller function changing `ds` upon calling another function so if you know why `ds` is zero in this case, send a pull request to inform others please.
	2. since `r15d`, the lower-32-bits of the `r15` register are already set to `0xaaaaaaaa`, this `ds:r15+0x11111111` will result into `0x00000000:0xaaaaaaaa+0x11111111` or `0x00000000:0xbbbbbbbb` and the `qword` at this address will be `0xbbbbbbbb`. __holy guacamole__. what the hell? WHY? why would Swift do all this calculation in order to reach to the value of `0xbbbbbbbb` which it could just put in `cs` and just use the `mov` instruction? my guess is that the pairing of the previous `mov` instruction and the `lea` instruction has some sort of optimization on x86_64. remember that this is __not__ ARM code yet. i am going to have to learn more about arm assembly before I can write an article about it. hopefully soon

3. at this point, `rdi` is `0xaaaaaaaa` and `rsi` is `0xbbbbbbbb`, the x and y positions of our point. how does swift then create the `CGPoint`? using the private function `imp___stubs___TFE12CoreGraphicsVSC7CGPointCfMS0_FT1xSi1ySi_S0_`. after this function, `xmm0` and `xmm1` will contain the x and y position of our point and they are placed in the stack like so:

	```asm
	0000000100001b81         movsd      xmmword [ss:rbp+var_C8], xmm0
	0000000100001b89         movsd      xmmword [ss:rbp+var_D0], xmm1
	```

4. then we have to get the frame of our view and place it in the `f` local constant:

	```asm
	0000000100001b91         mov        rbx, qword [ds:0x100005e40]                 ; @selector(view)
	0000000100001ba0         mov        rdi, r14                                    ; argument "instance" for method imp___stubs__objc_msgSend
	0000000100001ba3         mov        rsi, rbx                                    ; argument "selector" for method imp___stubs__objc_msgSend
	0000000100001ba6         call       imp___stubs__objc_msgSend
	0000000100001bb3         mov        rbx, rax
	0000000100001bb6         test       rbx, rbx
	0000000100001bb9         je         0x100001de4

	0000000100001bbf         mov        rdx, qword [ds:0x100005e48]                 ; @selector(frame), argument "selector" for method imp___stubs__objc_msgSend_stret
	0000000100001bc6         lea        rdi, qword [ss:rbp+var_40]                  ; argument "stret" for method imp___stubs__objc_msgSend_stret
	0000000100001bca         mov        rsi, rbx                                    ; argument "instance" for method imp___stubs__objc_msgSend_stret
	0000000100001bcd         call       imp___stubs__objc_msgSend_stret
	```

	we are retrieving the `frame` property of our view and the call to this method is done using the `imp___stubs__objc_msgSend_stret` function.

5. after we have the frame, we are retrieving the `size` property of the frame and that is done with these instructions:

	```asm
	0000000100001bd2         movsd      xmm0, qword [ss:rbp+var_30]
	0000000100001bd7         movsd      xmmword [ss:rbp+var_D8], xmm0
	0000000100001bdf         movsd      xmm0, qword [ss:rbp+var_28]
	0000000100001be4         movsd      xmmword [ss:rbp+var_E0], xmm0
	```

	it turns out that the location `[ss:rbp+var_30]` in the stack segment after the `imp___stubs__objc_msgSend_stret` call contains the `size.x` value and `[ss:rbp+var_28]` location in `ss` contains the value of `size.y`. now they are in the stack, representing our local `fs` constant: `let fs = f.size`.

6. then we get to a series of really strange calculations so let's break it down:

	```asm
	0000000100001bfc         mov        rax, 0x41a579bdf8000000
	0000000100001c06         mov        qword [ss:rbp+var_48], rax
	0000000100001c0a         movsd      xmm0, qword [ss:rbp+var_C8]
	0000000100001c12         movsd      xmmword [ss:rbp+var_50], xmm0
	0000000100001c17         mov        r12, qword [ds:imp___got___TWPV12CoreGraphics7CGFloatSs9EquatableS_] ; imp___got___TWPV12CoreGraphics7CGFloatSs9EquatableS_
	0000000100001c1e         mov        rbx, qword [ds:imp___got___TMdV12CoreGraphics7CGFloat] ; imp___got___TMdV12CoreGraphics7CGFloat
	0000000100001c25         add        rbx, 0x8
	0000000100001c29         lea        rdi, qword [ss:rbp+var_48]
	0000000100001c2d         lea        rsi, qword [ss:rbp+var_50]
	0000000100001c31         mov        rdx, rbx
	0000000100001c34         mov        rcx, rbx
	0000000100001c37         call       qword [ds:r12]
	0000000100001c3b         test       al, 0x1
	0000000100001c3d         je         0x100001c98
	```

	1. first is the mysterious `mov` instruction: `0000000100001bfc         mov        rax, 0x41a579bdf8000000`. what is going on here? if you know what this value is, send a PR please and complete this article.
	2. then we have this code `movsd      xmm0, qword [ss:rbp+var_C8]`. remember what we put in `[ss:rbp+var_C8]`? remember this code?

		```asm
		0000000100001b7c         call       imp___stubs___TFE12CoreGraphicsVSC7CGPointCfMS0_FT1xSi1ySi_S0_
		0000000100001b81         movsd      xmmword [ss:rbp+var_C8], xmm0
		```
	
		well, the x position of the point that we had created is now inside `[ss:rbp+var_C8]` and after `cs:0000000100001bfc`, `rax` will contain the x position. so Swift is just loading the value of our `p` constant back into general purpose registers, in this case, into the `xmm0` register.
	
	3. we then have the following lines of code:

		```asm
		0000000100001c17         mov        r12, qword [ds:imp___got___TWPV12CoreGraphics7CGFloatSs9EquatableS_] ; imp___got___TWPV12CoreGraphics7CGFloatSs9EquatableS_
		0000000100001c1e         mov        rbx, qword [ds:imp___got___TMdV12CoreGraphics7CGFloat] ; imp___got___TMdV12CoreGraphics7CGFloat
		```
	
		after this, `r12` will be set to `0x0000000100F76590` and `rbx` will be set to `0x0000000100F76910` (i have a debugger). still, this is quite vague. let's move on to find out more. maybe these are pieces of a bigger puzzle which we/i don't get yet.
	
	4. then we have more mysterious lines:

		```asm
		0000000100001c25         add        rbx, 0x8
		0000000100001c29         lea        rdi, qword [ss:rbp+var_48]
		0000000100001c2d         lea        rsi, qword [ss:rbp+var_50]
		0000000100001c31         mov        rdx, rbx
		0000000100001c34         mov        rcx, rbx
		```
	
		the `add` is clearly jumping over a `qword`. the two `lea` instructions are interesting. the first one is loading in `qword [ss:rbp+var_48]` and we know that the value at that address is set by this command already `mov        rax, 0x41a579bdf8000000` and then we have the second `lea` instruction which if you trace it back through the code, comes from here:
	
		```asm
		0000000100001b7c         call       imp___stubs___TFE12CoreGraphicsVSC7CGPointCfMS0_FT1xSi1ySi_S0_
		0000000100001b81         movsd      xmmword [ss:rbp+var_C8], xmm0
		```
		
		and that is the `x` value of our point (the `p` constant). we already know that `rbx` was set to `0x0000000100F76910` and we are jumping a quad word, so that means `rbx` should now be `0000000100F76918`. then we are moving that value into `rdx` and `rcx`. at the end of this madness, our gprs will be set to the following:
	
		GPR | Vaule
		---|---
		RAX | 0x41A579BDF8000000
		XMM2 | 0x000000000000000041E5555555400000
		R12 | 0x0000000100F76590
		RBX | 0x0000000100F76918
		RCX | 0x0000000100F76918
		RDX | 0x0000000100F76918
		RDI | 0x00007FFF5F01AE50
		RSI | 0x00007FFF5F01AE48
		DS | 0x00000000
		
	5. after all of this mystery, we get to the `call` instruction on `qword [ds:r12]`. what in the actual world is this?
	
	
	
	



Conclusion
===
1. The `imp___stubs___TFE12CoreGraphicsVSC7CGPointCfMS0_FT1xSi1ySi_S0_` private function takes in an x and a y value in the `rdi` and the `rsi` registers to create a `CGPoint`.
2. Usually, Swift uses the `imp___stubs__objc_msgSend` to call a function on an instance of `NSObject`. for a property, such as the `frame` property of a `UIView` however, Swift uses the `imp___stubs__objc_msgSend_stret` function but still follows the [System V AMD64](http://goo.gl/mBdSoG) calling convention.

References
===
1. [Tuples - The Swift Programming Language](http://goo.gl/6UmR8n)
2. [NASM - Writing 64-bit code](http://goo.gl/5wvq4R)
3. [System V AMD64 ABI](http://goo.gl/mBdSoG) on Wikipedia
4. [System V Application Binary Interface, AMD64 Architecture Processor Supplement, Draft Version 0.99.6](http://goo.gl/lCBYu)