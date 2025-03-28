# Exploit: Attaque de chaîne de format

**Commande**:  
```bash
python -c 'print "\x8c\x98\x04\x08" + "%60c" + "%4$n"' > /tmp/exploit4; cat /tmp/exploit4 - | ./level3`  
```

**Explication**:  
Cet exploit exploite une vulnérabilité de chaîne de format, où le spécificateur **%4$n** est utilisé pour écrire le nombre de caractères déjà imprimés par `printf` à une adresse mémoire spécifique. Cette adresse est celle du quatrième argument sur la pile. En contrôlant cette adresse, nous pouvons rediriger le flux d’exécution du programme.

**Étapes détaillées**:  
- **Identification de l’adresse de la variable m**: Utiliser GDB pour localiser l’adresse exacte de la variable m qui doit être modifiée. Lancer GDB avec la commande `gdb ./level3`, puis mettre un point d’arrêt sur la fonction où m est utilisée avec `break v`. Utiliser la commande `p &m` pour obtenir l’adresse de la variable m : `0x804988c`.
- **Détermination de l’adresse dans les arguments de printf**: Pour comprendre où les données entrées apparaissent dans la pile lors de l’exécution de printf, exécuter une commande de test qui affiche les adresses sur la pile :  
  `python -c 'print "AAAA" + " %p" * 20' - | ./level3`  
  Cela affiche les adresses sur la pile et montre où `AAAA` est situé. `AAAA` apparaîtra comme le quatrième argument imprimé (`%p`), cela signifie que l’adresse passée en entrée se trouve au quatrième argument de `printf`, donc `%4$n` peut être utilisé pour écrire à cette adresse.
- **Construction du payload**: Le payload doit écrire un nombre spécifique de caractères (dans cet exemple, 64) à l’adresse de la variable `m`. Les octets `\x8c\x98\x04\x08` représentent l’adresse de `m` (en little endian). Pour atteindre le total de 64 caractères imprimés avant d’écrire cette valeur :  
  `"\x8c\x98\x04\x08" + "%60c"`
- **Exécution finale**: Combinez ces éléments pour former le payload final qui écrit le nombre nécessaire de caractères à l’adresse ciblée, utilisant `%4$n` pour effectuer l’écriture :  
  `\x8c\x98\x04\x08" + "%60c" + "%4$n`  
  En exécutant ce payload, vous modifiez la valeur de `m` à 64 en exploitant la vulnérabilité de la chaîne de format, ce qui permet de prendre le contrôle du flux d’exécution du programme.

**Ressources**:  
- [Les failles Format String](https://repo.zenk-security.com/Techniques%20d.attaques%20%20.%20%20Failles/Les%20failles%20Format%20String.pdf)  
- [Exploit 101: Format Strings](https://axcheron.github.io/exploit-101-format-strings/)  
- [Format String Attack (OWASP)](https://owasp.org/www-community/attacks/Format_string_attack)
