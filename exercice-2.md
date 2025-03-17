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
   
# Partie 3 : Analyse du stockage

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





