# Sammenligning av programmeringsspråk

Oppgave: Skriv ut tallene fra 0 til 9 på hver sin linje i konsollen.

Python:
```python
for i in range(10):
  print(i)
```

Java:
```java
public class Main {
  public static void main(String[] args) {
    for (int i = 0; i < 10; i++) {
      System.out.println(i);
    }
  }
}
```

C:
```C
#include <stdio.h>

int main() {
  for (int i = 0; i < 10; i++) {
    printf("%d\n", i);
  }
}
```

Assembly. This is essentially what you get if you compile the above C program above (before linking), with a manual facelift and comments highlighting interesting sections added afterwards. Compiled using clang with the -S flag on an M1 Mac, yielding ARM assembly language:
```
	.section	__TEXT,__text,regular,pure_instructions
	.globl	_main           ; When the program starts, go to the first instruction in _main
	.p2align	2
_main:
	sub	sp, sp, #32
	stp	x29, x30, [sp, #16]
	add	x29, sp, #16
	stur	wzr, [x29, #-4]
	str	wzr, [sp, #8]   ; Put the value 0 at the location labeled "i" (a.k.a. "[sp, #8]")
LB_CHECK:
	ldr	w8, [sp, #8]    ; Make a copy of the number in the box labeled "i" and put it in register w8
	subs	w8, w8, #9      ; Subtract 9 from the number in register w8
	b.gt	LB_ENDING       ; If result of previous computation was greater than 0, jump to ending section
	ldr	w9, [sp, #8]
	mov	x8, x9
	adrp	x0, l_.str@PAGE
	add	x0, x0, l_.str@PAGEOFF
	mov	x9, sp
	str	x8, [x9]
	bl	_printf         ; Do the print function
	ldr	w8, [sp, #8]    ; Make a copy of the number in the box labeled "i" and hold it with register w8
	add	w8, w8, #1      ; Add one to the number at register w8
	str	w8, [sp, #8]    ; Put back the number at register w8 into the box labeled with "i"
	b	LB_CHECK        ; Go back to the performing the check
LB_ENDING:
	mov	w0, #0          ; Return value 0 put in register w0
	ldp	x29, x30, [sp, #16] 
	add	sp, sp, #32
	ret
  
	.section	__TEXT,__cstring,cstring_literals
l_.str:
	.asciz	"%d\n"
```

So
