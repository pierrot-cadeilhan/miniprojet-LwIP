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
![image](https://github.com/user-attachments/assets/27eefce4-7765-460d-b8ed-3cd8080ae2ff)

## Etape II: Serveur HTTP pour contrôler des LEDs
## Etape III: Jeu avec version client et version serveeur
