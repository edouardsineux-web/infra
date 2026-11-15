# Carte de l'infrastructure (à lire en début de session)

Dernière mise à jour : 15/11/2026. Ne contient **aucun secret** (pas de mot de passe, de clé ni de jeton) : seulement des adresses, des rôles et des noms d'utilisateur.
Légende : ✅ confirmé par l'utilisateur · 🔎 déduit d'une session précédente, à confirmer · ❓ inconnu.

## Machines

| Nom court | Fournisseur | Adresse | Utilisateur SSH | Rôle | Statut |
|---|---|---|---|---|---|
| `vps-claude` | Infomaniak | `200.234.64.136` | `claude` 🔎 | Claude Code : sessions lancées par `claude remote-control`, dans `tmux` | ✅ fournisseur, 🔎 rôle |
| `vps-bot` | Infomaniak | ❓ | ❓ | Bot eToro **uniquement** | ✅ rôle |
| `vps-apps` | OVH | `37.187.37.163` | ❓ | Coolify (Docker) : **Matrice** | ✅ adresse, 🔎 Matrice seule |
| `ovh-mutu` | OVH, mutualisé | `ssh.cluster100.hosting.ovh.net:22` | login FTP principal ❓ | Apps PHP/MySQL, une par dossier et sous-domaine | ✅ |
| GitHub | — | compte `edouardsineux-web` | — | Code source de toutes les apps, et ce dépôt `infra` | ✅ |

## Apps

| App | Pro / perso | Hébergement | Mise en ligne | Dépôt GitHub | Statut |
|---|---|---|---|---|---|
| Matrice | pro | `vps-apps` (Coolify) | Coolify | ❓ | ✅ |
| Keytrace | pro | `ovh-mutu` | SFTP/SSH vers son dossier | ❓ | ✅ « sur le mutualisé » |
| eventsquiz | pro | `ovh-mutu` | SFTP/SSH vers son dossier | ❓ | ✅ |
| QR générateur | pro | `ovh-mutu` | SFTP/SSH vers son dossier | ❓ | ✅ |
| Bocconi Test Prep | **perso** | `ovh-mutu`, propre sous-domaine et dossier | ZIP + SFTP/SSH vers son dossier, puis `install/migrate.php` et `admin/import.php` (voir `UPGRADE.md` du dépôt) | `edouardsineux-web/Bocconitest` | ✅ |
| Bot eToro | **perso** | `vps-bot` | ❓ | ❓ | ✅ |

## Règles

- **Pro et perso sont séparés.** Une session ouverte pour une app perso ne lit, ne modifie et ne déploie rien d'une app pro, et inversement. Sur le VPS Claude Code : `~/apps/pro/` et `~/apps/perso/`, une session `claude remote-control` par app, lancée dans le dossier de l'app.
- **Mutualisé OVH partagé** : le compte SSH donne accès à tous les dossiers (pro et perso). N'écrire **que** dans le dossier de l'app concernée, après avoir vérifié le chemin distant. Ne jamais supprimer ni écraser en dehors.
- **Sauvegarde** de la base (export phpMyAdmin) confirmée par l'utilisateur avant toute migration.
- Les **secrets** restent sur les machines (`config.php`, `.env`, `~/.ssh`) ou dans le gestionnaire de mots de passe de l'utilisateur : jamais dans ce dépôt, dans un chat ou dans un commit.

## Qui peut faire quoi

- **Sessions cloud** (claude.ai, app Claude Code, « environment: anthropic_cloud ») : accès à GitHub uniquement. Elles ne peuvent joindre **ni** les VPS **ni** le mutualisé (pas d'accès SSH ; le réseau n'autorise pas les adresses IP). Elles codent, testent et poussent sur GitHub.
- **Sessions lancées sur `vps-claude`** (`claude remote-control`, environment « bridge ») : elles tournent sur le VPS et peuvent se connecter en SSH aux autres machines si la clé de `vps-claude` y est autorisée. **Ce sont elles qui mettent en ligne.**
- **Coolify** : interface web sur `vps-apps` ; une session cloud ne peut pas l'ouvrir.

## Accès SSH depuis `vps-claude` (à mettre en place une fois)

Dans `~/.ssh/config` sur `vps-claude` :

```
Host ovh
    HostName ssh.cluster100.hosting.ovh.net
    User <login FTP principal>
Host vps-apps
    HostName 37.187.37.163
    User <utilisateur>
```

| Accès | Clé de `vps-claude` autorisée ? |
|---|---|
| `vps-claude` → `ovh` | ❓ à vérifier : `ssh ovh ls` |
| `vps-claude` → `vps-apps` | ❓ |

## Accès depuis le PC Windows de l'utilisateur

- Clé `C:\Users\edoua\.ssh\id_ed25519`, créée le 15/11/2026 ; on ne sait pas encore sur quel serveur elle est autorisée.
- Pas encore de fichier `C:\Users\edoua\.ssh\config` : à créer avec un raccourci `Host vps-claude` (`200.234.64.136`, utilisateur `claude`).

## À compléter

1. Adresse et utilisateur de `vps-bot`.
2. Utilisateur SSH de `vps-apps` ; vérifier dans Coolify que **seule Matrice** y tourne.
3. Login SSH du mutualisé, et nom du dossier de chaque app sous `~/www/`.
4. Noms des dépôts GitHub des autres apps.
