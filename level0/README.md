# Exploit: Exploitation par bit setuid (s) (-rw(s)r-x---+)

**Commande**:  
```bash 
./level0 423
```

**Explication**:  
Dans ce niveau, le propriétaire du fichier est **level1**. Il dispose du bit **setuid**, qui permet d’exécuter un processus au nom d’un autre utilisateur. Dans notre cas, cela s’exécute au nom de **level1**. On peut donc exécuter **level0** avec les privilèges de **level1**, ce qui nous fait passer en tant que niveau 1.

**Ressources**:  
- [Wikipédia - Setuid](https://fr.wikipedia.org/wiki/Setuid)
