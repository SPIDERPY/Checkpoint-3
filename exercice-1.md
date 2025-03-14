# Exercice 1 : Manipulations pratiques sur VM Windows

---

## **Partie 1 : Gestion des utilisateurs**

### Q.1.1.1 Créer l'utilisateur Lionel Lemarchand avec les mêmes attributs que Kelly Rhameur
1. Ouvrez *Active Directory Users and Computers* sur votre VM Windows.
2. Accédez à l'OU contenant l'utilisateur **Kelly Rhameur**.
3. Faites un clic droit sur son compte et sélectionnez **Copier**.
4. Remplissez les informations pour créer l'utilisateur **Lionel Lemarchand**, ses attributs seront ainsi identiques.

---

### Q.1.1.2 Créer une OU DeactivatedUsers et déplacer le compte de Kelly dedans
1. Toujours dans *Active Directory Users and Computers*, faites un clic droit sur le domaine > **Nouveau** > **Unité d'organisation (OU)**.
2. Nommez cette OU : **DeactivatedUsers**.
3. Faites un clic droit sur le compte de **Kelly Rhameur** > **Déplacer** > choisissez l'OU **DeactivatedUsers**.

---

### Q.1.1.3 Modifier le groupe de l'OU dans laquelle était Kelly Rhameur
1. Localisez l'OU précédente de **Kelly Rhameur** dans *Active Directory Users and Computers*.
2. Faites un clic droit sur le compte de **Kelly Rhameur** > **Propriétés** > onglet **Membre de**.
3. Ajoutez ou retirez des groupes selon les besoins :
   - Pour **ajouter un groupe** : cliquez sur **Ajouter...**, entrez le nom du groupe, validez.
   - Pour **retirer un groupe** : sélectionnez le groupe et cliquez sur **Supprimer**.
4. Cliquez sur **Appliquer** pour enregistrer.

---

### Q.1.1.4 Archiver le dossier de Kelly Rhameur et créer celui de Lionel Lemarchand
1. Accédez au dossier utilisateur de Kelly Rhameur (souvent sous `C:\Users` ou un partage réseau).
2. Copiez le contenu du dossier dans un emplacement d'archivage sécurisé.
3. Créez un dossier pour Lionel Lemarchand avec les mêmes permissions via l’onglet **Sécurité** des propriétés du dossier.

---

## **Partie 2 : Restriction utilisateurs**

### Q.1.2.1 Limiter les horaires de connexion de Gabriel Ghul
1. Dans *Active Directory Users and Computers*, faites un clic droit sur le compte de **Gabriel Ghul** > **Propriétés**.
2. Accédez à l’onglet **Compte** et cliquez sur **Heures de connexion**.
3. Configurez les horaires autorisés : du lundi au vendredi, de 7h à 17h.

---

### Q.1.2.2 Restreindre les connexions au seul ordinateur CLIENT01
1. Toujours dans l’onglet **Compte**, cliquez sur **Se connecter à...**.
2. Ajoutez `CLIENT01` comme seul ordinateur autorisé.

---

### Q.1.2.3 Mettre en place une stratégie de mot de passe pour l'OU LabUsers
1. Ouvrez *Group Policy Management* sur votre serveur.
2. Créez une nouvelle GPO appelée **PasswordPolicy**.
3. Configurez les paramètres dans :
   - **Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Password Policy** :
     - Longueur minimale : 12 caractères.
     - Complexité obligatoire : caractères majuscules, minuscules, chiffres et spéciaux.
     - Durée de validité : 30 jours.
4. Appliquez cette GPO à l'OU **LabUsers**.

---

## **Partie 3 : Lecteurs réseaux**

### Q.1.3.1 Créer une GPO Drive-Mount pour monter les lecteurs E: et F:
1. Toujours dans *Group Policy Management*, créez une nouvelle GPO nommée **Drive-Mount**.
2. Accédez à **User Configuration > Preferences > Drive Maps**.
3. Ajoutez un nouveau lecteur mappé :
   - Lettre du lecteur : **E:**.
   - Chemin UNC : `\\Serveur\PartageE`.
4. Ajoutez un autre lecteur mappé :
   - Lettre du lecteur : **F:**.
   - Chemin UNC : `\\Serveur\PartageF`.
5. Activez l’option **Reconnecter** pour les deux lecteurs.
6. Appliquez la GPO à l'OU contenant les utilisateurs concernés.

---
