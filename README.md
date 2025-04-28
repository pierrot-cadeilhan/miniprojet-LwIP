# miniprojet-LwIP
## Definition des objectifs
- Affichage de LEDs depuis une page WEB
- Messagerie
## Structure du réseau
- Un routeur connecté à la carte par ethernet et à un ordianateur
## Spécificité
- Utilisation du port série pour le print (modification de _write() dans syscall.c)


## Difficultés:
L'initialisation MX_LWIP_Init(); mène à une Hard Fault
It was due to a fonction called ptr_to_mem that was ran with a ptr value greater than MEM_SIZE. Cela est du à l'absence de la déclaration compilateur de MEM_SIZE dans lwipopts.h qui est alors défini par défaut à 1600 dans opts.h
