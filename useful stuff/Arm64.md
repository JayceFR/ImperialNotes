# Calling Conventions 
Also known as aarch64
Contains 32 registers! shitting!!!!!!
![[Pasted image 20260209215547.png]]
Use `x16` and `x17` to store temporaries. 
`x30` also known as the link register is used to store the return address. 

*Note:* Stack pointer must not ever be used without being aligned to 16 bytes. When pushing values to the stack, they are pushed/popped in pairs with `stp` and `ldp`

1. The first *8* arguments for a function are passed into `x0 - x7`
2. Remaining arguments are pushed onto the stack before the call. 
3. The base pointer is not required by the calling convention.  (recommended)
4. Returns are into `x0`, except for returning stack allocated structs, which is returned with `x8/xr` (recommended)




