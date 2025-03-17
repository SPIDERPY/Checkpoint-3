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
Pour désactiver l'accès root à distance, modifier le fichier `/etc/ssh/sshd_config` et définir la directive suivante :
```bash
PermitRootLogin no
```
Ensuite, redémarrer le service SSH :
```bash
sudo systemctl restart sshd
```
