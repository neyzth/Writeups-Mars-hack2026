Pour le challenge 4 de la phase AD

j'ai pris l'indice au vu du nombre de point : "un service est
vulnérable"

La consigne est d'accéder a la machine MH-CLI1 et accéder au bureau de
l'administrateur pour y récupérer le flag.

En premier lieu j'ai scanné les ports de la vm pour faire une découverte
sans succès il n'y a rien d'ouvert.

L'idée de passer par wmic viens ensuite :

`evil-winrm -i 192.168.1.10 -u j.peuplu -p '6fhQ6B8Fg'`

bingo, accès au shell de la machine via l'utilisateur j.peuplu
