Temps d'execució:
1 suma
4 sumador
7 multiplicació
24 divisió

### Instruccions més importants
.data 
.text / .code
.word (n1),(n2).. - enters word(s) of data (64-bits)
- N:   .word 1           ; Definim N = 1

ld - load 64-bit double-word s
- ld  r2, N(r0)          ; Carga r2 = N

sd - store 64-bit double-word 
- sd    r6, D(r0)          ; guardar D

l.d - load 64-bit floating-point 
- l.d f0, result(r0)       ; f0 = result (float)

s.d - store 64-bit floating-point 
- s.d f3, result(r0)       ; guardar f3 a result

halt - stops the program
- halt                   ; Finalitza el programa

daddi - add immediate
- daddi r3, r0, 2          ; r3 = 2

slti - set if less than or equal immediate
- slti r7, r1, 6           ; r7 = 1 si r1 < 6

beq - branch if pair of registers are equal 
- beq r1, r2, fi           ; si r1 == r2 salta a fi

bne - branch if pair of registers are not equal 
- bne r7, r0, bucle        ; si r7 != 0 salta a bucle

beqz - branch if register is equal to zero 
- beqz r7, fi              ; si r7 == 0 salta a fi

bnez - branch if register is not equal to zero
- bnez r7, bucle ; si r7 != 0 repeteix el bucle

nop - no operation 
- nop                      ; cicle buit (hazard pipeline)

dadd - add integers 
- dadd r5, r5, r6          ; r5 = r5 + r6

dsub - subtract integers 
- dsub r4, r4, r1          ; r4 = r4 - r1

dmul - signed integer multiplication 
- dmul r4, r3, r2          ; r4 = r3 * r2

ddiv - signed integer division
- ddiv r5, r4, r3          ; r5 = r4 / r3

add.d - add floating-point 
- add.d f0, f0, f3         ; f0 = f0 + f3 (acumular)

sub.d - subtract floating-point 
- sub.d f2, f2, f1         ; f2 = f2 - f1

mul.d - multiply floating-point 
- mul.d f3, f1, f2         ; f3 = f1 * f2

div.d - divide floating-point
- div.d f3, f1, f2       ; f3 = f1 / f2 

cvt.d.l - convert 64-bit integer to a double floating-point format
- cvt.d.l f1, f1           ; f1 (enter) → f1 (double)

cvt.l.d - convertir double → enter
-  cvt.l.d f1, f1           ; f1 (double) → f1 (enter)

mtc1 - move data from integer register to floating-point register
- mtc1 r4, f1            ; f1 = r4

