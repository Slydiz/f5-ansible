# Options de Configuration Ansible F5

## Virtual Server (`bigip_virtual_server`)

### Paramètres actuellement utilisés :
- `name` : Nom du Virtual Server
- `destination` : Adresse IP de destination
- `port` : Port d'écoute
- `pool` : Pool associé
- `description` : Description
- `snat` : SNAT (Source NAT) - actuellement "automap"
- `profiles` : Profils appliqués (http, tcp)

### Paramètres supplémentaires disponibles :
- `enabled_vlans` : Liste des VLANs autorisés
- `disabled_vlans` : Liste des VLANs désactivés
- `source` : Restriction d'adresse source (ex: "0.0.0.0/0")
- `irules` : iRules à appliquer
- `policies` : Politiques LTM
- `fallback_persistence_profile` : Profil de persistance de secours
- `default_persistence_profile` : Profil de persistance par défaut
- `mirror` : Miroring de connexion (yes/no)
- `mask` : Masque réseau pour le VS
- `rate_limit` : Limitation de débit (connexions/seconde)
- `rate_limit_mode` : Mode de limitation (object, object-source, object-destination, etc.)
- `ip_protocol` : Protocole IP (tcp, udp, sctp)
- `type` : Type de VS (standard, forwarding, performance-http, etc.)
- `address_translation` : Traduction d'adresse (yes/no)
- `port_translation` : Traduction de port (yes/no)
- `security_log_profiles` : Profils de logging de sécurité
- `security_nat_policy` : Politique NAT de sécurité
- `firewall_enforced_policy` : Politique de firewall appliquée
- `ip_intelligence_policy` : Politique IP Intelligence
- `clone_pools` : Pools de clonage de trafic

## Pool (`bigip_pool`)

### Paramètres actuellement utilisés :
- `name` : Nom du Pool
- `lb_method` : Méthode de load balancing
- `monitors` : Monitors de santé
- `description` : Description

### Paramètres supplémentaires disponibles :
- `slow_ramp_time` : Temps de montée en charge (secondes)
- `reselect_tries` : Nombre de tentatives de re-sélection
- `service_down_action` : Action si service down (none, reset, drop, reselect)
- `monitor_type` : Type de monitor (and_list, m_of_n, single)
- `quorum` : Nombre minimum de membres actifs
- `priority_group_activation` : Activation des groupes de priorité
- `minimum_active_members` : Nombre minimum de membres actifs
- `minimum_up_members` : Nombre minimum de membres up
- `minimum_up_members_action` : Action si minimum pas atteint
- `minimum_up_members_checking` : Vérification enabled/disabled
- `metadata` : Métadonnées personnalisées

### Méthodes de Load Balancing disponibles :
- `round-robin` : Distribution circulaire
- `least-connections-member` : Moins de connexions actives
- `least-connections-node` : Moins de connexions par node
- `least-sessions` : Moins de sessions
- `fastest-node` : Node le plus rapide (réponse)
- `observed-member` : Basé sur observations
- `predictive-member` : Prédictif
- `ratio-member` : Ratio par membre
- `ratio-node` : Ratio par node
- `dynamic-ratio-member` : Ratio dynamique par membre
- `dynamic-ratio-node` : Ratio dynamique par node
- `weighted-least-connections-member` : Pondéré par connexions
- `weighted-least-connections-node` : Pondéré par connexions (node)

### Monitors disponibles :
- `http` : HTTP GET simple
- `https` : HTTPS GET
- `tcp` : Connexion TCP
- `tcp_half_open` : TCP SYN seulement
- `icmp` : Ping ICMP
- `gateway_icmp` : ICMP gateway
- `udp` : UDP check
- `dns` : DNS query
- `external` : Script externe
- `mysql` : MySQL database
- `postgresql` : PostgreSQL database
- `smtp` : SMTP check
- `pop3` : POP3 check
- `imap` : IMAP check
- `ftp` : FTP check
- `ldap` : LDAP check
- `radius` : RADIUS check

## Node (`bigip_node`)

### Paramètres actuellement utilisés :
- `name` : Nom du Node
- `host` : Adresse IP du serveur
- `description` : Description

### Paramètres supplémentaires disponibles :
- `monitor` : Monitor spécifique pour le node
- `state` : État (present, absent, enabled, disabled, forced_offline)
- `connection_limit` : Limite de connexions simultanées
- `rate_limit` : Limite de connexions par seconde
- `ratio` : Ratio pour le load balancing
- `dynamic_ratio` : Ratio dynamique
- `availability_requirements` : Exigences de disponibilité
- `monitors` : Liste de monitors

## Pool Member (`bigip_pool_member`)

### Paramètres actuellement utilisés :
- `pool` : Nom du Pool
- `name` : Nom du Node
- `host` : Adresse IP
- `port` : Port

### Paramètres supplémentaires disponibles :
- `connection_limit` : Limite de connexions
- `rate_limit` : Limite de débit
- `ratio` : Ratio de distribution
- `priority_group` : Groupe de priorité (0-65535)
- `state` : État (present, absent, enabled, disabled, forced_offline)
- `fqdn` : FQDN au lieu d'IP
- `fqdn_auto_populate` : Auto-population FQDN
- `monitors` : Monitors spécifiques au membre
- `description` : Description

## Exemples d'utilisation avancée

### Virtual Server avec iRules et VLAN :
```yaml
bigip_virtual_server:
  name: "vs-advanced"
  destination: "10.0.0.100"
  port: "443"
  pool: "pool-web"
  profiles:
    - name: http
    - name: clientssl
      context: client-side
  irules:
    - "/Common/irule-redirect"
    - "/Common/irule-logging"
  enabled_vlans:
    - "/Common/vlan-dmz"
  rate_limit: 1000
  mirror: yes
```

### Pool avec configuration avancée :
```yaml
bigip_pool:
  name: "pool-critical"
  lb_method: "least-connections-member"
  monitors:
    - "/Common/https"
    - "/Common/tcp"
  monitor_type: "and_list"
  slow_ramp_time: 300
  minimum_active_members: 2
  service_down_action: "reselect"
```

### Node avec monitoring et limites :
```yaml
bigip_node:
  name: "server-01"
  host: "192.168.1.10"
  monitors:
    - "/Common/icmp"
  connection_limit: 500
  rate_limit: 100
```

### Pool Member avec priorité :
```yaml
bigip_pool_member:
  pool: "pool-web"
  name: "server-01"
  host: "192.168.1.10"
  port: "8080"
  priority_group: 10
  ratio: 2
  connection_limit: 1000
```
