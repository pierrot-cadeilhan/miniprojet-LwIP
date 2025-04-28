# miniprojet-LwIP
## Definition des objectifs
- Affichage de LEDs depuis une page WEB
- Messagerie
## Structure du réseau
- Un routeur connecté à la carte par ethernet et à un ordinateur
## Spécificité
- Utilisation du port série pour le print (modification de _write() dans syscall.c)


## Difficultés:
### L'initialisation MX_LWIP_Init(); mène à une Hard Fault
- La Hard Fault provient de la fonction mem_init() du fichier mem.c de lwIP.
  
*Tout d'abord, que fait cette fonction ?*

Elle permet d'initialiser le tas du middleware lwIP, qui est implémenté comme une liste chaînée d'objets mem.

*Quelle partie pose problème ?*

C'est l'appel de la fonction ptr_to_mem pour définir la fin du tas, de taille MEM_SIZE.
```c ram_end = ptr_to_mem(MEM_SIZE_ALIGNED);```

- Observons en détail ptr_to_mem.

*Que fait cette fonction ?*

Pour manipuler le tas, on utilise des adresses relatives à la racine du tas, qui doivent alors être converties en pointeurs. On a pour ce faire les fonctions ptr_to_mem et mem_to_ptr.
Le code de la fonction est reporté ci-dessous.
```c
static struct mem *ptr_to_mem(mem_size_t ptr)
{
	return (struct mem *)(void *)&ram[ptr];
}
```

*D'où vient le problème ?*

Le problème est qu'on accède à un endroit de la RAM dont l'accès n'est pas permis.

```<error: Cannot access memory at address 0x30040000>```

En effet, dans le linker file, on a la définition des mémoires suivantes:
```
MEMORY
{
  RAM    (xrw)    : ORIGIN = 0x20000000,   LENGTH = 320K
  FLASH    (rx)    : ORIGIN = 0x8000000,   LENGTH = 1024K
}
```
La RAM s'arrête donc en 0x2004FFFF, ce qui est bien inferieur à LWIP_RAM_HEAP_POINTER qui vaut 0x30040000.
