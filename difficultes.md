# L'initialisation MX_LWIP_Init(); mène à une Hard Fault
/!\ Si la recompilation du fichier .ioc entraine la remodification LWIP_RAM_HEAP_POINTER dans lwipopts.h, pensez à changer la racine du tas dans CubeMX /!\
## La Hard Fault provient de la fonction mem_init() du fichier mem.c de lwIP
  
**Tout d'abord, que fait cette fonction ?**

Elle permet d'initialiser le tas du middleware lwIP, qui est implémenté comme une liste chaînée d'objets mem.

**Quelle partie pose problème ?**

Il vient de la deuxième instruction de ce bloc.
```c
ram_end = ptr_to_mem(MEM_SIZE_ALIGNED);
ram_end->used = 1;
ram_end->next = MEM_SIZE_ALIGNED;
ram_end->prev = MEM_SIZE_ALIGNED;
MEM_SANITY();
```
## Voyons en détail la source de l'erreur

**Que sont les variables considérées ?**

La fonction ptr_to_mem à pour rôle de transformer une adresse mémoire relative en un pointeur vers un objet mem à cet endroit.

```c
ptr_to_mem(mem_size_t ptr)
{
	return (struct mem *)(void *)&ram[ptr];
}
```

ram_end est donc le pointeur vers l'objet mem situé en fin de tas.

**Qu'est ce qui cause l'erreur ?**

Le problème est que l'adresse de ram_end LWIP_RAM_HEAP_POINTER + MEM_SIZE_ALIGNED est hors de la zone de RAM !!

```<error: Cannot access memory at address 0x30040640>```

En effet, dans le linker script, on a la définition des mémoires suivantes:

```
MEMORY
{
  RAM    (xrw)    : ORIGIN = 0x20000000,   LENGTH = 320K
  FLASH    (rx)    : ORIGIN = 0x8000000,   LENGTH = 1024K
}
```

La RAM s'arrête donc en 0x2004FFFF, ce qui est bien inferieur à LWIP_RAM_HEAP_POINTER qui vaut 0x30040640.

## Solution mise en oeuvre
On va construire un espace mémoire dédié dans la RAM en la réduisant un peu. Pour ce faire, on modifie le linker script ainsi:
```
MEMORY
{
  RAM	(xrw)		: ORIGIN = 0x20000000,	LENGTH = 288K
  LWIP_RAM	(xrw)	: ORIGIN = 0x20048000,	LENGTH = 32K
  FLASH	(rx)		: ORIGIN = 0x8000000,	LENGTH = 1024K
}
```

# On ne peut pas uploader sur GitHub des fichiers de plus de 25MB ou plus de 100 fichiers d'un coup...
COMMENT UPLOADER LES PROJETS ?? :'(
