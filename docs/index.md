# Sammenligning av programmeringsspråk

Oppgave: Skriv ut tallene fra 0 til 9 på hver sin linje i konsollen.

**Python:**
```python
for i in range(10):
  print(i)
```

**Java:**
```java
public class Main {
  public static void main(String[] args) {
    for (int i = 0; i < 10; i++) {
      System.out.println(i);
    }
  }
}
```

**C:**
```C
#include <stdio.h>

int main() {
  for (int i = 0; i < 10; i++) {
    printf("%d\n", i);
  }
}
```

**Assembly.** Dette er et språk som er nærmere hva datamaskinen forstår. I disse dager er det relativt få utviklere som noensinne programmerer i assembly. I stedet benyttes det programmer som *kompilator* eller *fortolker* for å automatisk oversette høyere-nivå språk til assembly. Under viser vi omtrent det du får dersom du kompilerer C-programmet over, men med litt manuelle forenklinger og forklarende kommentarer for noen interessante deler av koden. Kompilert med clang på en M1-prosessor, som altså benytter ARM sitt assembly-språk.
```
	.section	__TEXT,__text,regular,pure_instructions
	.globl	_main
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
	b.gt	LB_ENDING       ; If result of previous computation was greater than 0, go to ending section
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
	mov	w0, #0          ; Put value 0 into the return value register w0
	ldp	x29, x30, [sp, #16] 
	add	sp, sp, #32
	ret
  
	.section	__TEXT,__cstring,cstring_literals
l_.str:
	.asciz	"%d\n"
```

**Maskin-kode.** Assembly-kode kan videre oversettes til maskin-kode, som er en samling med 0'ere og 1'ere. Vi viser her koden som hexadesimale tall: hvert symbol tilsvarer fire binærer verdier (1'ere eller 0'ere). Når programmet kjøres lastes disse verdiene rent fysisk inne i datamaskinen sitt minne (RAM) som små spenningsforskjeller der 1'ere typisk har høy spenning, mens 0'ere typisk har lav spenning.
```
ff8300d1 fd7b01a9 fd430091 bfc31fb8 ff0b00b9 e80b40b9 08250071 8c010054
e90b40b9 e80309aa 00000090 00000091 e9030091 280100f9 00000094 e80b40b9 
08050011 e80b00b9 f3ffff17 00008052 fd7b41a9 ff830091 c0035fd6
```

Det er en tett korrespondandse mellom maskin-koden og assembly-koden, hvor hver linje med assembly tilsvarer en bestemt sekvens med 1'ere og 0'ere.
```
ff 83 00 d1  	=> sub	sp, sp, #32
fd 7b 01 a9  	=> stp	x29, x30, [sp, #16]
fd 43 00 91  	=> add	x29, sp, #16
bf c3 1f b8  	=> stur	wzr, [x29, #-4]
...
```
