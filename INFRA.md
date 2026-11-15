# Carte de l'infrastructure (à lire en début de session)

Dernière mise à jour : 15/11/2026. Ne contient **aucun secret** (pas de mot de passe, de clé ni de jeton) : seulement des adresses, des rôles et des noms d'utilisateur.
Légende : ✅ confirmé par l'utilisateur · 🔎 déduit d'une session précédente, à confirmer · ❓ inconnu.

## Machines

| Nom court | Fournisseur | Adresse | Utilisateur SSH | Rôle | Statut |
|---|---|---|---|---|---|
| `vps-claude` | Infomaniak | `200.234.64.136` | `claude` (très probable) | Claude Code : sessions lancées par `claude remote-control`, dans `tmux` | ✅ fournisseur, 🔎 rôle |
| `vps-bot` | Infomaniak | `179.237.64.74` | ❓ | Bot eToro **uniquement** | ✅ |
| `vps-apps` | OVH | `37.187.37.163` | ❓ | Coolify (Docker), interface `https://coolify.auto-events.fr` : Matrice, Jean Mich, eventsquiz (test) | ✅ |
| `ovh-mutu` | OVH, mutualisé | `ssh.cluster100.hosting.ovh.net:22` | `skjbfds` (login FTP principal) | Apps PHP/MySQL, une par dossier et sous-domaine | ✅ |
| GitHub | — | compte `edouardsineux-web` | — | Code source de toutes les apps, et ce dépôt `infra` | ✅ |

## Apps

### Sur `vps-apps` (Coolify)

| App | Pro / perso | Adresse | Dépôt GitHub | Statut |
|---|---|---|---|---|
| Matrice | pro | ❓ | `edouardsineux-web/matrice` | ✅ |
| Jean Mich | pro | ❓ | ❓ | ✅ hébergement |
| eventsquiz, version test | pro | `test.eventsquiz.fr` | ❓ | ✅ |

Mise en ligne : par Coolify (depuis GitHub ou l'interface). Une session cloud ne peut pas ouvrir Coolify.

### Sur `ovh-mutu` (multisite OVH : un dossier racine par site)

| Site (nom OVH) | Dossier racine | Domaines | App | Pro / perso | Statut |
|---|---|---|---|---|---|
| Website-Bocconi | `Bocconi` | 1 | Bocconi Test Prep | **perso** | ✅ |
| Website-Keytrace | `Keytrace` | 1 | Keytrace | pro | ✅ |
| Website-quiz | `quiz` | 2 | eventsquiz (production) | pro | ✅ |
| Website-qr | `qr` | 1 | QR générateur | pro | ✅ |
| Quiz BMW | `BMWQuiz` | 1 | Quiz BMW | pro | ✅ dossier |
| NSH | `sitensh` | 1 | site NSH | pro | ✅ dossier |
| Website-test | `test` | 1 | site de test | pro | ✅ dossier |
| Website-tools | `tools` | 1 | outils | pro | ✅ dossier |
| Website-www | `www` | 1 | site principal | pro | ✅ dossier |
| Website-fisca | `fisca` | 2 | fisca | pro | ✅ dossier |

Chemins : relatifs à la racine du compte (`~/<dossier>`, à vérifier par `ssh ovh ls`). La colonne « Inactif » de l'espace client concerne Git / pare-feu / CDN, pas le site lui-même. Domaines exacts : ❓ (onglet Multisite).

Bocconi : mise en ligne par ZIP + SFTP/SSH vers `Bocconi/` uniquement, puis `install/migrate.php` et `admin/import.php` (voir `UPGRADE.md` du dépôt `Bocconitest`).

### Sur `vps-bot`

| App | Pro / perso | Dépôt GitHub | Statut |
|---|---|---|---|
| Bot eToro | **perso** | `edouardsineux-web/etoro-bot` | ✅ |

### Dépôts GitHub connus

`infra`, `Bocconitest`, `etoro-bot`, `matrice`. Les autres apps (Keytrace, eventsquiz, QR, Jean Mich…) ne semblent pas avoir de dépôt sur ce compte : ❓.

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
    User skjbfds
Host vps-apps
    HostName 37.187.37.163
    User <utilisateur>
Host vps-bot
    HostName 179.237.64.74
    User <utilisateur>
```

| Accès | Clé de `vps-claude` autorisée ? |
|---|---|
| `vps-claude` → `ovh` | ❓ à vérifier : `ssh ovh ls` |
| `vps-claude` → `vps-apps` | ❓ |

## Accès depuis le PC Windows de l'utilisateur

- Clé `C:\Users\edoua\.ssh\id_ed25519`, créée le 15/11/2026 ; on ne sait pas encore sur quel serveur elle est autorisée.
- Pas encore de fichier `C:\Users\edoua\.ssh\config` : à créer avec un raccourci `Host vps-claude` (`200.234.64.136`, utilisateur `claude`).

## Résumé pro / perso

- **Perso** : Bocconi Test Prep (`ovh-mutu`, dossier `Bocconi`), Bot eToro (`vps-bot`).
- **Pro** : tout le reste (Matrice, Jean Mich, eventsquiz test et production, Keytrace, QR, Quiz BMW, NSH, tools, www, fisca, test).

## À compléter

1. Utilisateurs SSH de `vps-apps` et `vps-bot`. Où chercher : dans Coolify, menu **Servers** → le serveur → champ **User** (pour `vps-apps`, souvent `root`) ; dans le Manager Infomaniak, fiche du VPS → accès SSH (pour `vps-bot`) ; ou bien dans `~/.ssh/config` de `vps-claude` s'il existe déjà.
2. Domaines exacts de chaque site du mutualisé (onglet Multisite) : facultatif.
