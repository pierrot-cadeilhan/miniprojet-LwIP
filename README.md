# miniprojet-LwIP
## Etape I: Serveur TCP pour contrôler des LEDs
Pour cette étape, il aura fallu
- Modifier la fonction ``` _write()``` du fichier Core/Src/syscalls.c pour rediriger les ```printf()``` vers le port série 
- Configurer la librairie lwIP et l'Ethernet
- Résoudre le problème d'adressage du tas de lwIP (cf. difficultés)
- Configurer Tera Term pour le TCP/IP et le port Série
- Implémenter un serveur TCP dans une tâche

### Modification de ``` _write()```
Pour ce faire, on remplacera la fonction par celle-ci:
```c
__attribute__((weak)) int _write(int file, char *ptr, int len)
{
	extern UART_HandleTypeDef huart1;
	if (file == 1) HAL_UART_Transmit(&huart1, (unsigned char *) ptr, len, 1000);
	return len;
}
```
Il faudra bien entendu configurer dans la section *Connectivity* de CubeMX l'USART1 de façon à pouvoir utiliser le port série.
Dans mon cas, la configuration est la suivante.
- Baud rate: 115200 Bauds
- Word length: 8 bits
- Parity: None
- Stop bits: 1 bit

![image](https://github.com/user-attachments/assets/27eefce4-7765-460d-b8ed-3cd8080ae2ff)
### Configuration du périphérique Ethernet
Il faut tout d'abord sélectionner dans la section *connectivity* de CubeMX le mode d'Ethernet RMII, et activer dans les paramètres le mode interruption sur réception.

![image](https://github.com/user-attachments/assets/9f167a56-9b30-4f60-92c6-8559bfe0cc78)

### Configuration de lwIP
Il faut ensuite activer dans la section *Middleware and Software* de CubeMX le middleware lwIP, puis sélectionner dans *plateform setting* la plateforme Ethernet.

![image](https://github.com/user-attachments/assets/3c180a3f-fd4c-40bc-ae5a-40806b16ccfd)

Ensuite, on prendra soin d'activer le paramètre LWIP_DHCP dans *General Settings*. Ce paramètre permet l'attribution automatique d'une adresse IP au périphérique Ethernet.

Enfin, on mettra la valeur 10*1024 à la taille MEM_SIZE du tas de lwIP. Cela correspond à une taille de 10KBytes. 

/!\ Comme vu dans les difficultés, et mentionné plus bas, il y a un problème sur l'adressage du tas. La solution, détaillée dans le fichier markdown, nécessite de changer l'adressage du tas à 0x20048000.
![image](https://github.com/user-attachments/assets/7f2f8a70-3674-4540-90c7-1392d10d30fa)

### Résoudre le problème d'adressage
Il y a un problème d'adressage dont la résolution a été décrite dans le fichier difficultes.md

### Configuration de Tera Term
On commence par la configuration du terminal série.
Pour ce faire, on choisit la connexion série, puis on règle dans *Configuration*->*Port Série* la configuration choisie pour le périphérique USART1. Ensuite, on chosit LF à l'émission et la réception et on active *echo local* dans *Configuration*->*Terminal*.

![image](https://github.com/user-attachments/assets/8bf3f0a1-9cc5-4330-8abc-722ef6445806)

Ensuite, on configure le terminal de la liaison TCP/IP.
**Une fois le serveur lancé** (sinon le terminal ne se connectera pas) on choisit le mode telnet, puis on rentre l'adresse du serveur et son port, et on valide la sélection (le port sera défini plus bas dans le code).

![image](https://github.com/user-attachments/assets/2994a6ae-7aba-4f02-955d-a976f1a3823b)

### Implémentation du serveur TCP dans une tâche
On introduit une tâche TCPServer qui se charge de l'écoute sur un port et de l'interprétation des messages TCP reçus. Pour plus de détails, voir le code du projet.

## Etape II: Serveur HTTP pour contrôler des LEDs
## Etape III: Jeu avec version client et version serveeur
