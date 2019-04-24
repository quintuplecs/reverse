# WhiteRabbit
slopey112 - 4/24/2019

Let's take a look at the binary.
```
root@kali:~/Crack Me# ./hidden 
The only way out is inward





...Voce consegue achar a funcao escondida?
```
At first glance, we can see that nothing happens. But let's take a look at this binary under GDB.
```assembly
   0x00005555555551d5 <+0>:	push   rbp
   0x00005555555551d6 <+1>:	mov    rbp,rsp
   0x00005555555551d9 <+4>:	lea    rdi,[rip+0xe58]        # 0x555555556038
   0x00005555555551e0 <+11>:	call   0x555555555030 <puts@plt>
   0x00005555555551e5 <+16>:	lea    rdi,[rip+0xe6c]        # 0x555555556058
   0x00005555555551ec <+23>:	mov    eax,0x0
   0x00005555555551f1 <+28>:	call   0x555555555040 <printf@plt>
   0x00005555555551f6 <+33>:	lea    rdi,[rip+0xe63]        # 0x555555556060
   0x00005555555551fd <+40>:	call   0x555555555030 <puts@plt>
   0x0000555555555202 <+45>:	mov    eax,0x0
   0x0000555555555207 <+50>:	pop    rbp
   0x0000555555555208 <+51>:	ret
```
The main function is not very interesting. We can see that it does nothing but print a few lines. But let's take a look at the functions.
```
(gdb) info func
All defined functions:

Non-debugging symbols:
0x0000000000001000  _init
0x0000000000001030  puts@plt
0x0000000000001040  printf@plt
0x0000000000001050  __cxa_finalize@plt
0x0000000000001060  _start
0x0000000000001090  deregister_tm_clones
0x00000000000010c0  register_tm_clones
0x0000000000001100  __do_global_dtors_aux
0x0000000000001140  frame_dummy
0x0000000000001145  secret
0x00000000000011d5  main
0x0000000000001210  __libc_csu_init
0x0000000000001280  __libc_csu_fini
0x0000000000001284  _fini
```
Okay, so there's a ninteresting function called `secret`. Most likely, we can just modify our return address and jump to that function.
```
(gdb) b *0x0000555555555208
Breakpoint 2 at 0x555555555208
```
We'll set a breakpoint at the very last instruction of the `main` function and we'll modify the return address.
```
(gdb) set {long}0x7fffffffe1c8 = 0x0000555555555145
(gdb) x/xg $rsp
0x7fffffffe1c8:	0x0000555555555145
```
And when we take a step forward, we can see that we are now in function `secret`.
```
(gdb) si
0x0000555555555145 in secret ()
```
When we let teh function run through, we get our flag.
```
(gdb) c
Continuing.
flag{3sc0nd1d0_3h_M41s_G0st0S0}
``` 
