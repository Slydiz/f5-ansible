# Projet Ansible F5 - Configuration Lab

Projet Ansible pour automatiser la création de nodes, pools et virtual servers sur F5 BIG-IP.

## Structure du projet

```
F5 Ansible/
├── ansible.cfg                 # Configuration Ansible
├── inventory/
│   └── hosts.yml              # Inventaire F5
├── group_vars/
│   └── f5.yml                 # Variables F5 (nodes, pools, VS)
├── playbooks/
│   ├── 01-create-nodes.yml           # Créer les nodes
│   ├── 02-create-pools.yml           # Créer les pools
│   ├── 03-add-pool-members.yml       # Ajouter membres aux pools
│   ├── 04-create-virtual-servers.yml # Créer les VS
│   └── deploy-full.yml               # Déploiement complet
└── README.md
```

## Prérequis

### 1. Installer la collection F5
```powershell
ansible-galaxy collection install f5networks.f5_modules
```

### 2. Installer les dépendances Python
```powershell
pip install f5-sdk bigsuds netaddr objectpath isoparser lxml deepdiff
```

## Configuration

### Modifier l'inventaire
Éditez `inventory/hosts.yml` :
- `ansible_host`: IP de management de votre F5
- `ansible_user`: Utilisateur admin F5
- `ansible_password`: Mot de passe (à mettre dans vault en prod)

### Modifier les variables
Éditez `group_vars/f5.yml` :
- **f5_nodes**: Liste de vos 3 serveurs Debian (IPs à adapter)
- **f5_pools**: Configuration du pool (lb_method, monitors)
- **f5_pool_members**: Association nodes -> pool avec ports
- **f5_virtual_servers**: VIP, port, pool associé

## Utilisation

### Déploiement complet (recommandé)
```powershell
ansible-playbook playbooks/deploy-full.yml
```

### Déploiement par étape
```powershell
# 1. Créer uniquement les nodes
ansible-playbook playbooks/01-create-nodes.yml

# 2. Créer uniquement les pools
ansible-playbook playbooks/02-create-pools.yml

# 3. Ajouter les membres aux pools
ansible-playbook playbooks/03-add-pool-members.yml

# 4. Créer les Virtual Servers
ansible-playbook playbooks/04-create-virtual-servers.yml
```

### Mode simulation (dry-run)
```powershell
ansible-playbook playbooks/deploy-full.yml --check
```

## Configuration exemple fournie

### Nodes (3 serveurs web Debian)
- web-server-01: 192.168.10.11
- web-server-02: 192.168.10.12
- web-server-03: 192.168.10.13

### Pool
- **Nom**: pool-web-http
- **Méthode LB**: round-robin
- **Monitor**: /Common/http
- **Membres**: 3 nodes sur port 80

### Virtual Server
- **Nom**: vs-web-http
- **VIP**: 10.1.10.100:80
- **Pool**: pool-web-http
- **SNAT**: automap
- **Profiles**: http, tcp

## Sécurisation (Production)

### Créer un vault pour les credentials
```powershell
# Créer le fichier vault
ansible-vault create group_vars/vault.yml

# Contenu du vault:
vault_f5_user: admin
vault_f5_password: VotreMotDePasse
```

### Modifier hosts.yml pour utiliser le vault
```yaml
ansible_user: "{{ vault_f5_user }}"
ansible_password: "{{ vault_f5_password }}"
```

### Lancer avec vault
```powershell
ansible-playbook playbooks/deploy-full.yml --ask-vault-pass
```

## Évolutions possibles

- Ajouter des monitors customs
- Créer des VS HTTPS avec certificats SSL
- Ajouter des iRules
- Configurer des profils HTTP personnalisés
- Déployer sur plusieurs F5 (HA)

## Support

Collection utilisée: `f5networks.f5_modules` (legacy - compatible avec F5 en production)

Documentation: https://clouddocs.f5.com/products/orchestration/ansible/devel/
