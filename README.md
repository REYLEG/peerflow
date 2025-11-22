# peerflow
# Application P2P de Partage de Fichiers - Projet Systèmes Distribués

## 📋 Description du Projet

Application complète de partage de fichiers pair-à-pair (P2P) inspirée d'uTorrent, développée dans le cadre du cours de Systèmes Distribués. Ce projet implémente les concepts fondamentaux et avancés des systèmes distribués : architecture décentralisée, communication inter-processus, gestion de la concurrence, tolérance aux pannes, et découverte de services.

**Niveau de complexité** : Avancé - Ce projet nécessite une solide compréhension des réseaux, de la programmation concurrente et des algorithmes distribués.

## 🎯 Objectifs Pédagogiques

### Concepts Fondamentaux
- Architecture peer-to-peer décentralisée
- Protocoles de communication réseau (TCP/UDP)
- Gestion de la concurrence et synchronisation
- Tolérance aux pannes et récupération d'erreurs
- Découverte et tracking de pairs distribués

### Concepts Avancés
- DHT (Distributed Hash Table) pour découverte décentralisée
- Algorithmes de sélection de pairs optimaux
- Gestion de bande passante et throttling
- Chiffrement des communications (TLS/SSL)
- Mécanismes de réputation et anti-triche

## ✨ Fonctionnalités

### Phase 1 : MVP (Minimum Viable Product) - 3 semaines
**Objectif** : Démontrer la faisabilité technique de base

- [x] Communication TCP entre 2 pairs
- [x] Transfert d'un fichier complet (sans chunking)
- [x] Découverte manuelle de pairs (IP hardcodée)
- [x] Interface CLI basique
- [x] Logging des opérations

**Critère de succès** : Transférer un fichier de 10MB entre 2 machines sur le même réseau local.

### Phase 2 : Fonctionnalités Core - 4 semaines
**Objectif** : Implémenter le système de chunks et multi-pairs

- [ ] Division des fichiers en chunks (256KB par défaut)
- [ ] Téléchargement parallèle depuis plusieurs pairs
- [ ] Tracker centralisé simple (liste des pairs actifs)
- [ ] Gestion de la file d'attente de téléchargement
- [ ] Vérification d'intégrité (SHA-256 par chunk)
- [ ] Reprise de téléchargement après interruption
- [ ] Statistiques en temps réel (vitesse, progression)
- [ ] Upload simultané pendant le téléchargement

**Critère de succès** : 5 pairs téléchargeant simultanément un fichier de 100MB avec 90% d'efficacité.

### Phase 3 : Optimisations et Robustesse - 3 semaines
**Objectif** : Améliorer performance et fiabilité

- [ ] Algorithme de sélection de pairs (rarest-first, random-first)
- [ ] Limitation de bande passante configurable
- [ ] Gestion avancée des timeouts et reconnexions
- [ ] Cache des chunks pour optimiser les uploads
- [ ] Support de fichiers multiples simultanés
- [ ] Système de priorité des téléchargements
- [ ] Détection et bannissement des pairs malveillants
- [ ] Compression optionnelle des données

**Critère de succès** : Réseau de 20 pairs stables pendant 1 heure avec pannes simulées.

### Phase 4 : Fonctionnalités Avancées - 4 semaines
**Objectif** : Implémenter des concepts distribués avancés

- [ ] DHT (Kademlia) pour découverte décentralisée
- [ ] Magnet links (pas besoin de fichier .torrent)
- [ ] Chiffrement TLS pour les connexions
- [ ] NAT traversal (UPnP, hole punching)
- [ ] Système de réputation des pairs
- [ ] Support IPv6
- [ ] Interface web (WebSocket + React)
- [ ] API REST pour intégration externe
- [ ] Metrics et monitoring (Prometheus)

**Critère de succès** : Système complètement décentralisé fonctionnel sur Internet public.

### Phase 5 : Polish et Documentation - 2 semaines
**Objectif** : Finaliser pour présentation académique

- [ ] Tests unitaires complets (>80% couverture)
- [ ] Tests d'intégration end-to-end
- [ ] Tests de charge et stress tests
- [ ] Documentation technique complète
- [ ] Rapport académique détaillé
- [ ] Présentation et démonstration live
- [ ] Dockerisation pour déploiement facile

## 🏗️ Architecture Détaillée

### Vue d'Ensemble du Système

```
┌──────────────────────────────────────────────────────────────────┐
│                     RÉSEAU P2P GLOBAL                            │
│                                                                   │
│  ┌─────────────┐         ┌─────────────┐      ┌─────────────┐  │
│  │   Peer A    │◄───────►│   Peer B    │◄────►│   Peer C    │  │
│  │  (Seeder)   │         │ (Leecher)   │      │ (Leecher)   │  │
│  └──────┬──────┘         └──────┬──────┘      └──────┬──────┘  │
│         │                       │                      │         │
│         └───────────────────────┼──────────────────────┘         │
│                                 │                                │
│                        ┌────────▼────────┐                       │
│                        │  DHT Network    │                       │
│                        │  (Distributed)  │                       │
│                        └────────┬────────┘                       │
│                                 │                                │
│                        ┌────────▼────────┐                       │
│                        │  Tracker Server │                       │
│                        │   (Optional)    │                       │
│                        └─────────────────┘                       │
└──────────────────────────────────────────────────────────────────┘
```

### Architecture d'un Pair Individuel

```
┌─────────────────────────────────────────────────────────────┐
│                    APPLICATION PEER                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌────────────────────────────────────────────────────┐    │
│  │              USER INTERFACE LAYER                  │    │
│  │  ├─ CLI Interface                                  │    │
│  │  ├─ Web Interface (optional)                       │    │
│  │  └─ REST API                                       │    │
│  └────────────────┬───────────────────────────────────┘    │
│                   │                                         │
│  ┌────────────────▼───────────────────────────────────┐    │
│  │           APPLICATION CORE LAYER                   │    │
│  │                                                     │    │
│  │  ┌──────────────────────────────────────────────┐ │    │
│  │  │  Torrent Manager                            │ │    │
│  │  │  - Gestion sessions                         │ │    │
│  │  │  - File d'attente                           │ │    │
│  │  │  - Orchestration globale                    │ │    │
│  │  └──────────────────────────────────────────────┘ │    │
│  │                                                     │    │
│  │  ┌──────────────────┐    ┌──────────────────┐    │    │
│  │  │  Peer Manager    │    │  File Manager    │    │    │
│  │  │  - Découverte    │    │  - Chunking      │    │    │
│  │  │  - Connexions    │    │  - Assembly      │    │    │
│  │  │  - Reputation    │    │  - Verification  │    │    │
│  │  └──────────────────┘    └──────────────────┘    │    │
│  │                                                     │    │
│  │  ┌──────────────────┐    ┌──────────────────┐    │    │
│  │  │  Piece Manager   │    │  Bandwidth Mgr   │    │    │
│  │  │  - Sélection     │    │  - Throttling    │    │    │
│  │  │  - Priorité      │    │  - QoS           │    │    │
│  │  │  - Stratégie     │    │  - Statistics    │    │    │
│  │  └──────────────────┘    └──────────────────┘    │    │
│  └─────────────────────────────────────────────────┘    │
│                   │                                         │
│  ┌────────────────▼───────────────────────────────────┐    │
│  │           NETWORK LAYER                            │    │
│  │                                                     │    │
│  │  ┌──────────────────┐    ┌──────────────────┐    │    │
│  │  │  Protocol Layer  │    │  Transport Layer │    │    │
│  │  │  - BitTorrent    │    │  - TCP Sockets   │    │    │
│  │  │  - DHT           │    │  - UDP Sockets   │    │    │
│  │  │  - PEX           │    │  - TLS/SSL       │    │    │
│  │  └──────────────────┘    └──────────────────┘    │    │
│  │                                                     │    │
│  │  ┌──────────────────┐    ┌──────────────────┐    │    │
│  │  │  Connection Pool │    │  Event Loop      │    │    │
│  │  │  - Max connexions│    │  - Async I/O     │    │    │
│  │  │  - Keep-alive    │    │  - Callbacks     │    │    │
│  │  └──────────────────┘    └──────────────────┘    │    │
│  └─────────────────────────────────────────────────┘    │
│                   │                                         │
│  ┌────────────────▼───────────────────────────────────┐    │
│  │           STORAGE LAYER                            │    │
│  │                                                     │    │
│  │  ┌──────────────────┐    ┌──────────────────┐    │    │
│  │  │  Metadata Store  │    │  Piece Cache     │    │    │
│  │  │  - SQLite        │    │  - LRU Cache     │    │    │
│  │  │  - Torrents info │    │  - Memory mapped │    │    │
│  │  └──────────────────┘    └──────────────────┘    │    │
│  │                                                     │    │
│  │  ┌──────────────────┐    ┌──────────────────┐    │    │
│  │  │  File System     │    │  Config Store    │    │    │
│  │  │  - Downloads     │    │  - Settings      │    │    │
│  │  │  - Uploads       │    │  - Preferences   │    │    │
│  │  └──────────────────┘    └──────────────────┘    │    │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
```

### Protocole de Communication P2P

#### 1. Phase de Découverte
```
Pair A                  Tracker/DHT                Pair B
  │                          │                        │
  │─────── ANNOUNCE ────────>│                        │
  │  (Je partage fichier X)  │                        │
  │                          │                        │
  │<────── PEER LIST ────────│                        │
  │  (Pairs ayant X)         │                        │
  │                          │                        │
  │────────────────── CONNECT ────────────────────────>│
  │                                                    │
```

#### 2. Phase de Handshake
```
Pair A                                            Pair B
  │                                                  │
  │────────────── HANDSHAKE ───────────────────────>│
  │  protocol: "BitTorrent"                         │
  │  info_hash: SHA1(torrent)                       │
  │  peer_id: "CLIENT-VERSION-RANDOM"               │
  │                                                  │
  │<───────────── HANDSHAKE ─────────────────────────│
  │                                                  │
  │────────────── BITFIELD ────────────────────────>│
  │  [1,1,0,1,0,0,1,1,...]  (chunks disponibles)   │
  │                                                  │
  │<───────────── BITFIELD ──────────────────────────│
  │                                                  │
```

#### 3. Phase de Transfert
```
Pair A (Leecher)                              Pair B (Seeder)
  │                                                  │
  │─────────── INTERESTED ─────────────────────────>│
  │  (Je veux télécharger)                          │
  │                                                  │
  │<────────── UNCHOKE ──────────────────────────────│
  │  (OK, je t'envoie des données)                  │
  │                                                  │
  │─────────── REQUEST ─────────────────────────────>│
  │  piece_index: 5                                 │
  │  begin: 0                                        │
  │  length: 16384                                   │
  │                                                  │
  │<────────── PIECE ────────────────────────────────│
  │  piece_index: 5                                 │
  │  begin: 0                                        │
  │  data: [binary data 16KB]                       │
  │                                                  │
  │─────────── HAVE ────────────────────────────────>│
  │  piece_index: 5 (Je l'ai maintenant)            │
  │                                                  │
```

#### 4. Types de Messages du Protocole

| Message | ID | Payload | Description |
|---------|----|---------| ------------|
| CHOKE | 0 | Aucun | Stop d'upload vers ce pair |
| UNCHOKE | 1 | Aucun | Reprise d'upload vers ce pair |
| INTERESTED | 2 | Aucun | Intérêt pour des pièces du pair |
| NOT_INTERESTED | 3 | Aucun | Plus d'intérêt |
| HAVE | 4 | piece_index (4 bytes) | Notification de nouvelle pièce |
| BITFIELD | 5 | bitfield | État complet des pièces |
| REQUEST | 6 | index, begin, length | Demande d'un bloc |
| PIECE | 7 | index, begin, data | Envoi d'un bloc |
| CANCEL | 8 | index, begin, length | Annulation de requête |
| PORT | 9 | port (2 bytes) | Port DHT |

### Algorithmes Clés

#### 1. Algorithme de Sélection de Pièces

**Rarest First** (pour pairs normaux) :
```python
def select_next_piece(available_pieces, peer_pieces):
    """
    Sélectionne la pièce la plus rare parmi celles disponibles
    """
    # Compter la rareté de chaque pièce
    rarity = {}
    for piece_index in available_pieces:
        count = sum(1 for peer in connected_peers 
                   if peer.has_piece(piece_index))
        rarity[piece_index] = count
    
    # Trier par rareté (moins présente = plus prioritaire)
    sorted_pieces = sorted(rarity.items(), key=lambda x: x[1])
    
    # Retourner la plus rare
    return sorted_pieces[0][0]
```

**Random First** (pour nouveaux pairs) :
```python
def select_first_pieces(available_pieces, count=4):
    """
    Sélectionne aléatoirement les premières pièces
    pour devenir rapidement utile au réseau
    """
    import random
    return random.sample(available_pieces, min(count, len(available_pieces)))
```

**Endgame Mode** (dernières pièces) :
```python
def endgame_strategy(remaining_pieces):
    """
    Demande les pièces restantes à TOUS les pairs
    pour finir rapidement
    """
    for piece in remaining_pieces:
        for peer in all_connected_peers:
            if peer.has_piece(piece):
                peer.request_piece(piece)
```

#### 2. Algorithme de Choking

**Optimistic Unchoking** :
```python
def update_choking_status():
    """
    - Unchoke les 4 meilleurs uploadeurs
    - Unchoke 1 pair aléatoire toutes les 30s (optimistic)
    - Choke tous les autres
    """
    # Trier pairs par vitesse d'upload
    sorted_peers = sorted(peers, key=lambda p: p.upload_rate, reverse=True)
    
    # Unchoke top 4
    for peer in sorted_peers[:4]:
        peer.unchoke()
    
    # Optimistic unchoke (exploration)
    if time.now() - last_optimistic > 30:
        random_peer = random.choice(sorted_peers[4:])
        random_peer.unchoke()
        last_optimistic = time.now()
    
    # Choke le reste
    for peer in sorted_peers[5:]:
        peer.choke()
```

#### 3. DHT (Kademlia) - Découverte Décentralisée

**Structure de Données** :
```python
class DHTNode:
    def __init__(self):
        self.node_id = random_160_bit_id()  # ID unique du nœud
        self.routing_table = KBuckets()      # Table de routage
        self.data_store = {}                 # Stockage local
    
class KBucket:
    """
    Stocke jusqu'à K=8 contacts proches de notre ID
    """
    def __init__(self, range_min, range_max):
        self.contacts = []  # Max 8 contacts
        self.range = (range_min, range_max)
        self.last_updated = time.now()
```

**Opérations DHT** :
```python
# FIND_NODE - Trouver les K nœuds les plus proches d'un ID
def find_node(target_id):
    closest = routing_table.get_closest_nodes(target_id, K=8)
    for node in closest:
        response = node.send_find_node(target_id)
        closest.update(response)
    return closest

# ANNOUNCE_PEER - Annoncer qu'on a un fichier
def announce_peer(info_hash):
    closest_nodes = find_node(info_hash)
    for node in closest_nodes:
        node.store_peer(info_hash, my_ip, my_port)

# GET_PEERS - Trouver des pairs ayant un fichier
def get_peers(info_hash):
    closest_nodes = find_node(info_hash)
    peers = []
    for node in closest_nodes:
        peers.extend(node.get_peers(info_hash))
    return peers
```

## 🛠️ Stack Technique Complète

### Backend
- **Langage** : Python 3.10+ (asyncio pour concurrence)
  - Alternative : Rust (performance maximale), Go (concurrence native)
- **Networking** : 
  - `asyncio` + `aiohttp` - I/O asynchrone
  - `socket` - Communication TCP/UDP bas niveau
  - `ssl` - Chiffrement TLS
- **Sérialisation** :
  - Bencode (format BitTorrent standard)
  - Protocol Buffers (performance)
  - JSON (debugging et API)
- **Storage** :
  - SQLite - Métadonnées et configuration
  - File system - Chunks et fichiers
  - Redis (optionnel) - Cache distribué
- **Cryptographie** :
  - `hashlib` - SHA-1, SHA-256 pour intégrité
  - `cryptography` - Chiffrement optionnel

### Frontend (Optionnel)
- **Framework** : React 18+ avec TypeScript
- **État** : Redux Toolkit ou Zustand
- **UI** : Material-UI ou Tailwind CSS
- **Communication** : WebSocket pour temps réel
- **Visualisation** : D3.js pour graphe du réseau

### Infrastructure
- **Containerisation** : Docker + Docker Compose
- **CI/CD** : GitHub Actions
- **Monitoring** : 
  - Prometheus - Métriques
  - Grafana - Dashboards
  - ELK Stack - Logs (optionnel)
- **Tests** :
  - pytest - Tests unitaires
  - pytest-asyncio - Tests async
  - locust - Tests de charge
  - Docker - Tests d'intégration multi-container

## 📦 Installation et Configuration

### Prérequis Système
```bash
# OS : Linux (Ubuntu 20.04+), macOS 11+, Windows 10+ avec WSL2
# Python 3.10+
# Docker 20.10+ (pour tests distribués)
# Ports ouverts : 6881-6889 (TCP/UDP)
# RAM : 4GB minimum, 8GB recommandé
# Disque : 50GB minimum pour tests
```

### Installation Complète

#### 1. Clone et Setup Environnement
```bash
# Cloner le repository
git clone https://github.com/votre-username/p2p-file-sharing.git
cd p2p-file-sharing

# Créer environnement virtuel
python3 -m venv venv
source venv/bin/activate  # Linux/Mac
# ou
venv\Scripts\activate  # Windows

# Installer dépendances de développement
pip install -r requirements-dev.txt

# Installer pre-commit hooks
pre-commit install
```

#### 2. Configuration Initiale
```bash
# Copier fichier de configuration exemple
cp config.example.ini config.ini

# Éditer configuration
nano config.ini

# Créer répertoires nécessaires
mkdir -p shared downloads logs data/metadata
```

#### 3. Setup Base de Données
```bash
# Initialiser base de données SQLite
python scripts/init_db.py

# Vérifier migrations
alembic upgrade head
```

#### 4. Tests d'Installation
```bash
# Tests unitaires
pytest tests/unit/

# Test de connexion réseau
python scripts/network_test.py

# Démarrer en mode debug
python main.py --debug --port 6881
```

### Configuration Avancée

#### Fichier `config.ini`
```ini
[Network]
# Port d'écoute principal
listen_port = 6881
# Plage de ports pour connexions sortantes
port_range_min = 6882
port_range_max = 6889
# Nombre maximum de connexions simultanées
max_connections = 50
max_connections_per_torrent = 30
# Timeout de connexion
connection_timeout = 30
# Keep-alive interval
keepalive_interval = 120

[Transfer]
# Taille d'un chunk (en bytes)
chunk_size = 262144  # 256 KB
# Taille d'un bloc (sous-partie d'un chunk)
block_size = 16384   # 16 KB
# Nombre de requêtes en pipeline
max_pipelined_requests = 5
# Nombre de chunks demandés en avance
look_ahead_chunks = 3

[Paths]
# Répertoire des fichiers partagés
shared_directory = ./shared
# Répertoire des téléchargements
download_directory = ./downloads
# Répertoire des fichiers temporaires (chunks incomplets)
temp_directory = ./downloads/.incomplete
# Répertoire des métadonnées
metadata_directory = ./data/metadata
# Fichier de log
log_file = ./logs/p2p.log

[Tracker]
# Activer le tracker centralisé
enabled = true
# Adresse du tracker
tracker_url = http://localhost:8000/announce
# Intervalle d'annonce (secondes)
announce_interval = 1800
# Nombre minimum de pairs à demander
min_peers = 20

[DHT]
# Activer DHT (Kademlia)
enabled = true
# Port DHT (UDP)
port = 6881
# Taille des K-buckets
bucket_size = 8
# Timeout de requête DHT
request_timeout = 5
# Bootstrap nodes (pour rejoindre réseau)
bootstrap_nodes = 
    router.bittorrent.com:6881
    dht.transmissionbt.com:6881

[Bandwidth]
# Limite upload (KB/s, 0 = illimité)
max_upload_rate = 0
# Limite download (KB/s, 0 = illimité)
max_download_rate = 0
# Limite globale (KB/s)
max_total_rate = 0
# Réserver bande passante pour protocol overhead
protocol_overhead_ratio = 0.1

[Choking]
# Nombre de peers à unchoke
unchoke_slots = 4
# Intervalle de mise à jour du choking (secondes)
choking_interval = 10
# Intervalle d'optimistic unchoke (secondes)
optimistic_unchoke_interval = 30

[Performance]
# Taille du cache de pièces (MB)
piece_cache_size = 256
# Activer memory-mapped files
use_mmap = true
# Nombre de threads pour I/O disque
disk_io_threads = 4
# Taille du buffer de lecture
read_buffer_size = 1048576  # 1 MB

[Security]
# Activer chiffrement des connexions
encryption_enabled = false
# Niveau de chiffrement requis (none, optional, required)
encryption_level = optional
# Vérifier signature des pairs
verify_peer_signatures = false
# Bloquer pairs malveillants automatiquement
auto_ban_malicious = true
# Durée de bannissement (secondes)
ban_duration = 3600

[Logging]
# Niveau de log (DEBUG, INFO, WARNING, ERROR, CRITICAL)
log_level = INFO
# Taille maximale du fichier de log (MB)
log_max_size = 100
# Nombre de fichiers de backup
log_backup_count = 5
# Log vers console aussi
log_to_console = true

[WebUI]
# Activer interface web
enabled = false
# Port de l'interface web
port = 8080
# Autoriser accès externe
allow_external = false
# Authentification requise
require_auth = true
username = admin
password = changeme

[API]
# Activer API REST
enabled = false
# Port API
port = 8081
# Token d'authentification
api_token = your-secret-token-here

[Advanced]
# Mode strict du protocole BitTorrent
strict_protocol = false
# Support des extensions de protocole
support_extensions = true
# Extensions activées (PEX, DHT, FAST, etc.)
enabled_extensions = PEX,FAST,METADATA
# Activer UPnP pour NAT traversal
enable_upnp = true
# Activer NAT-PMP
enable_natpmp = true
```

## 🚀 Guide d'Utilisation Complet

### Commandes CLI

#### Démarrage et Configuration
```bash
# Démarrage standard
python main.py

# Démarrage avec configuration personnalisée
python main.py --config /path/to/config.ini

# Mode daemon (arrière-plan)
python main.py --daemon

# Mode debug verbeux
python main.py --debug --log-level DEBUG

# Spécifier port et répertoires
python main.py --port 6881 --shared-dir ./shared --download-dir ./downloads
```

#### Commandes Interactives

**Gestion des Torrents** :
```bash
# Ajouter un torrent via fichier .torrent
> add /path/to/file.torrent

# Ajouter via magnet link
> add magnet:?xt=urn:btih:...

# Créer un nouveau torrent
> create /path/to/file.mp4 --tracker http://tracker.example.com

# Lister tous les torrents
> list
ID  Name                Size      Status      Progress  Peers  Down    Up
1   ubuntu-22.04.iso   3.5 GB    Downloading 45%       12     2.5MB/s 500KB/s
2   movie.mkv          8.2 GB    Seeding     100%      8      0       1.2MB/s

# Voir détails d'un torrent
> info 1
Name: ubuntu-22.04.iso
Size: 3.5 GB
Pieces: 14000 (256 KB each)
Downloaded: 1.58 GB (45%)
Uploaded: 234 MB
Ratio: 0.15
Peers: 12 connected (45 in swarm)
Seeds: 8
Leechers: 4
Download rate: 2.5 MB/s
Upload rate: 500 KB/s
ETA: 23 minutes
Created: 2024-11-15
Tracker: http://tracker.ubuntu.com/announce
```

**Contrôle des Téléchargements** :
```bash
# Pause un torrent
> pause 1

# Reprendre un torrent
> resume 1

# Arrêter et supprimer (garde fichiers)
> remove 1

# Supprimer avec fichiers
> remove 1 --delete-files

# Priorité (high, normal, low)
> priority 1 high

# Limiter vitesse pour un torrent (KB/s)
> limit 1 --down 1000 --up 500
```

**Gestion des Pairs** :
```bash
# Lister pairs connectés
> peers
Torrent: ubuntu-22.04.iso
IP Address        Port   Client          Progress  Down    Up      Status
192.168.1.50      6881   μTorrent/3.5   100%      500KB/s 2MB/s   Seeder
10.0.0.123        6882   qBittorrent    67%       1MB/s   300KB/s Leecher
...

# Bannir un pair
> ban 192.168.1.50

# Débannir
> unban 192.168.1.50

# Lister pairs bannis
> banlist
```

**Gestion des Fichiers** :
```bash
# Lister fichiers partagés
> share list
Shared Files:
1. ubuntu-22.04.iso (3.5 GB) - 15 seeders
2. document.pdf (2.3 MB) - 3 seeders
3. music_album.zip (450 MB) - 7 seeders

# Partager un nouveau fichier
> share add /path/to/file.mp4

# Arrêter de partager
> share remove 2

# Vérifier intégrité d'un fichier
> verify 1
Verifying ubuntu-22.04.iso...
Progress: [====================] 100%
Result: ✓ All 14000 pieces verified successfully
```

**Statistiques et Monitoring** :
```bash
# Statistiques globales
> stats
Global Statistics:
Total Downloaded: 45.2 GB
Total Uploaded: 128.7 GB
Share Ratio: 2.85
Active Torrents: 3
Seeding: 12
Total Peers: 47
Uptime: 5 days, 3 hours
Average Download: 1.2 MB/s
Average Upload: 800 KB/s

# Statistiques réseau
> network
Network Status:
Listen Port: 6881
UPnP Status: Enabled (Port forwarded)
DHT Status: Active (1247 nodes)
DHT Torrents: 8
Incoming Connections: 23
Outgoing Connections: 24
Bandwidth Used: 2.1 MB/s (Down: 1.3 MB/s, Up: 800 KB/s)

# Historique des transferts
> history
Date         Torrent              Downloaded  Uploaded  Ratio
2024-11-20   movie.mkv           8.2 GB      15.3 GB   1.87
2024-11-19   album.zip           450 MB      1.2 GB    2.67
2024-11-18   ubuntu-22.04.iso    3.5 GB      8.9 GB    2.54
```

**Configuration en Live** :
```bash
# Voir configuration actuelle
> config show

# Modifier configuration
> config set max_download_rate 5000
> config set max_upload_rate 2000
> config set max_connections 100

# Sauvegarder configuration
> config save

# Recharger configuration depuis fichier
> config reload
```

**Utilitaires** :
```bash
# Tester connexion avec un pair
> ping 192.168.1.50:6881

# Rechercher torrents (si DHT activé)
> search "ubuntu 22.04"

# Export liste des torrents
> export torrents.json

# Import liste
> import torrents.json

# Nettoyer fichiers temporaires
> cleanup

# Aide
> help
> help add
```

### Exemples d'Usage Complets

#### Scénario 1 : Partager un gros fichier
```bash
# Terminal 1 - Créer et partager
$ python main.py --port 6881
> create /path/to/bigfile.zip --name "My Big File"
Torrent created: mybigfile.torrent
Info Hash: a1b2c3d4e5f6. **Tests et Validation** (6 pages)
   - Stratégie de tests
   - Tests unitaires et d'intégration
   - Tests de performance
   - Scénarios de pannes
   - Résultats et analyse

7. **Évaluation de Performance** (7 pages)
   - Métriques mesurées (débit, latence, scalabilité)
   - Protocole expérimental
   - Résultats et graphiques
   - Comparaison avec systèmes existants
   - Analyse des goulots d'étranglement

8. **Défis et Solutions** (4 pages)
   - Problèmes rencontrés
   - Solutions implémentées
   - Compromis et décisions techniques
   - Leçons apprises

9. **Extensions Futures** (2 pages)
   - Améliorations possibles
   - Fonctionnalités non implémentées
   - Pistes de recherche

10. **Conclusion** (1 page)
    - Récapitulatif des réalisations
    - Apports pédagogiques
    - Perspectives

11. **Annexes**
    - Spécification complète du protocole
    - Diagrammes UML
    - Extraits de code complets
    - Résultats de tests détaillés
    - Manuel d'utilisation

## 🚨 Pièges Courants et Solutions

### Problème 1 : NAT Traversal

**Symptôme** : Les pairs ne peuvent pas se connecter depuis l'extérieur.

**Solutions** :
```python
# 1. UPnP (Universal Plug and Play)
import miniupnpc

def setup_upnp(port):
    """Ouvrir automatiquement le port via UPnP"""
    upnp = miniupnpc.UPnP()
    upnp.discoverdelay = 200
    upnp.discover()
    upnp.selectigd()
    
    # Mapper port externe -> port interne
    upnp.addportmapping(
        port,           # port externe
        'TCP',
        upnp.lanaddr,   # IP locale
        port,           # port interne
        'P2P Client',
        ''
    )
    logger.info(f"Port {port} forwarded via UPnP")

# 2. Hole Punching UDP
def udp_hole_punch(peer_address, stun_server):
    """
    Utiliser STUN pour découvrir l'IP publique
    et créer un "trou" dans le NAT
    """
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.bind(('0.0.0.0', 0))
    
    # Requête STUN
    public_ip, public_port = get_public_address(sock, stun_server)
    
    # Envoyer paquet UDP vers le pair
    # pour créer entrée NAT
    sock.sendto(b'PUNCH', peer_address)
    
    return public_ip, public_port
```

### Problème 2 : Race Conditions sur les Chunks

**Symptôme** : Corruption de fichiers, chunks dupliqués.

**Solution** :
```python
import asyncio
from asyncio import Lock

class PieceManager:
    def __init__(self):
        self.pieces = {}
        self.locks = {}  # Lock par pièce
        
    async def write_piece(self, piece_index, data):
        """Écriture thread-safe d'une pièce"""
        
        # Obtenir ou créer lock pour cette pièce
        if piece_index not in self.locks:
            self.locks[piece_index] = Lock()
        
        async with self.locks[piece_index]:
            # Vérifier si pas déjà écrite
            if piece_index in self.pieces:
                logger.warning(f"Piece {piece_index} already written, skipping")
                return False
            
            # Vérifier intégrité
            if not self.verify_piece(piece_index, data):
                raise IntegrityError(f"Piece {piece_index} failed verification")
            
            # Écrire sur disque
            await self.file_manager.write_piece(piece_index, data)
            
            # Marquer comme complète
            self.pieces[piece_index] = True
            
            logger.info(f"Piece {piece_index} written successfully")
            return True
```

### Problème 3 : Deadlock dans le Choking

**Symptôme** : Tous les pairs sont choked, aucun transfert.

**Solution** :
```python
class ChokingManager:
    def __init__(self):
        self.unchoke_slots = 4
        self.optimistic_unchoke_interval = 30
        
    def update_choking(self, peers):
        """
        Garantir qu'au moins un pair est toujours unchoked
        """
        # Trier pairs par contribution
        sorted_peers = sorted(
            peers, 
            key=lambda p: p.uploaded_bytes, 
            reverse=True
        )
        
        # TOUJOURS unchoke le meilleur
        if sorted_peers:
            sorted_peers[0].unchoke()
        
        # Unchoke top 4
        for peer in sorted_peers[:self.unchoke_slots]:
            peer.unchoke()
        
        # Optimistic unchoke (évite deadlock)
        if len(sorted_peers) > self.unchoke_slots:
            random_peer = random.choice(sorted_peers[self.unchoke_slots:])
            random_peer.unchoke()
            logger.debug(f"Optimistic unchoke: {random_peer.address}")
        
        # Choke le reste
        for peer in sorted_peers[self.unchoke_slots + 1:]:
            peer.choke()
```

### Problème 4 : Memory Leak avec Connexions

**Symptôme** : Utilisation mémoire qui augmente indéfiniment.

**Solution** :
```python
import weakref
from contextlib import asynccontextmanager

class ConnectionPool:
    def __init__(self, max_connections=50):
        self.connections = weakref.WeakValueDictionary()
        self.max_connections = max_connections
        self.semaphore = asyncio.Semaphore(max_connections)
        
    @asynccontextmanager
    async def get_connection(self, peer_address):
        """Context manager garantissant la fermeture"""
        async with self.semaphore:
            conn = await self._create_connection(peer_address)
            try:
                yield conn
            finally:
                await self._close_connection(conn)
    
    async def _create_connection(self, address):
        """Créer connexion avec timeout"""
        try:
            conn = await asyncio.wait_for(
                PeerConnection.connect(address),
                timeout=30
            )
            self.connections[address] = conn
            return conn
        except asyncio.TimeoutError:
            raise ConnectionError(f"Timeout connecting to {address}")
    
    async def _close_connection(self, conn):
        """Fermer proprement"""
        try:
            await conn.close()
        except Exception as e:
            logger.error(f"Error closing connection: {e}")
        finally:
            # Libérer ressources
            if conn.address in self.connections:
                del self.connections[conn.address]

# Usage
async def download_from_peer(peer_address, piece_index):
    async with connection_pool.get_connection(peer_address) as conn:
        data = await conn.request_piece(piece_index)
        return data
    # Connexion automatiquement fermée ici
```

### Problème 5 : Performance I/O Disque

**Symptôme** : Goulot d'étranglement sur écriture disque.

**Solution** :
```python
import aiofiles
import mmap
from concurrent.futures import ThreadPoolExecutor

class DiskIOManager:
    def __init__(self, num_threads=4):
        self.executor = ThreadPoolExecutor(max_workers=num_threads)
        self.write_cache = {}
        self.cache_size = 256 * 1024 * 1024  # 256 MB
        
    async def write_piece_async(self, file_path, offset, data):
        """Écriture asynchrone non-bloquante"""
        loop = asyncio.get_event_loop()
        
        # Exécuter I/O dans thread pool
        await loop.run_in_executor(
            self.executor,
            self._write_piece_sync,
            file_path, offset, data
        )
    
    def _write_piece_sync(self, file_path, offset, data):
        """Écriture synchrone avec memory-mapped file"""
        with open(file_path, 'r+b') as f:
            # Memory map pour performance
            with mmap.mmap(f.fileno(), 0) as mm:
                mm[offset:offset+len(data)] = data
                mm.flush()
    
    async def batch_write(self, writes):
        """Écriture par batch pour optimiser"""
        tasks = [
            self.write_piece_async(path, offset, data)
            for path, offset, data in writes
        ]
        await asyncio.gather(*tasks)
```

## 🎓 Aspects Pédagogiques Approfondis

### Démonstration des Concepts du Cours

#### 1. Temps Logique et Ordonnancement

**Application** : Ordonnancement des requêtes de chunks
```python
class LogicalClock:
    """
    Implémentation d'horloge de Lamport pour 
    ordonner les événements distribués
    """
    def __init__(self):
        self.time = 0
        self.lock = asyncio.Lock()
    
    async def tick(self):
        """Incrémenter l'horloge"""
        async with self.lock:
            self.time += 1
            return self.time
    
    async def update(self, received_time):
        """Mise à jour à la réception d'un message"""
        async with self.lock:
            self.time = max(self.time, received_time) + 1
            return self.time

# Usage dans le protocole
class PeerConnection:
    async def send_request(self, piece_index):
        timestamp = await self.clock.tick()
        message = {
            'type': 'REQUEST',
            'piece': piece_index,
            'timestamp': timestamp
        }
        await self.send(message)
    
    async def handle_request(self, message):
        # Mettre à jour horloge avec temps reçu
        await self.clock.update(message['timestamp'])
        # Traiter requête...
```

#### 2. Exclusion Mutuelle Distribuée

**Application** : Prévenir plusieurs pairs d'écrire le même chunk
```python
class DistributedLock:
    """
    Algorithme de Ricart-Agrawala pour exclusion mutuelle
    """
    def __init__(self, peer_id, all_peers):
        self.peer_id = peer_id
        self.all_peers = all_peers
        self.request_queue = []
        self.replies_received = set()
        
    async def acquire(self, resource_id):
        """Acquérir lock distribué"""
        # Envoyer REQUEST à tous les pairs
        timestamp = await self.clock.tick()
        for peer in self.all_peers:
            await peer.send_lock_request(resource_id, timestamp)
        
        # Attendre réponses de TOUS les pairs
        while len(self.replies_received) < len(self.all_peers):
            await asyncio.sleep(0.1)
        
        # Lock acquis
        logger.info(f"Lock acquired for resource {resource_id}")
    
    async def release(self, resource_id):
        """Libérer lock"""
        self.replies_received.clear()
        
        # Envoyer REPLY à tous dans la queue
        for peer, req_timestamp in self.request_queue:
            await peer.send_lock_reply(resource_id)
        
        self.request_queue.clear()
```

#### 3. Consensus Distribué

**Application** : Accord sur l'ordre des chunks à télécharger
```python
class ConsensusProtocol:
    """
    Simplified Paxos pour consensus sur priorité des pièces
    """
    def __init__(self, peer_id, quorum_size):
        self.peer_id = peer_id
        self.quorum_size = quorum_size
        self.proposals = {}
        
    async def propose_priority(self, piece_priorities):
        """
        Proposer un ordre de priorité et obtenir consensus
        """
        proposal_id = (self.clock.time, self.peer_id)
        
        # Phase 1: Prepare
        promises = await self.send_prepare(proposal_id)
        
        if len(promises) < self.quorum_size:
            return None  # Pas de quorum
        
        # Phase 2: Accept
        accepts = await self.send_accept(proposal_id, piece_priorities)
        
        if len(accepts) >= self.quorum_size:
            # Consensus atteint
            return piece_priorities
        else:
            return None
```

#### 4. Détection de Pannes

**Application** : Heartbeat et détection de pairs morts
```python
class FailureDetector:
    """
    Détecteur de pannes basé sur heartbeat
    """
    def __init__(self, timeout=30, check_interval=10):
        self.timeout = timeout
        self.check_interval = check_interval
        self.last_heartbeat = {}
        self.suspected = set()
        
    async def start_monitoring(self, peers):
        """Démarrer surveillance des pairs"""
        while True:
            now = time.time()
            
            for peer in peers:
                last_seen = self.last_heartbeat.get(peer.id, 0)
                
                if now - last_seen > self.timeout:
                    if peer.id not in self.suspected:
                        self.suspected.add(peer.id)
                        logger.warning(f"Peer {peer.id} suspected failed")
                        await self.handle_suspected_failure(peer)
                
            await asyncio.sleep(self.check_interval)
    
    async def receive_heartbeat(self, peer_id):
        """Recevoir heartbeat d'un pair"""
        self.last_heartbeat[peer_id] = time.time()
        
        # Retirer des suspects si présent
        if peer_id in self.suspected:
            self.suspected.remove(peer_id)
            logger.info(f"Peer {peer_id} recovered")
```

#### 5. Réplication et Cohérence

**Application** : Maintenir cohérence des métadonnées entre pairs
```python
class ReplicationManager:
    """
    Gestion de la réplication avec cohérence éventuelle
    """
    def __init__(self, replication_factor=3):
        self.replication_factor = replication_factor
        self.version_vectors = {}
        
    async def replicate_metadata(self, torrent_id, metadata):
        """
        Répliquer métadonnées sur plusieurs pairs
        avec vector clocks pour cohérence
        """
        # Incrémenter version vector
        if torrent_id not in self.version_vectors:
            self.version_vectors[torrent_id] = {}
        
        self.version_vectors[torrent_id][self.peer_id] = \
            self.version_vectors[torrent_id].get(self.peer_id, 0) + 1
        
        # Sélectionner pairs de réplication
        replica_peers = self.select_replica_peers(
            torrent_id, 
            self.replication_factor
        )
        
        # Envoyer à tous les réplicas
        tasks = [
            peer.store_metadata(
                torrent_id, 
                metadata, 
                self.version_vectors[torrent_id]
            )
            for peer in replica_peers
        ]
        
        results = await asyncio.gather(*tasks, return_exceptions=True)
        
        # Vérifier quorum d'écriture
        successful = sum(1 for r in results if not isinstance(r, Exception))
        if successful < self.replication_factor // 2 + 1:
            raise ReplicationError("Failed to achieve write quorum")
    
    def resolve_conflicts(self, version_vectors):
        """
        Résoudre conflits avec vector clocks
        """
        # Comparer version vectors
        # Implémenter happened-before relation
        pass
```

## 📊 Benchmarks et Résultats Attendus

### Métriques de Performance

#### 1. Débit de Transfert

**Test** : Fichier de 1GB, réseau local 1Gbps

| Nombre de Pairs | Débit Moyen | Temps Total | Efficacité |
|----------------|-------------|-------------|------------|
| 1 seeder, 1 leecher | 95 MB/s | 11s | 95% |
| 1 seeder, 5 leechers | 80 MB/s | 13s | 80% |
| 1 seeder, 10 leechers | 65 MB/s | 16s | 65% |
| 5 seeders, 10 leechers | 110 MB/s | 9s | 110% |

**Observation** : Le débit augmente avec plusieurs seeders grâce au téléchargement parallèle.

#### 2. Scalabilité

**Test** : Impact du nombre de pairs sur performance

```python
# Script de benchmark
async def benchmark_scalability():
    results = []
    
    for num_peers in [5, 10, 20, 50, 100]:
        start_time = time.time()
        
        # Créer swarm
        swarm = create_swarm(
            num_seeders=1,
            num_leechers=num_peers
        )
        
        # Mesurer temps jusqu'à 90% completion
        await wait_for_completion(swarm, threshold=0.9)
        
        elapsed = time.time() - start_time
        results.append({
            'peers': num_peers,
            'time': elapsed,
            'throughput': calculate_throughput(swarm)
        })
    
    return results
```

**Résultats attendus** :
- Scalabilité quasi-linéaire jusqu'à 50 pairs
- Saturation autour de 100 pairs (limitée par seeder)

#### 3. Tolérance aux Pannes

**Test** : Déconnexion aléatoire de pairs

```python
async def test_fault_tolerance():
    # Démarrer téléchargement
    leecher = create_peer(role='leecher')
    seeders = create_peers(count=5, role='seeder')
    
    # Déconnecter 60% des seeders aléatoirement
    while not leecher.is_complete():
        await asyncio.sleep(5)
        
        # Kill random seeder
        if random.random() < 0.3:
            victim = random.choice(seeders)
            await victim.disconnect()
            seeders.remove(victim)
    
    # Vérifier complétion malgré pannes
    assert leecher.verify_integrity()
```

**Résultat attendu** : Complétion réussie même avec 60% de pannes.

#### 4. Latence Réseau

**Test** : Impact de la latence sur performance

| Latence RTT | Débit | Impact |
|-------------|-------|--------|
| 0ms (local) | 95 MB/s | Baseline |
| 10ms | 85 MB/s | -10% |
| 50ms | 60 MB/s | -37% |
| 100ms | 40 MB/s | -58% |
| 200ms | 25 MB/s | -74% |

**Mitigation** : Augmenter pipeline depth pour latence élevée.

## 🔬 Expérimentations Avancées

### Expérience 1 : Comparaison d'Algorithmes de Sélection

**Hypothèse** : Rarest-first est plus efficace que random selection.

**Protocole** :
```python
async def compare_selection_algorithms():
    algorithms = ['random', 'rarest_first', 'sequential']
    results = {}
    
    for algo in algorithms:
        # Setup identique pour tous
        swarm = create_swarm(seeders=2, leechers=10)
        
        for leecher in swarm.leechers:
            leecher.set_selection_algorithm(algo)
        
        # Mesurer temps de complétion
        start = time.time()
        await wait_for_all_complete(swarm.leechers)
        elapsed = time.time() - start
        
        results[algo] = {
            'time': elapsed,
            'avg_peers_utilized': calculate_avg_peers(swarm)
        }
    
    return results
```

**Résultats attendus** :
- Rarest-first : 15% plus rapide que random
- Sequential : 30% plus lent (mauvaise distribution)

### Expérience 2 : Impact du Choking Interval

**Question** : Quel est l'intervalle optimal de mise à jour du choking ?

**Test** :
```python
async def test_choking_intervals():
    intervals = [5, 10, 20, 30, 60]  # secondes
    
    for interval in intervals:
        swarm = create_swarm(seeders=1, leechers=20)
        swarm.seeder.set_choking_interval(interval)
        
        # Mesurer fairness et throughput
        metrics = await run_swarm_for_duration(swarm, duration=300)
        
        results[interval] = {
            'fairness_index': calculate_jain_fairness(metrics),
            'total_throughput': sum(m.download_rate for m in metrics)
        }
```

**Résultat attendu** : Optimum autour de 10-20 secondes (trade-off fairness/overhead).

### Expérience 3 : Taille Optimale des Chunks

**Question** : Quelle taille de chunk maximise le débit ?

| Taille Chunk | Overhead | Granularité | Débit |
|--------------|----------|-------------|-------|
| 64 KB | Haut (3%) | Excellente | 80 MB/s |
| 128 KB | Moyen (2%) | Bonne | 90 MB/s |
| 256 KB | Bas (1%) | Bonne | 95 MB/s |
| 512 KB | Très bas (0.5%) | Moyenne | 93 MB/s |
| 1 MB | Minimal (0.3%) | Faible | 85 MB/s |

**Conclusion** : 256 KB est le sweet spot (standard BitTorrent).

## 🎯 Critères d'Évaluation Académique

### Grille de Notation Suggérée

#### Fonctionnalités (35 points)
- [10] Transfert P2P fonctionnel entre 2 pairs
- [10] Système de chunks avec vérification d'intégrité
- [5] Téléchargement depuis pairs multiples
- [5] Tracker ou DHT fonctionnel
- [5] Interface utilisateur claire

#### Architecture et Code (25 points)
- [8] Architecture modulaire et extensible
- [7] Code propre, commenté, PEP8
- [5] Gestion d'erreurs robuste
- [5] Tests unitaires (>50% couverture)

#### Concepts Distribués (20 points)
- [5] Décentralisation effective
- [5] Gestion de la concurrence
- [5] Tolérance aux pannes démontrée
- [5] Scalabilité mesurée

#### Documentation et Rapport (15 points)
- [5] README complet
- [5] Rapport académique structuré
- [3] Diagrammes et schémas clairs
- [2] Documentation API/code

#### Présentation (5 points)
- [3] Démonstration fonctionnelle
- [2] Clarté des explications

### Bonus (+10 points possibles)
- [+3] Interface web réactive
- [+3] DHT (Kademlia) complet
- [+2] Chiffrement des communications
- [+2] Métriques et monitoring avancés

## 🤝 Workflow de Développement Recommandé

### Semaine 1-2 : Prototype Minimal
```bash
git checkout -b feature/mvp
```

**Objectifs** :
- [ ] Communication TCP entre 2 pairs
- [ ] Handshake simple
- [ ] Transfert d'un fichier texte (sans chunks)
- [ ] Logging basique

**Critère de succès** : Transférer "hello.txt" entre 2 terminaux.

### Semaine 3-4 : Chunking et Multi-Peers
```bash
git checkout -b feature/chunking
```

**Objectifs** :
- [ ] Division fichiers en chunks de 256KB
- [ ] Vérification SHA-256
- [ ] Support de 3+ pairs simultanés
- [ ] Tracker centralisé basique

**Critère de succès** : 3 pairs téléchargeant un fichier de 10MB.

### Semaine 5-6 : Robustesse
```bash
git checkout -b feature/robustness
```

**Objectifs** :
- [ ] Gestion des timeouts
- [ ] Reconnexion automatique
- [ ] Reprise après crash
- [ ] Tests de pannes

**Critère de succès** : Complétion après déconnexion de 50% des pairs.

### Semaine 7-8 : Optimisations
```bash
git checkout -b feature/optimization
```

**Objectifs** :
- [ ] Algorithme rarest-first
- [ ] Choking efficace
- [ ] Limitation bande passante
- [ ] Métriques de performance

### Semaine 9-10 : Features Avancées
```bash
git checkout -b feature/advanced
```

**Objectifs** :
- [ ] DHT basique
- [ ] Interface web (optionnel)
- [ ] API REST
- [ ] Documentation complète

### Semaine 11-12 : Polish et Tests
```bash
git checkout -b feature/final-polish
```

**Objectifs** :
- [ ] Tests complets (>80% couverture)
- [ ] Correction bugs
- [ ] Rapport académique
- [ ] Préparation présentation

## 📚 Ressources et Références

### Documentation Officielle
- [BEP 0003 - BitTorrent Protocol](http://www.bittorrent.org/beps/bep_0003.html)
- [BEP 0005 - DHT Protocol](http://www.bittorrent.org/beps/bep_0005.html)
- [BEP 0010 - Extension Protocol](http://www.bittorrent.org/beps/bep_0010.html)

### Livres Recommandés
- **Distributed Systems** - Maarten van Steen & Andrew S. Tanenbaum
- **Designing Data-Intensive Applications** - Martin Kleppmann
- **Computer Networking: A Top-Down Approach** - Kurose & Ross

### Articles Académiques
- Cohen, B. (2003). *"Incentives Build Robustness in BitTorrent"*
- Legout, A., et al. (2006). *"Rarest First and Choke Algorithms Are Enough"*
- Bharambe, A., et al. (2006). *"Analyzing and Improving BitTorrent Performance"*

### Implémentations de Référence
- [libtorrent](https://www.libtorrent.org/) - Bibliothèque C++ complète
- [qBittorrent](https://github.com/qbittorrent/qBittorrent) - Client open-source
- [Transmission](https://github.com/transmission/transmission) - Client léger

### Outils de Debug
```bash
# Wireshark - Analyser trafic réseau
wireshark -i eth0 -f "tcp port 6881"

# tcpdump - Capture paquets
tcpdump -i eth0 -w capture.pcap port 6881

# netstat - Voir connexions actives
netstat -an | grep 6881

# iftop - Monitorer bande passante
sudo iftop -i eth0 -f "port 6881"
```

## 🐛 Debugging et Troubleshooting

### Debug Logging Avancé

```python
# src/utils/logger.py
import logging
import sys
from logging.handlers import RotatingFileHandler

def setup_logger(name, level=logging.INFO):
    """
    Logger avec rotation et format détaillé
    """
    logger = logging.getLogger(name)
    logger.setLevel(level)
    
    # Format détaillé
    formatter = logging.Formatter(
        '[%(asctime)s] %(levelname)-8s '
        '[%(name)s:%(lineno)d] '
        '[Peer:%(peer_id)s] '
        '%(message)s',
        datefmt='%Y-%m-%d %H:%M:%S'
    )
    
    # Handler fichier avec rotation
    file_handler = RotatingFileHandler(
        'logs/p2p.log',
        maxBytes=100*1024*1024,  # 100 MB
        backupCount=5
    )
    file_handler.setFormatter(formatter)
    logger.addHandler(file_handler)
    
    # Handler console
    console_handler = logging.StreamHandler(sys.stdout)
    console_handler.setFormatter(formatter)
    logger.addHandler(console_handler)
    
    return logger

# Usage avec contexte
logger = setup_logger('p2p')
logger = logging.LoggerAdapter(logger, {'peer_id': self.peer_id})
logger.info("Connected to peer", extra={'peer_addr': peer.address})
```

### Problèmes Fréquents

#### "Connection refused"
```bash
# Vérifier que le port est ouvert
sudo ufw allow 6881/tcp
sudo firewall-cmd --add-port=6881/tcp --permanent

# Vérifier processus écoutant
netstat -tuln | grep 6881
```

#### "Too many open files"
```bash
# Augmenter limite système
ulimit -n 65536

# Permanent dans /etc/security/limits.conf
* soft nofile 65536
* hard nofile 65536
```

#### "Memory leak"
```python
# Utiliser tracemalloc pour debug
import tracemalloc

tracemalloc.start()

# ... code ...

snapshot = tracemalloc.take_snapshot()
top_stats = snapshot.statistics('lineno')

for stat in top_stats[:10]:
    print(stat)
```

## 🎬 Conclusion et Prochaines Étapes

Ce README fournit une base complète pour développer votre application P2P de partage de fichiers. Voici les prochaines étapes recommandées :

### Démarrage Immédiat

1. **Cloner et initialiser le projet**
```bash
mkdir p2p-file-sharing
cd p2p-file-sharing
git init
python -m venv venv
source venv/bin/activate
pip install asyncio aiofiles
```

2. **Créer le prototype MVP (Semaine 1)**
```bash
# Structure minimale
mkdir -p src/{core,peer,network,utils}
touch src/__init__.py
touch src/core/{__init__.py,session.py}
touch src/peer/{__init__.py,peer.py}
touch src/network/{__init__.py,protocol.py}
touch main.py
```

3. **Premier objectif : Handshake entre 2 pairs**
```python
# main.py - Version minimale
import asyncio
from src.network.protocol import PeerConnection

async def main():
    # Démarrer serveur
    server = await PeerConnection.start_server(port=6881)
    print("Listening on port 6881...")
    
    # Ou se connecter comme client
    # client = await PeerConnection.connect("localhost", 6881)
    # await client.handshake()

if __name__ == "__main__":
    asyncio.run(main())
```

### Checklist de Développement

#### Phase 1 : Foundation (Semaines 1-2)
- [ ] Setup projet et environnement
- [ ] Classe `Peer` basique
- [ ] Connexion TCP entre 2 pairs
- [ ] Handshake protocol
- [ ] Transfert fichier simple (sans chunking)
- [ ] Logging configuré
- [ ] Tests unitaires basiques

**Validation** : Démontrer transfert de "test.txt" entre 2 terminaux

#### Phase 2 : Core Features (Semaines 3-4)
- [ ] Système de chunking (256KB)
- [ ] Calcul et vérification SHA-256
- [ ] Classe `TorrentFile` (métadonnées)
- [ ] Support multi-pairs (3+)
- [ ] Tracker HTTP simple
- [ ] File d'attente de téléchargement
- [ ] Barre de progression

**Validation** : 3 pairs téléchargent un fichier de 50MB

#### Phase 3 : Distribution (Semaines 5-6)
- [ ] Algorithme piece selection (rarest-first)
- [ ] Choking/Unchoking
- [ ] Téléchargement parallèle optimisé
- [ ] Upload pendant download
- [ ] Gestion des peers (connect/disconnect)
- [ ] Tests d'intégration

**Validation** : 10 pairs en swarm, efficacité >70%

#### Phase 4 : Robustness (Semaines 7-8)
- [ ] Gestion timeout et reconnexion
- [ ] Reprise après crash
- [ ] Validation intégrité complète
- [ ] Ban manager (pairs malveillants)
- [ ] Tests de pannes
- [ ] Monitoring basique

**Validation** : Complétion après 50% pannes simulées

#### Phase 5 : Advanced (Semaines 9-10)
- [ ] DHT (Kademlia) - optionnel mais impressionnant
- [ ] Magnet links
- [ ] Limitation bande passante
- [ ] API REST
- [ ] Interface web - optionnel
- [ ] Métriques Prometheus

**Validation** : Système complètement décentralisé fonctionnel

#### Phase 6 : Polish (Semaines 11-12)
- [ ] Tests complets (>80% coverage)
- [ ] Documentation code complète
- [ ] Rapport académique
- [ ] Diagrammes UML/séquence
- [ ] Guide d'utilisation
- [ ] Préparation présentation
- [ ] Vidéo de démonstration

**Validation** : Projet prêt pour évaluation

### Quick Start - Première Itération

**Objectif** : Avoir quelque chose qui fonctionne en 2 jours

```python
# Version ultra-simplifiée pour démarrer
# src/simple_peer.py

import socket
import json

class SimplePeer:
    def __init__(self, port):
        self.port = port
        self.sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        
    def start_server(self):
        """Démarrer en mode serveur"""
        self.sock.bind(('0.0.0.0', self.port))
        self.sock.listen(1)
        print(f"Listening on port {self.port}")
        
        conn, addr = self.sock.accept()
        print(f"Connection from {addr}")
        
        # Recevoir fichier
        data = b''
        while True:
            chunk = conn.recv(4096)
            if not chunk:
                break
            data += chunk
        
        # Sauvegarder
        with open('received_file', 'wb') as f:
            f.write(data)
        
        print(f"Received {len(data)} bytes")
        conn.close()
    
    def send_file(self, target_host, target_port, filepath):
        """Envoyer fichier à un pair"""
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.connect((target_host, target_port))
        
        with open(filepath, 'rb') as f:
            data = f.read()
            sock.sendall(data)
        
        print(f"Sent {len(data)} bytes")
        sock.close()

# Test
if __name__ == "__main__":
    import sys
    
    if sys.argv[1] == "server":
        peer = SimplePeer(6881)
        peer.start_server()
    else:
        peer = SimplePeer(6882)
        peer.send_file("localhost", 6881, sys.argv[2])
```

**Tester** :
```bash
# Terminal 1
python src/simple_peer.py server

# Terminal 2
echo "Hello P2P!" > test.txt
python src/simple_peer.py client test.txt
```

### Ressources de Démarrage

#### Templates de Code

**Protocol Message** :
```python
# src/network/message.py
from enum import IntEnum
from dataclasses import dataclass

class MessageType(IntEnum):
    CHOKE = 0
    UNCHOKE = 1
    INTERESTED = 2
    NOT_INTERESTED = 3
    HAVE = 4
    BITFIELD = 5
    REQUEST = 6
    PIECE = 7
    CANCEL = 8

@dataclass
class Message:
    type: MessageType
    payload: bytes
    
    def encode(self) -> bytes:
        """Encoder en format wire"""
        length = 1 + len(self.payload)
        return (
            length.to_bytes(4, 'big') +
            bytes([self.type]) +
            self.payload
        )
    
    @staticmethod
    def decode(data: bytes) -> 'Message':
        """Décoder depuis format wire"""
        length = int.from_bytes(data[:4], 'big')
        msg_type = MessageType(data[4])
        payload = data[5:4+length]
        return Message(msg_type, payload)
```

**Torrent Metadata** :
```python
# src/core/torrent.py
import hashlib
from dataclasses import dataclass
from typing import List

@dataclass
class TorrentInfo:
    name: str
    total_size: int
    piece_length: int
    pieces: List[bytes]  # SHA-256 hashes
    
    @property
    def num_pieces(self) -> int:
        return len(self.pieces)
    
    def piece_hash(self, index: int) -> bytes:
        return self.pieces[index]
    
    @staticmethod
    def create_from_file(filepath: str, piece_length: int = 262144):
        """Créer métadonnées depuis un fichier"""
        import os
        
        name = os.path.basename(filepath)
        total_size = os.path.getsize(filepath)
        pieces = []
        
        with open(filepath, 'rb') as f:
            while True:
                chunk = f.read(piece_length)
                if not chunk:
                    break
                piece_hash = hashlib.sha256(chunk).digest()
                pieces.append(piece_hash)
        
        return TorrentInfo(name, total_size, piece_length, pieces)
```

#### Scripts Utilitaires

**Créer fichier de test** :
```python
# scripts/create_test_file.py
import os
import random

def create_test_file(filename, size_mb):
    """Créer fichier binaire aléatoire"""
    size_bytes = size_mb * 1024 * 1024
    
    with open(filename, 'wb') as f:
        # Écrire par chunks de 1MB
        chunk_size = 1024 * 1024
        remaining = size_bytes
        
        while remaining > 0:
            chunk = os.urandom(min(chunk_size, remaining))
            f.write(chunk)
            remaining -= len(chunk)
    
    print(f"Created {filename} ({size_mb} MB)")

if __name__ == "__main__":
    import sys
    create_test_file(sys.argv[1], int(sys.argv[2]))
```

**Test de connectivité** :
```python
# scripts/test_connection.py
import socket
import sys

def test_connection(host, port):
    """Tester si un pair est accessible"""
    try:
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.settimeout(5)
        sock.connect((host, port))
        print(f"✓ Connection successful to {host}:{port}")
        sock.close()
        return True
    except Exception as e:
        print(f"✗ Connection failed: {e}")
        return False

if __name__ == "__main__":
    test_connection(sys.argv[1], int(sys.argv[2]))
```

### Conseils de Développement

#### 1. Développement Itératif
- **Commencez SIMPLE** : Un seul fichier, 2 pairs, pas de chunks
- **Ajoutez UNE feature à la fois**
- **Testez après chaque ajout**
- **Committez souvent** : `git commit -m "Add basic handshake"`

#### 2. Debugging
```python
# Ajoutez des prints partout au début
def send_piece(self, peer, piece_index):
    print(f"[DEBUG] Sending piece {piece_index} to {peer.address}")
    data = self.get_piece_data(piece_index)
    print(f"[DEBUG] Piece size: {len(data)} bytes")
    peer.send(data)
    print(f"[DEBUG] Piece sent successfully")
```

#### 3. Tests Manuels Progressifs
```bash
# Étape 1 : Test local
Terminal 1: python main.py --port 6881
Terminal 2: python main.py --port 6882 --connect localhost:6881

# Étape 2 : Test réseau local
Machine A: python main.py --port 6881
Machine B: python main.py --port 6882 --connect 192.168.1.X:6881

# Étape 3 : Test Internet (avec port forwarding)
Machine distante: python main.py --port 6881
Votre machine: python main.py --port 6882 --connect <IP_PUBLIC>:6881
```

#### 4. Gestion du Temps
- **Semaines 1-4** : 60% du temps → Features de base
- **Semaines 5-8** : 25% du temps → Optimisations
- **Semaines 9-12** : 15% du temps → Polish et docs

**Gardez 2 semaines de buffer pour les imprévus !**

### Points de Contrôle (Checkpoints)

#### Checkpoint 1 (Fin Semaine 2)
**Question** : "Puis-je transférer un fichier entre 2 pairs ?"
- Si OUI → Continuez Phase 2
- Si NON → Débuggez avant d'avancer

#### Checkpoint 2 (Fin Semaine 4)
**Question** : "Ai-je un système de chunks fonctionnel ?"
- Si OUI → Continuez Phase 3
- Si NON → Simplifiez l'architecture

#### Checkpoint 3 (Fin Semaine 8)
**Question** : "Le système est-il robuste aux pannes ?"
- Si OUI → Ajoutez features avancées
- Si NON → Focus sur stabilité

#### Checkpoint 4 (Fin Semaine 10)
**Question** : "Ai-je de quoi présenter ?"
- Si OUI → Commencez documentation
- Si NON → Freeze features, documentez l'existant

### Démonstration Finale

**Préparation de la démo** :
```bash
# Script de démo automatisé
# demo.sh

#!/bin/bash

echo "=== Démonstration P2P File Sharing ==="

# Setup
echo "1. Préparation environnement..."
python scripts/create_test_file.py demo_file.mp4 100

# Démarrer tracker
echo "2. Démarrage tracker..."
python main.py --tracker &
TRACKER_PID=$!
sleep 2

# Démarrer seeder
echo "3. Démarrage seeder..."
python main.py --port 6881 --seed demo_file.mp4 &
SEEDER_PID=$!
sleep 2

# Démarrer leechers
echo "4. Démarrage 3 leechers..."
for port in 6882 6883 6884; do
    python main.py --port $port --download demo_file.mp4 &
done

echo "5. Téléchargement en cours..."
echo "   Surveillez les logs pour voir la progression"

# Attendre complétion
sleep 60

# Cleanup
echo "6. Nettoyage..."
kill $TRACKER_PID $SEEDER_PID
pkill -f "main.py"

echo "=== Démonstration terminée ==="
```

**Points à présenter** :
1. **Architecture** : Diagrammes et explications (5 min)
2. **Démo live** : Transfert réel entre machines (5 min)
3. **Code highlight** : Montrer algorithmes clés (3 min)
4. **Tests** : Résultats de benchmarks (2 min)
5. **Défis** : Problèmes rencontrés et solutions (3 min)
6. **Q&A** : Questions du jury (2 min)

### Support et Aide

#### Si vous êtes bloqué

**Problème technique** :
1. Consultez les logs détaillés
2. Utilisez `pdb` pour debugger : `import pdb; pdb.set_trace()`
3. Testez chaque composant isolément
4. Cherchez dans les issues GitHub de projets similaires

**Manque de temps** :
1. Priorisez les fonctionnalités core
2. Documentez ce qui est fait
3. Listez ce qui reste dans "Future Work"
4. Un MVP bien fait > Projet complexe incomplet

**Doutes sur architecture** :
1. Dessinez les diagrammes sur papier
2. Discutez avec des pairs/professeur
3. Inspirez-vous de libtorrent (code bien structuré)
4. Refactorisez progressivement, pas tout d'un coup

### Ressources Additionnelles

**Vidéos recommandées** :
- "How BitTorrent Works" - Computerphile
- "Building a BitTorrent Client" - LiveOverflow
- "DHT explained" - P2P Networks series

**Repositories inspirants** :
- [pieces](https://github.com/eliben/pieces) - Client Python éducatif
- [torrent-client](https://github.com/veggiedefender/torrent-client) - Go implementation
- [libtorrent documentation](https://libtorrent.org/reference.html)

**Forums et Communautés** :
- r/BitTorrent - Reddit
- Stack Overflow - Tag [bittorrent]
- BitTorrent specifications mailing list

### Derniers Conseils

1. **Commencez MAINTENANT** - Ne passez pas trop de temps à planifier
2. **Échouez vite** - Testez vos hypothèses rapidement
3. **Documentez en cours de route** - Pas à la fin
4. **Versionnez tout** - Commits fréquents
5. **Demandez de l'aide** - Ne restez pas bloqué > 2h
6. **Amusez-vous** - C'est un super projet !

### Ligne d'Arrivée

**Critères de succès minimum** :
- ✅ Transfert P2P fonctionnel
- ✅ Système de chunks avec vérification
- ✅ Au moins 3 pairs en swarm
- ✅ Code propre et testé
- ✅ Documentation claire
- ✅ Démonstration convaincante

**Ce qui ferait "Wow"** :
- 🌟 DHT complètement fonctionnel
- 🌟 Interface web moderne
- 🌟 Tests exhaustifs avec métriques
- 🌟 Performance impressionnante
- 🌟 Code production-ready

---

## 📞 Informations de Contact et Contribution

### Mainteneurs du Projet
- **Votre Nom** - [votre.email@university.edu]
- **GitHub** : [@votre-username](https://github.com/votre-username)

### Contribution
Les contributions sont bienvenues ! Pour contribuer :
1. Fork le projet
2. Créez une branche feature (`git checkout -b feature/AmazingFeature`)
3. Committez vos changements (`git commit -m 'Add some AmazingFeature'`)
4. Push vers la branche (`git push origin feature/AmazingFeature`)
5. Ouvrez une Pull Request

### Rapport de Bugs
Utilisez les GitHub Issues avec le template :
```markdown
**Description du bug**
Description claire et concise

**Étapes pour reproduire**
1. Démarrer avec '...'
2. Exécuter '...'
3. Observer '...'

**Comportement attendu**
Ce qui devrait se passer

**Logs**
```
Insérez les logs pertinents
```

**Environnement**
- OS: [e.g., Ubuntu 22.04]
- Python: [e.g., 3.10.5]
- Version: [e.g., v1.2.0]
```

### License
Ce projet est sous licence MIT - voir le fichier [LICENSE](LICENSE) pour plus de détails.

### Remerciements
- Professeur [Nom] pour le cours de Systèmes Distribués
- Bram Cohen pour l'invention du protocole BitTorrent
- Communauté open-source pour les implémentations de référence
- Camarades de classe pour les discussions enrichissantes

---

## 🎓 Note Finale

Ce projet représente une excellente opportunité d'apprentissage pratique des systèmes distribués. Les concepts que vous allez implémenter (P2P, consensus, tolérance aux pannes, DHT) sont utilisés dans des systèmes réels à grande échelle :

- **Bitcoin/Blockchain** - Réseau P2P et consensus distribué
- **IPFS** - Système de fichiers distribué avec DHT
- **Cassandra/DynamoDB** - Bases de données distribuées
- **Kubernetes** - Orchestration distribuée
- **CDN** - Distribution de contenu

Bon courage dans votre développement ! 🚀

---

**Version** : 1.0.0  
**Dernière mise à jour** : Novembre 2024  
**Statut** : Documentation complète pour projet académique

---

*Remember: "The best way to predict the future is to implement it."*..
> add mybigfile.torrent
Torrent added. Seeding started.
> info 1
Status: Seeding
Peers: 0 (waiting for connections)

# Terminal 2 - Télécharger (autre machine)
$ python main.py --port 6882
> add mybigfile.torrent
Connecting to peers...
Found 1 peer
Download started: 0% [          ]
Download: 2.5 MB/s [=====>    ] 50%
Download: 1.8 MB/s [==========>] 100%
Download complete! Starting to seed...
```

#### Scénario 2 : Réseau de 5 pairs
```bash
# Pair 1 (Seeder initial) - Port 6881
> create movie.mkv
> add movie.torrent
Status: Seeding (100%)

# Pair 2 (Early adopter) - Port 6882
> add movie.torrent
Download: [===>      ] 30%
Peers: 1 (192.168.1.10:6881)

# Pair 3 (Mid-game) - Port 6883
> add movie.torrent
Download: [=>        ] 10%
Peers: 2 (192.168.1.10:6881, 192.168.1.11:6882)

# Pair 4 (Late joiner) - Port 6884
> add movie.torrent
Download: [>         ] 5%
Peers: 3
# Télécharge des chunks de tous les pairs disponibles

# Pair 5 (Concurrent) - Port 6885
> add movie.torrent
Download: [>         ] 3%
Peers: 4
# Bénéficie du meilleur débit grâce à la distribution
```

### API REST (Si activée)

#### Endpoints Disponibles

**Torrents** :
```bash
# Lister tous les torrents
GET /api/v1/torrents
Response:
{
  "torrents": [
    {
      "id": 1,
      "name": "ubuntu-22.04.iso",
      "size": 3758096384,
      "progress": 0.45,
      "status": "downloading",
      "download_rate": 2621440,
      "upload_rate": 524288,
      "peers": 12
    }
  ]
}

# Détails d'un torrent
GET /api/v1/torrents/1

# Ajouter un torrent
POST /api/v1/torrents
Body: {"torrent": "base64_encoded_torrent_file"}
# ou
Body: {"magnet": "magnet:?xt=urn:btih:..."}

# Contrôler un torrent
POST /api/v1/torrents/1/pause
POST /api/v1/torrents/1/resume
DELETE /api/v1/torrents/1?delete_files=true

# Statistiques
GET /api/v1/stats
```

**Exemples avec curl** :
```bash
# Ajouter un torrent
curl -X POST http://localhost:8081/api/v1/torrents \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{"magnet":"magnet:?xt=urn:btih:..."}'

# Voir progression
curl http://localhost:8081/api/v1/torrents/1 \
  -H "Authorization: Bearer your-token"

# Pause
curl -X POST http://localhost:8081/api/v1/torrents/1/pause \
  -H "Authorization: Bearer your-token"
```

## 📊 Structure du Projet Détaillée

```
p2p-file-sharing/
├── src/
│   ├── __init__.py
│   │
│   ├── core/                          # Cœur de l'application
│   │   ├── __init__.py
│   │   ├── torrent.py                 # Classe Torrent principale
│   │   ├── torrent_manager.py         # Orchestration des torrents
│   │   ├── session.py                 # Session globale de l'app
│   │   └── config.py                  # Gestion configuration
│   │
│   ├── peer/                          # Gestion des pairs
│   │   ├── __init__.py
│   │   ├── peer.py                    # Classe Peer individuel
│   │   ├── peer_manager.py            # Pool de pairs
│   │   ├── peer_connection.py         # Connexion TCP avec un pair
│   │   ├── peer_discovery.py          # Découverte de pairs
│   │   ├── choking_manager.py         # Algorithme de choking
│   │   └── reputation.py              # Système de réputation
│   │
│   ├── piece/                         # Gestion des pièces
│   │   ├── __init__.py
│   │   ├── piece_manager.py           # Orchestration des pièces
│   │   ├── piece_picker.py            # Algorithme de sélection
│   │   ├── piece_cache.py             # Cache en mémoire
│   │   └── piece_validator.py         # Vérification intégrité
│   │
│   ├── file/                          # Gestion des fichiers
│   │   ├── __init__.py
│   │   ├── file_manager.py            # I/O fichiers
│   │   ├── file_allocator.py          # Allocation espace disque
│   │   ├── chunker.py                 # Division en chunks
│   │   └── assembler.py               # Reconstruction fichiers
│   │
│   ├── network/                       # Couche réseau
│   │   ├── __init__.py
│   │   ├── protocol/
│   │   │   ├── __init__.py
│   │   │   ├── message.py             # Messages du protocole
│   │   │   ├── handshake.py           # Handshake BitTorrent
│   │   │   ├── encoder.py             # Encodage Bencode
│   │   │   └── decoder.py             # Décodage Bencode
│   │   ├── server.py                  # Serveur TCP
│   │   ├── client.py                  # Client TCP
│   │   ├── udp_tracker.py             # Client tracker UDP
│   │   ├── connection_pool.py         # Pool de connexions
│   │   └── bandwidth_manager.py       # Throttling bande passante
│   │
│   ├── tracker/                       # Tracker centralisé
│   │   ├── __init__.py
│   │   ├── tracker_client.py          # Client pour tracker HTTP
│   │   ├── tracker_server.py          # Serveur tracker (optionnel)
│   │   ├── announce.py                # Logique d'annonce
│   │   └── scrape.py                  # Récupération stats
│   │
│   ├── dht/                           # DHT (Kademlia)
│   │   ├── __init__.py
│   │   ├── node.py                    # Nœud DHT
│   │   ├── routing_table.py           # K-buckets
│   │   ├── kbucket.py                 # Bucket individuel
│   │   ├── protocol.py                # Protocole DHT
│   │   ├── rpc.py                     # Remote procedure calls
│   │   └── storage.py                 # Stockage DHT local
│   │
│   ├── storage/                       # Persistence
│   │   ├── __init__.py
│   │   ├── database.py                # Connexion SQLite
│   │   ├── models.py                  # Modèles de données
│   │   ├── metadata_store.py          # Store métadonnées torrents
│   │   └── resume_data.py             # Sauvegarde état pour reprise
│   │
│   ├── security/                      # Sécurité
│   │   ├── __init__.py
│   │   ├── encryption.py              # Chiffrement connexions
│   │   ├── hash.py                    # Fonctions de hash
│   │   └── ban_manager.py             # Gestion bans
│   │
│   ├── ui/                            # Interfaces utilisateur
│   │   ├── __init__.py
│   │   ├── cli/
│   │   │   ├── __init__.py
│   │   │   ├── shell.py               # Shell interactif
│   │   │   ├── commands.py            # Définition commandes
│   │   │   └── display.py             # Affichage formaté
│   │   ├── web/                       # Interface web (optionnel)
│   │   │   ├── __init__.py
│   │   │   ├── app.py                 # Application Flask/FastAPI
│   │   │   ├── routes.py              # Routes API
│   │   │   ├── websocket.py           # WebSocket pour temps réel
│   │   │   └── static/                # Frontend React
│   │   └── api/
│   │       ├── __init__.py
│   │       └── rest.py                # API REST
│   │
│   └── utils/                         # Utilitaires
│       ├── __init__.py
│       ├── logger.py                  # Logging configuré
│       ├── timer.py                   # Timers et scheduling
│       ├── validator.py               # Validations diverses
│       ├── network_utils.py           # Utilitaires réseau
│       ├── file_utils.py              # Utilitaires fichiers
│       └── metrics.py                 # Métriques et stats
│
├── tests/                             # Suite de tests complète
│   ├── __init__.py
│   ├── unit/                          # Tests unitaires
│   │   ├── test_torrent.py
│   │   ├── test_peer.py
│   │   ├── test_piece_picker.py
│   │   ├── test_protocol.py
│   │   ├── test_dht.py
│   │   └── ...
│   ├── integration/                   # Tests d'intégration
│   │   ├── test_download_flow.py
│   │   ├── test_peer_communication.py
│   │   ├── test_multi_peer.py
│   │   └── test_tracker_interaction.py
│   ├── e2e/                           # Tests end-to-end
│   │   ├── test_full_download.py
│   │   ├── test_network_scenarios.py
│   │   └── test_failover.py
│   ├── performance/                   # Tests de performance
│   │   ├── test_throughput.py
│   │   ├── test_scalability.py
│   │   └── benchmark.py
│   ├── fixtures/                      # Données de test
│   │   ├── sample.torrent
│   │   ├── test_files/
│   │   └── mock_peers.py
│   └── conftest.py                    # Configuration pytest
│
├── scripts/                           # Scripts utilitaires
│   ├── init_db.py                     # Initialisation BDD
│   ├── create_torrent.py              # Créer torrent en CLI
│   ├── network_test.py                # Test connectivité
│   ├── benchmark.py                   # Benchmarks
│   ├── stress_test.py                 # Tests de stress
│   └── migrate.py                     # Migrations BDD
│
├── docs/                              # Documentation
│   ├── architecture.md                # Architecture détaillée
│   ├── protocol.md                    # Spécification protocole
│   ├── api.md                         # Documentation API
│   ├── deployment.md                  # Guide déploiement
│   ├── contributing.md                # Guide contribution
│   ├── troubleshooting.md             # Résolution problèmes
│   ├── diagrams/                      # Diagrammes UML/sequence
│   │   ├── class_diagram.puml
│   │   ├── sequence_download.puml
│   │   └── network_topology.png
│   └── rapport/                       # Rapport académique
│       ├── rapport.tex
│       ├── sections/
│       └── images/
│
├── docker/                            # Configuration Docker
│   ├── Dockerfile                     # Image principale
│   ├── docker-compose.yml             # Orchestration
│   ├── docker-compose.test.yml        # Env de test
│   └── nginx.conf                     # Config reverse proxy
│
├── shared/                            # Fichiers partagés
├── downloads/                         # Téléchargements
│   └── .incomplete/                   # Chunks incomplets
├── data/                              # Données de l'app
│   ├── metadata/                      # Métadonnées torrents
│   └── resume/                        # Données de reprise
├── logs/                              # Fichiers de log
│
├── .github/                           # GitHub Actions
│   └── workflows/
│       ├── tests.yml                  # CI tests
│       ├── lint.yml                   # Linting
│       └── release.yml                # Release automation
│
├── main.py                            # Point d'entrée principal
├── requirements.txt                   # Dépendances production
├── requirements-dev.txt               # Dépendances développement
├── setup.py                           # Installation package
├── pyproject.toml                     # Configuration projet
├── .env.example                       # Variables d'environnement
├── config.example.ini                 # Configuration exemple
├── .gitignore
├── .dockerignore
├── LICENSE
└── README.md
```

## 🧪 Tests et Qualité

### Stratégie de Tests

#### 1. Tests Unitaires (>80% couverture)
```bash
# Exécuter tous les tests unitaires
pytest tests/unit/ -v --cov=src --cov-report=html

# Tests spécifiques
pytest tests/unit/test_piece_picker.py -v
pytest tests/unit/test_protocol.py::test_handshake -v

# Avec marqueurs
pytest -m "not slow" -v
pytest -m "network" -v
```

**Exemples de tests** :
```python
# tests/unit/test_piece_picker.py
import pytest
from src.piece.piece_picker import PiecePicker

def test_rarest_first_selection():
    """Test que l'algorithme rarest-first sélectionne bien la pièce la plus rare"""
    picker = PiecePicker(total_pieces=100)
    
    # Simuler disponibilité des pièces
    picker.update_peer_pieces(peer_id=1, pieces=[0, 1, 2, 5, 10])
    picker.update_peer_pieces(peer_id=2, pieces=[0, 1, 3, 4, 10])
    picker.update_peer_pieces(peer_id=3, pieces=[0, 2, 3, 5, 10])
    
    # Pièce 4 est la plus rare (1 seul pair)
    next_piece = picker.select_next_piece()
    assert next_piece == 4
    
def test_endgame_mode():
    """Test que le mode endgame demande toutes les pièces restantes"""
    picker = PiecePicker(total_pieces=10)
    picker.mark_completed(list(range(8)))  # 8 pièces complètes
    
    assert picker.is_endgame() is True
    requests = picker.get_endgame_requests()
    assert len(requests) == 2  # 2 pièces restantes
```

#### 2. Tests d'Intégration
```bash
# Tests d'intégration avec Docker
docker-compose -f docker-compose.test.yml up --build
pytest tests/integration/ -v

# Test communication entre pairs
pytest tests/integration/test_peer_communication.py -v
```

**Exemple** :
```python
# tests/integration/test_download_flow.py
import pytest
import asyncio
from src.core.torrent_manager import TorrentManager

@pytest.mark.asyncio
async def test_complete_download_flow():
    """Test complet : création torrent, seed, download"""
    
    # Setup: Créer un fichier de test
    test_file = create_test_file(size_mb=10)
    
    # Seeder: Créer et commencer à seeder
    seeder = TorrentManager(port=6881)
    torrent = await seeder.create_torrent(test_file)
    await seeder.start_seeding(torrent)
    
    # Leecher: Télécharger
    leecher = TorrentManager(port=6882)
    await leecher.add_torrent(torrent, seeder_address="localhost:6881")
    
    # Attendre fin du téléchargement (max 30s)
    await asyncio.wait_for(
        leecher.wait_for_completion(torrent),
        timeout=30
    )
    
    # Vérifier intégrité
    assert leecher.verify_torrent(torrent) is True
    
    # Cleanup
    await seeder.shutdown()
    await leecher.shutdown()
```

#### 3. Tests End-to-End
```python
# tests/e2e/test_network_scenarios.py
@pytest.mark.slow
def test_multi_peer_swarm():
    """Test avec 10 pairs téléchargeant simultanément"""
    
    # Setup swarm
    seeder = create_peer(port=6881, role="seeder")
    leechers = [create_peer(port=6882+i) for i in range(10)]
    
    # Démarrer tous les peers
    start_all_peers([seeder] + leechers)
    
    # Attendre que tous téléchargent
    wait_for_all_complete(leechers, timeout=120)
    
    # Vérifier
    for leecher in leechers:
        assert leecher.is_complete()
        assert leecher.ratio > 0  # A uploadé aussi
```

#### 4. Tests de Performance
```bash
# Benchmarks avec locust
locust -f tests/performance/locustfile.py --host=localhost:8081

# Tests de débit
python tests/performance/benchmark.py --peers 50 --file-size 1GB
```

### Métriques de Qualité

**Couverture de Code** :
```bash
# Générer rapport HTML
pytest --cov=src --cov-report=html tests/
# Ouvrir htmlcov/index.html

# Rapport terminal détaillé
pytest --cov=src --cov-report=term-missing tests/
```

**Linting et Formatage** :
```bash
# Black - Formatage automatique
black src/ tests/

# isort - Tri des imports
isort src/ tests/

# flake8 - Vérification style
flake8 src/ tests/ --max-line-length=100

# mypy - Vérification types
mypy src/ --strict

# pylint - Analyse statique
pylint src/ --rcfile=.pylintrc
```

**Pre-commit Hooks** :
```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/psf/black
    rev: 23.10.0
    hooks:
      - id: black
  
  - repo: https://github.com/pycqa/isort
    rev: 5.12.0
    hooks:
      - id: isort
  
  - repo: https://github.com/pycqa/flake8
    rev: 6.1.0
    hooks:
      - id: flake8
```

## 🐳 Déploiement avec Docker

### Docker Compose - Environnement Complet

```yaml
# docker-compose.yml
version: '3.8'

services:
  # Tracker centralisé
  tracker:
    build:
      context: .
      dockerfile: docker/Dockerfile.tracker
    ports:
      - "8000:8000"
    environment:
      - TRACKER_PORT=8000
      - LOG_LEVEL=INFO
    volumes:
      - tracker_data:/data
    networks:
      - p2p_network

  # Seeder initial
  seeder:
    build:
      context: .
      dockerfile: docker/Dockerfile
    ports:
      - "6881:6881"
    environment:
      - PEER_PORT=6881
      - TRACKER_URL=http://tracker:8000/announce
      - SHARED_DIR=/shared
      - ROLE=seeder
    volumes:
      - ./shared:/shared:ro
      - seeder_data:/data
    depends_on:
      - tracker
    networks:
      - p2p_network

  # Peers (leechers)
  peer1:
    build:
      context: .
      dockerfile: docker/Dockerfile
    ports:
      - "6882:6881"
    environment:
      - PEER_PORT=6881
      - TRACKER_URL=http://tracker:8000/announce
      - DOWNLOAD_DIR=/downloads
    volumes:
      - peer1_downloads:/downloads
      - peer1_data:/data
    depends_on:
      - tracker
      - seeder
    networks:
      - p2p_network

  peer2:
    build:
      context: .
      dockerfile: docker/Dockerfile
    ports:
      - "6883:6881"
    environment:
      - PEER_PORT=6881
      - TRACKER_URL=http://tracker:8000/announce
      - DOWNLOAD_DIR=/downloads
    volumes:
      - peer2_downloads:/downloads
      - peer2_data:/data
    depends_on:
      - tracker
      - seeder
    networks:
      - p2p_network

  # Interface Web (optionnel)
  webui:
    build:
      context: .
      dockerfile: docker/Dockerfile.web
    ports:
      - "8080:8080"
    environment:
      - API_URL=http://seeder:8081
    depends_on:
      - seeder
    networks:
      - p2p_network

  # Monitoring avec Prometheus
  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9090:9090"
    volumes:
      - ./docker/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    networks:
      - p2p_network

  # Visualisation avec Grafana
  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - grafana_data:/var/lib/grafana
      - ./docker/grafana/dashboards:/etc/grafana/provisioning/dashboards
    depends_on:
      - prometheus
    networks:
      - p2p_network

volumes:
  tracker_data:
  seeder_data:
  peer1_downloads:
  peer1_data:
  peer2_downloads:
  peer2_data:
  prometheus_data:
  grafana_data:

networks:
  p2p_network:
    driver: bridge
```

### Commandes Docker

```bash
# Construire images
docker-compose build

# Démarrer tout le réseau
docker-compose up -d

# Voir logs
docker-compose logs -f seeder
docker-compose logs -f peer1

# Scaler les peers
docker-compose up -d --scale peer1=5

# Arrêter
docker-compose down

# Nettoyer volumes
docker-compose down -v
```

## 📈 Monitoring et Métriques

### Métriques Exposées (Prometheus)

```python
# src/utils/metrics.py
from prometheus_client import Counter, Gauge, Histogram

# Compteurs
bytes_downloaded = Counter('p2p_bytes_downloaded_total', 'Total bytes downloaded')
bytes_uploaded = Counter('p2p_bytes_uploaded_total', 'Total bytes uploaded')
pieces_completed = Counter('p2p_pieces_completed_total', 'Total pieces completed')

# Jauges
active_torrents = Gauge('p2p_active_torrents', 'Number of active torrents')
connected_peers = Gauge('p2p_connected_peers', 'Number of connected peers')
download_rate = Gauge('p2p_download_rate_bytes', 'Current download rate')
upload_rate = Gauge('p2p_upload_rate_bytes', 'Current upload rate')

# Histogrammes
piece_download_time = Histogram('p2p_piece_download_seconds', 
                                'Time to download a piece')
peer_connection_time = Histogram('p2p_peer_connection_seconds',
                                 'Time to establish peer connection')
```

### Dashboard Grafana

Créer un dashboard avec :
- Graphe du débit de téléchargement/upload en temps réel
- Nombre de pairs connectés par torrent
- Progression des téléchargements
- Distribution des pièces dans le swarm
- Latence réseau moyenne
- Taux d'échec de connexion

## 🔒 Sécurité et Considérations

### Menaces et Mitigations

| Menace | Impact | Mitigation |
|--------|--------|------------|
| Peer malveillant envoyant données corrompues | Haute | Vérification SHA-256 de chaque chunk |
| Attaque DDoS via faux pairs | Haute | Rate limiting, ban automatique |
| Man-in-the-middle | Moyenne | Chiffrement TLS optionnel |
| Fuite d'information via DHT | Basse | Pas d'info sensible dans DHT |
| Épuisement ressources | Haute | Limites strictes connexions/mémoire |

### Bonnes Pratiques de Sécurité

```python
# Validation stricte des entrées
def validate_piece_data(piece_index, data, expected_hash):
    """Ne JAMAIS faire confiance aux données reçues"""
    if len(data) > MAX_PIECE_SIZE:
        raise SecurityError("Piece too large")
    
    actual_hash = hashlib.sha256(data).digest()
    if actual_hash != expected_hash:
        raise IntegrityError("Hash mismatch")
    
    return True

# Bannissement automatique
class BanManager:
    def record_failure(self, peer_ip):
        self.failures[peer_ip] += 1
        if self.failures[peer_ip] > FAILURE_THRESHOLD:
            self.ban(peer_ip, duration=3600)
            logger.warning(f"Banned {peer_ip} for malicious behavior")
```

## 📝 Rapport Académique - Structure Suggérée

### Table des Matières Recommandée

1. **Introduction** (2 pages)
   - Contexte et motivation
   - Objectifs du projet
   - Portée et limitations

2. **État de l'Art** (5 pages)
   - Systèmes P2P existants (BitTorrent, eMule, Gnutella)
   - Protocoles de communication
   - Algorithmes distribués (DHT, consensus)
   - Travaux académiques pertinents

3. **Architecture** (8 pages)
   - Vue d'ensemble du système
   - Diagrammes de composants
   - Diagrammes de séquence
   - Choix technologiques justifiés

4. **Implémentation** (10 pages)
   - Protocole de communication détaillé
   - Algorithmes clés (piece selection, choking)
   - Gestion de la concurrence
   - Optimisations de performance
   - Extraits de code significatifs

5. **Aspects Distribués** (5 pages)
   - Décentralisation et réplication
   - Synchronisation et cohérence
   - Tolérance aux pannes
   - Scalabilité

6.
