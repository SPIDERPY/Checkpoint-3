# Exercice 2 : Manipulations pratiques sur VM Linux
---
## Partie 1 : Gestion des utilisateurs

### Q.2.1.1 : Création d’un compte utilisateur personnel
Pour créer un compte utilisateur :
```bash
sudo adduser wilder
```
### Q.2.1.2 : Préconisations pour ce compte
- Utiliser un mot de passe complexe.
- Limiter les permissions aux stricts besoins (ne pas donner les droits root).
- Ajouter l'utilisateur à un groupe spécifique si nécessaire.
- Configurer une clé SSH pour sécuriser les connexions.

## Partie 2 : Configuration de SSH

### Q.2.2.1 : Désactiver complètement l'accès root
1. Pour désactiver l'accès root à distance, modifier le fichier `/etc/ssh/sshd_config` et définir la directive suivante :
```bash
2. PermitRootLogin no
```
3. Ensuite, redémarrer le service SSH :
```bash
sudo systemctl restart sshd
```
### Q.2.2.3 : Authentification par clé SSH
1. Générer une paire de clés SSH sur votre machine locale :
   ```bash
   ssh-keygen
   ```
2. Copier la clé publique générée sur le serveur SRVLX01 pour votre compte :
  ```bash
  ssh-copy-id wilder@SRVLX01
  ```
3. Désactiver l'authentification par mot de passe sur le serveur. Modifier /etc/ssh/sshd_config pour inclure :
   ```bash
   PasswordAuthentication no
   ```
4. Redémarrer le service SSH après cette modification :
   ```bash
   sudo systemctl restart sshd
   ```
   
## Partie 3 : Analyse du stockage

### Q.2.3.1 : Quels sont les systèmes de fichiers actuellement montés ?
Pour afficher les systèmes de fichiers montés, utilisez la commande suivante :
   ```bash
   df -h
   ```
### Q.2.3.2 : Quel type de système de stockage ils utilisent ?
Pour déterminer le type de système de stockage utilisé, exécutez la commande suivante :
   ```bash
   lsblk -f
   ```
### Q.2.3.3 : Ajouter le point de montage dans `/etc/fstab` pour qu'il soit monté automatiquement :
   ```bash
   echo '/dev/[nom_groupe_volumes]/sauvegardes /var/lib/bareos/storage ext4 defaults 0 0' | sudo tee -a /etc/fstab
   ```
### Q.2.3.4 : Ajouter un nouveau volume logique LVM de 2 Gio qui servira à héberger des sauvegardes.
Ce volume doit être monté automatiquement à chaque démarrage dans l'emplacement par défaut : `/var/lib/bareos/storage`.

1. Créer un volume logique LVM de 2 Gio :
   ```bash
   sudo lvcreate -L 2G -n sauvegardes [nom_groupe_volumes]
   ```
2. Formater le volume logique nouvellement créé :
   ```bash
   mkfs.ext4 /dev/[nom_groupe_volumes]/sauvegardes
   ```
3. Ajouter le point de montage dans /etc/fstab pour un montage automatique à chaque démarrage :
    ```bash
    echo '/dev/[nom_groupe_volumes]/sauvegardes /var/lib/bareos/storage ext4 defaults 0 0' | sudo tee -a /etc/fstab
```
4. Monter toutes les partitions configurées dans /etc/fstab :
   ```bash
   sudo mount -a
   ```
### Q.2.3.5 : Combien d'espace disponible reste-t-il dans le groupe de volume ?
Pour vérifier l'espace disponible dans le groupe de volumes, utilisez la commande suivante :
```bash
   sudo vgdisplay
```

## Partie 4 : Sauvegardes

### Q.2.4.1 : Expliquer succinctement les rôles respectifs des 3 composants Bareos installés sur la VM
1. **bareos-dir (Director)** :
   - C'est le composant principal qui gère la planification, l'organisation et le suivi des sauvegardes.
   - Il coordonne les tâches entre les autres composants (bareos-sd et bareos-fd).
   - Il supervise les catalogues de sauvegarde.

2. **bareos-sd (Storage Daemon)** :
   - Ce composant est responsable du stockage des données sauvegardées.
   - Il écrit les données sur les supports de stockage (disques, bandes, etc.) spécifiés dans la configuration.

3. **bareos-fd (File Daemon)** :
   - Ce composant s'exécute sur les machines clientes ou sur le serveur pour accéder aux fichiers à sauvegarder.
   - Il communique avec le Director pour envoyer les fichiers vers le Storage Daemon.

Ensemble, ces composants permettent une gestion centralisée et efficace des sauvegardes sur le serveur.

## Partie 5 : Filtrage et analyse réseau

### Q.2.5.1 : Quelles sont actuellement les règles appliquées sur Netfilter ?
Pour afficher les règles actuellement appliquées sur Netfilter, exécutez la commande suivante :
   ```bash
   sudo iptables -L
   ```
Cette commande liste toutes les règles existantes dans les chaînes d'input, output et forward.
### Q.2.5.2 : Quels types de communications sont autorisées ?
Les types de communications autorisées peuvent être déduits des règles affichées par la commande iptables -L. Par exemple :

Les règles avec la cible ACCEPT indiquent les communications autorisées.

Vérifiez les ports, protocoles et adresses IP spécifiés dans ces règles.

### Q.2.5.3 : Quels types sont interdits ?
Les types de communications interdites sont également visibles dans les règles affichées par iptables -L. Par exemple :

Les règles avec la cible DROP ou REJECT indiquent les communications interdites.

Analysez les ports, protocoles ou adresses IP bloqués par ces règles.

### Q.2.5.4 : Sur nftables, ajouter les règles nécessaires pour autoriser Bareos à communiquer avec les clients Bareos
Bareos utilise les ports TCP 9101 à 9103 pour ses communications. Pour ajouter ces règles sur nftables :

1. Ajouter les règles nécessaires :
   ```bash
   sudo nft add rule inet filter input tcp dport 9101-9103 accept
   ```
2. Sauvegarder la configuration nftables pour qu’elle soit persistante après un redémarrage :
   ```bash
   sudo nft list ruleset > /etc/nftables.conf
   ```
3. Redémarrer nftables pour appliquer les règles :
   ```bash
   sudo systemctl restart nftables
   ```

## Partie 6 : Analyse de logs

### Q.2.6.1 : Lister les 10 derniers échecs de connexion ayant eu lieu sur le serveur
Pour lister les 10 derniers échecs de connexion, utilisez la commande suivante :
   ```bash
   sudo grep "Failed password" /var/log/auth.log | tail -n 10
   ```
Cette commande permet d'extraire les tentatives échouées dans les logs d'authentification.    
Elle affiche :    
La date et l'heure de la tentative : Visible au début de chaque ligne du log.    
L'adresse IP de la machine ayant fait la tentative : Généralement indiquée après le texte "from".    
Pour analyser davantage, vous pouvez utiliser awk pour formater directement la sortie. Par exemple :
   ```bash
   sudo grep "Failed password" /var/log/auth.log | tail -n 10 | awk '{print $1, $2, $3, $11}'
   ```
Les éléments importants extraits incluront :
- La date (ex. Mar 17 10:25:43)
- L'adresse IP (ex. 192.168.1.100)




