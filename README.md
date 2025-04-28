# miniprojet-LwIP
## Definition des objectifs
- Affichage de LEDs depuis une page WEB
- Messagerie
## Structure du réseau
- Un routeur connecté à la carte par ethernet et à un ordinateur
## Spécificité
- Utilisation du port série pour le print (modification de _write() dans syscall.c)

# Configurations
## Ethernet
On configure le périphérique ethernet
## lwIP
On désactive le DHCP (qui adapte l'adresse IP)
- On utilise 192.168.1.100
- Avec le netmask 255.255.255.0
- Et le routeur a pour addresse 192.168.1.1
On active le support de FreeRTOS
On active le HTTPD (serveur HTTP)

# Difficultés
Les difficultés que j'ai rencontrées durant l'implémentation m'ont pris du temps à débogguer, j'ai donc décidé de tenir un fichier markdown expliquant le processus de debogage et la solution de chaque problème.
