## Partie 1 : Gestion des utilisateurs

### Q.1.1.1  

![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/q1.1.1.png)  

1. **J'ai ouvert** "Active Directory Users and Computers" (ADUC).
2. **J'ai navigué** dans l'OU **DirectionDesRessourcesHumaines** sous **LabUsers**.
3. **J'ai cliqué** avec le bouton droit sur **Kelly Rhameur**.
4. **J'ai sélectionné** l'option **Copy...** pour créer un nouvel utilisateur basé sur Kelly Rhameur.

Ensuite j'ai saisi les infos concernant le nouvelle utilisateur

![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/q1_lionel.png)  
![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/q1_lionel2.png)  

Résultat :

![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/q1_lionelfinal.png)  

### Q.1.1.2 

J'ai crée l'UO : clique droit sur le domaine > New > Organizational Unit

![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/q1.2_créationUO.png)   

Ensuite, j'ai désactivé le compte de Kelly Rhameur : Clique droit > Disable account, toujours dans le même menu, > move pour le déplacer dans mon UO nouvellement crée 

![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/Q1.2_desactivetdeplacement.png)  


### Q.1.1.3   

J'ai modifié le groupe en retirant Kelly Rhameur de l'OU d'origine et en mettant à jour son emplacement dans l'OU DeactivatedUsers, comme illustré.
![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/Q1.1.3_groupeUO.png)  

### Q.1.1.4    

J'ai créé le dossier individuel de Lionel Lemarchand dans le lecteur F: et archivé celui de Kelly Rhameur en le renommant avec le suffixe -ARCHIVE, comme montré ci-dessous
![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/Q1.4.png)  


## Partie 2 : Restriction utilisateurs  

### Q.1.2.1  

![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/part2q1.2.1.png)   

1. **J'ai ouvert** Active Directory Users and Computers.
2. **J'ai cliqué** sur l'icône de recherche (1), sélectionné **TSSR.LAN** (2), cherché "Gabriel" et **cliqué sur Find Now** (3).
3. **J'ai sélectionné** l'utilisateur **Gabriel.Guhl** (4).
4. **J'ai ouvert** les **Propriétés** de l'utilisateur, puis **cliqué** sur l'onglet **Account** (5).
5. **J'ai cliqué** sur **Logon Hours**, sélectionné **lundi à vendredi** de **7h à 17h** en bleu (6).
6. **J'ai validé** en cliquant sur **OK**, puis sur **Apply** et **OK**.

### Q.1.2.2  


Dans l'onglet Account des propriétés de l'utilisateur, j'ai cliqué sur Log On To, sélectionné The following computers, puis ajouté CLIENT01 comme unique ordinateur autorisé pour la connexion.  

![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/part2q1.2.2.png)  

### Q.1.2.3  

Tout d'abord, j'ai crée et activé ma GPO dans l'OU LabUsers  

![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/part2Q1.2.3.png)   

Ensuite je l'ai édité pour y intégrer la stratégie de mdp  

![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/PART2Q1.2.3strate.png)  

1. **J'ai ouvert** l'outil **Group Policy Management Editor** et **sélectionné** l'OU **LabUsers** sous **Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Account Policies** > **Password Policy** (étape 1).

2. **J'ai cliqué** sur **Minimum password length** (étape 2) et **coché** l'option **Define this policy setting**.

3. **J'ai défini** la longueur minimale du mot de passe à **8 caractères** (étape 3).

4. **J'ai validé** en cliquant sur **OK** (étape 4)


## Partie 3 : Lecteurs réseaux  

### Q.1.3.1   

Tout d'abord j'ai partagé les lecteurs E: et F: sur le réseau   

![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/PART3q1.21partage1.png)  
![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/PART3q1.21partage.png)  

Ensuite j'ai crée ma GPO Drive-Mount dans l'UO LabComputers puis je l'ai edité pour y intégrer les chemins de partage des lecteurs E et F  

![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/PART3Q1.3.png)  

![Créer l'utilisateur Lionel Lemarchand](https://github.com/MB7M/Checkpoint-3/blob/main/Images/PART3Q1.3RESULTAT.png) 






