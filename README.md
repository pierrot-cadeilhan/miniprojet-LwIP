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

## Etape II: Serveur HTTP pour contrôler des LEDs
## Etape III: Jeu avec version client et version serveeur
