# ASM Workshop

<details>
<summary>Prérequis</summary>

1. NASM
2. GCC
3. ARCH X86-64

</details>

## Section

| Section       |	Utilisation                 |	Lecture / Écriture  | Mémoire                   |
| :---:         | ---:                        | ---:                | ---:                      |
| .text	        | Code exécutable	            | Lecture seule	      | Segment de code           |
| .data	        | Données initialisées	      | Lecture-écriture	  | Segment de données        |
| .bss	        | Données non initialisées	  | Lecture-écriture	  | Mémoire réservée          |
| .rodata	      | Données en lecture seule	  | Lecture seule	      | Segment de données        |
| .text.startup	| Code d’initialisation	      | Lecture seule	      | Segment de code           |
| .comment	    | Métadonnées de compilation	| Non utilisée	      | Hors exécution            |
| .eh_frame	    | Débogage et exceptions	    | Lecture seule	      | Informations de stack     |

___

<details>
<summary>TEK 1</summary>
Juste le nom de la section est suffisant (minimum 2)
</details>
<details>
<summary>TEK 2</summary>
Juste le nom de la section est suffisant (minimum 4)
</details>
<details>
<summary>TEK 3</summary>
Produire le code NASM (minimum 4)
</details>

___


### 🛠 Exercice 00 : Quelle section serait la plus approprié ?

Exemple :

```C
char *data;
```

Réponse :
```NASM
section .bss    ; TEK 1-2 seulement
    data resq 1 ; TEK 3 ;)
```

***


```C
const char *data = "Ok cool !";
```

Réponse :
```BASH
Modifiez-moi
```

***
```C
char *data = "Ok cool !";
```

Réponse :
```BASH
Modifiez-moi
```

***
```C
const char data[] = "Ok cool !";
```

Réponse :
```BASH
Modifiez-moi
```

***
```C
char data[] = "Ok cool !";
```

Réponse :
```BASH
Modifiez-moi
```

***
```C
const char data[20] = "Ok cool !";
```

Réponse :
```BASH
Modifiez-moi
```

***

```C
char data[20];
```

Réponse :
```BASH
Modifiez-moi
```

***

###### En bref

| Déclaration C	                      | Type de Donnée	              | Stockage en NASM                    |
| :---                                | ---                           | ---                                 |
| ```const char *data = "Hello";```	  | Chaîne constante	            | .rodata                             |
| ```char *data = "Hello";```	        | Pointeur vers une constante	  | .rodata (chaîne) + .data (pointeur) |
| ```char data[] = "Hello";```	      | Tableau modifiable	          | .data                               |
| ```char *data;``` (non initialisé)	| Pointeur non initialisé	      | .bss                                |

![Progression](https://img.shields.io/badge/Progression-5%25-red)

---

## Cadre de pile (Stack Frame)

Lorsqu'une fonction est appelée, elle dispose d'un cadre (frame) dans la pile (Stack). Cette pile croît vers les adresses basses (selon les architectures cibles ...).

```
Prologue :

push rbp : Sauvegarde l'ancien rbp.

mov rbp, rsp : Définit le nouveau rbp pour cette fonction.

sub rsp, X : Alloue de l'espace pour les variables locales.
```

```
Corps de la fonction :

Les variables locales sont accessibles via [rbp - offset].

Les arguments sont accessibles via [rbp + offset].
```
```
Épilogue :

leave : Restaure rsp et rbp (mov rsp, rbp puis pop rbp).

ret : Retourne à l'appelant.
```

En bref,

```function = [Prologue + Corps de la fonction + Épilogue]```
ou
```function = [Corps de la fonction]```

---

## Registre

Une convention mnémotechnique utile lors de la programmation en assembleur et de la consultation de la documentation, Intel définit un ensemble de préfixes (R = 64 bits, E = 32 bits, aucun préfixe = 16 bits) et de suffixes (X/D = DWORD, W = WORD, L/B = Octet faible, H = Octet élevé) lorsqu'il fait référence à ces registres, ce qui décrit à la fois (a) la largeur de l'opérande, et (b) où ces bits sont situés dans le registre complet.

```
                            | Si l'octet le plus significatif en premier (little-endian)
                A register [0100011101001111010011110100010001001010010011110100001000100001]
                    offset  0       8       16             32                             64
          (Low 8-bits)  AL  |<----->|       |              |                               |
         (High 8-bits)  AH          |<----->|              |                               |
         (Low 16-bits)  AX  |<------------->|              |                               |
         (Low 32-bits) EAX  |<---------------------------->|                               |
(Full 64-bit register) RAX  |<------------------------------------------------------------>|
```
---

## Types de données :

Variations courantes que vous pouvez rencontrer :

| Type    | Bits | Octets | Alias                                                   |
| :---    |:---:  |:---:  |---                                                      |
| n/a     | 4    | ½      | nibble, semioctet (rarement mentionné)                  |
| `BYTE`  | 8    | 1      | byte, octet, char                                       |
| `WORD`  | 16   | 2      | word, short                                             |
| `DWORD` | 32   | 4      | long, doubleword, longword, int, int32                  |
| `QWORD` | 64   | 8      | longword, long long, quadword, int64                    |
| n/a     | 128  | 16     | octaword, double quadword (utilisé en calcul intensif)  |

| Type scalaire |	Type de données             | C	Alignement requis   |
| :---:         | ---:                        | ---:                  |
| INT8          | char                        | BYTE                  |
| UINT8         | unsigned char               | BYTE                  |
| INT16         | short                       | WORD                  |
| UINT16        | unsigned short              | WORD                  |
| INT32         | int, long                   | Double WORD           |
| UINT32        | unsigned int, unsigned long | Double WORD           |
| INT64         | __int64                     | Quadruple WORD        |
| UINT64        | unsigned __int64            | Quadruple WORD        |
| FP32          | flaot                       | Double WORD           |
| FP64          | double                      | Quadruple WORD        |
| POINTER       | *                           | Quadruple WORD        |
| __m64         | struct __m64                | Quadruple WORD        |
| __m128        | struct __m128               | Octuple WORD          |

**À SAVOIR** : Le type `WORD` fait référence à la plus grande valeur entière que le processeur peut traiter en une seule instruction. Cela remonte à l'époque des processeurs Intel 8086 qui étaient `16`-bits. Bien que les capacités des processeurs aient évolué, les manuels Intel (et par conséquent presque toute la documentation) continuent à utiliser cette terminologie comme indiqué dans le tableau ci-dessus. Cependant, certains documents spécialisés peuvent appliquer la définition originale à du matériel très ancien ou très récent. Il est donc conseillé de toujours se référer au manuel du fabricant pour s’assurer de bien comprendre l’architecture utilisée.

---

## Structure des instructions x86 :

La longueur d'une instruction ne doit pas dépasser `15` octets, sinon le processeur déclenchera une exception.

## Structure d'une instruction unique :

| 0-4 octets   | 1-3 octets   | 0-1 octet      | 0-1 octet                  | 0,1,2,4 octets      | 0,1,2,4,8 octets |
|-------------|-------------|---------------|---------------------------|--------------------|------------------|
| `Préfixe`   | `Opcode`    | `Mod-Reg R/M` | `Scale-Index-Base (SIB)`  | `Déplacement`     | `Immédiat`       |

___

<details>
<summary>TEK 1</summary>
Appelez-moi pour la compilation C-ASM
</details>

<details>
<summary>TEK 2 - 3</summary>
Débrouillez-vous :)
</details>

<details>
<summary>Indice</summary>
La valeur de retour est stocké dans le registre RAX.
</details>

<details>
<summary>Indice</summary>
L'utilité d'une Stack Frame ?
</details>

Les fichiers `*.asm` et `*.c` correspondant à ces exercices doivent être déposés dans le dossier `Ex01-2`

___

### 🛠 Exercice 01.00 : Créer une fonction (C/ASM) avec Stack Frame (exo0100.asm && ex01.c)

Créez une fonction `foo(void)` qui retourne `42`.


```bash
Modifiez-moi (C code)
```
```bash
Modifiez-moi (NASM code)
```

### 🛠 Exercice 01.01 : Créer une fonction (C/ASM) sans Stack Frame (exo0101.asm && ex01.c)

Créez une fonction `foo(void)` qui retourne `42`.

```bash
Modifiez-moi (C code)
```
```bash
Modifiez-moi (NASM code)
```


### 🛠 Exercice 02.00 : Créer une fonction (C/ASM) avec Stack Frame (exo0200.asm && ex02.c)


```bash
Modifiez-moi (C code)
```
```bash
Modifiez-moi (NASM code)
```


### 🛠 Exercice 02.01 : Créer une fonction (C/ASM) sans Stack Frame (exo0201.asm && ex02.c)


```bash
Modifiez-moi (C code)
```
```bash
Modifiez-moi (NASM code)
```

![Progression](https://img.shields.io/badge/Progression-15%25-red)

___

<details>
<summary>TEK 1</summary>
L'exercice `03.00` suffira
</details>

Les fichiers `*.asm` et `*.c` correspondant à ces exercices doivent être déposés dans le dossier `Ex03`
___


### 🛠 Exercice 03.00 (exo0300.asm && ex0300.c)
Créez une fonction en assembleur `add(a, b)` qui retourne `a + b`.
Cette fonction sera appelé à partir de code C.

Exemple :
```BASH
> ./03.00 3 4
7
> echo $?
7
```

### 🛠 Exercice 03.01 (exo0301.asm && ex0301.c)
Créez une fonction en C `sub(a, b)` qui retourne `a - b`.
Cette fonction sera appelé à partir de code assembleur.
Vérifiez votre solution en [exécutant les tests ici](https://github.com/your-repo/actions).

### 🛠 Exercice 03.02 (exo0302.asm && ex0302.c)
Créez une fonction `infinite_loops(void)` qui retourne rien.
Cette fonction doit boucle inf.

### 🛠 Exercice 03.03 (exo0303.asm && ex0303.c)
Créez une fonction `loop(unsigned int iter)` qui retourne rien.
Cette fonction doit afficher `"ok"` en boucle, en répétant l'opération plusieurs fois en fonction de la valeur d' `iter`.
Vérifiez votre solution en [exécutant les tests ici](https://github.com/your-repo/actions).

![Progression](https://img.shields.io/badge/Progression-30%25-yellow)

___


Les fichiers `*.asm` et `*.c` correspondant à ces exercices doivent être déposés dans le dossier `Ex04`

___



### 🛠 Exercice 4 : Manipulation de registre (ex04.asm)

Lorsqu'on fait référence à un registre dans MODRM.reg ou MODRM.rm, on s'attend à ce que ce registre contienne directement la valeur nécessaire pour l'opération.

Exemple :
```NASM
MOV EAX, ECX  ; EAX prend la valeur stockée dans ECX
```
Cependant, dans le troisième cas mentionné ci-dessus, on peut également utiliser un registre dans SIB.index et SIB.base. Cela signifie que le registre contient une adresse mémoire, que le processeur va déréférencer pour récupérer la valeur située à cette adresse.

Exemple :
```NASM
MOV EAX, [ECX]  ; EAX prend la valeur stockée à l'adresse pointée par ECX
```
Ici, ECX ne contient plus directement la valeur, mais l'adresse d'une donnée en mémoire.

La concrétisation des éléments mentionnés précédemment est illustrée dans le code suivant :

```C
#include <stdio.h>

int main(void)
{
    int eax = 100;
    int valeur = 42;
    int *ptr = &valeur;

    printf("EAX = %d, Valeur mémoire = %d\n", eax, *ptr);

    return 0;
}
```
Nous souhaiterions obtenir l'équivalent en assembleur ;)

___

![Progression](https://img.shields.io/badge/Progression-50%25-yellow)

### 🛠 Exercice 5 : Simple copie (ex05.c)

Complète le code suivant :

```C
#include <stdio.h>
#include <string.h>

int main(void)
{
    char dest[20] = {0};
    const char *src = "Hello, World!";

    asm volatile(
        "cld\n\t"
        "rep movsb"
        :
        : "S" (à compléter), "D" (à compléter), "c" (à compléter)
    );

    printf("Copied String: %s\n", dest);
    return 0;
}
```
![Progression](https://img.shields.io/badge/Progression-60%25-green)

___

### 🛠 Exercice 6 : Appel de l'assembleur depuis du code C (ex06.c)

Faire l'équivalent de deux appels système.

![Progression](https://img.shields.io/badge/Progression-61%25-green)

___

Je repose ça là.

| Nom   |	Taille    |	Registres | courants    |
| :---: | :---:     | :---:     | ---         |
| Byte  |	8 bits    |	al, ah, bl, bh          |
| Word  |	16 bits   |	ax, bx, cx, dx          |
| Dword |	32 bits   |	eax, ebx, ecx, edx      |
| Qword |	64 bits   |	rax, rbx, rcx, rdx      |
| Oword |	128 bits  |	xmm0-xmm15 | (SSE)      |
| Yword |	256 bits  |	ymm0-ymm15 | (AVX)      |
| Zword |	512 bits  |	zmm0-zmm31 | (AVX-512)  |


## Intel Streaming SIMD Extensions (SSE)

En 1999, Intel a annoncé SSE comme successeur de Intel MMX, ajoutant
[+70 nouvelles instructions](https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions#SSE_instructions) et [+8 nouveaux registres 128-bit](https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions#Registers) `XMM0-7`.. plus tard, lorsque amd64 a introduit 8 autres registres `XMM8-15`, Intel a suivi la tendance.

Cela a permis de résoudre deux problèmes majeurs : a) MMX ne fonctionnait qu'avec des entiers, et b) le passage entre les instructions MMX/FPU était trop inefficace pour un usage pratique, car elles devaient partager les mêmes registres FPU.

Il y a eu plusieurs versions de SSE à ce jour, y compris
[`SSE`](https://en.wikipedia.org/wiki/X86_instruction_listings#SSE_instructions),
[`SSE2`](https://en.wikipedia.org/wiki/X86_instruction_listings#SSE2_instructions),
[`SSE3`](https://en.wikipedia.org/wiki/X86_instruction_listings#SSE3_instructions),
[`SSSE3`](https://en.wikipedia.org/wiki/X86_instruction_listings#SSSE3_instructions),
[`SSE4a`](https://en.wikipedia.org/wiki/X86_instruction_listings#SSE4a),
[`SSE4.1`](https://en.wikipedia.org/wiki/X86_instruction_listings#SSE4.1),
et la dernière version en date de cette écriture
[`SSE4.2`](https://en.wikipedia.org/wiki/X86_instruction_listings#SSE4.2).
La dernière version est compatible avec la première.

Il existe également le rejeton avorté d'AMD [`SSE5`](https://en.wikipedia.org/wiki/X86_instruction_listings#SSE5_derived_instructions) ou `XOP`, qui n'a existé brièvement que dans un processeur avant d'être abandonné après avoir été rejeté par Intel.

Bien que les noms, les implémentations et leurs ensembles d'instructions exacts soient différents, le concept reste le même : --**SIMD** ; que vous fassiez de l'encodage vidéo, de la synthèse audio ou du streaming de textures vers un GPU, vous optimisez en effectuant une seule opération sur une belle matrice/vecteur de données flottantes chaque fois que cela est possible.

## Extensions Vectorielles Avancées ([AVX](https://en.wikipedia.org/wiki/X86_instruction_listings#AVX) / [AVX2](https://en.wikipedia.org/wiki/X86_instruction_listings#AVX2) / [AVX512](https://en.wikipedia.org/wiki/X86_instruction_listings#AVX-512))

Aujourd'hui, certains processeurs conçus pour des charges de travail lourdes offrent des jeux d'instructions SIMD qui fonctionnent sur des registres encore plus grands :

- **AVX:** Seize nouveaux registres 256 bits (`YMM0-15`), les registres `XMM` occupant les 128 bits inférieurs du même registre `YMM` numéroté.
- **AVX-512:** Trente-deux nouveaux registres 512 bits (`ZMM0-31`), les registres `YMM` et `XMM` occupant respectivement les 256 et 128 bits inférieurs du registre `ZMM`.


### 🛠 Exercice 7 : Manipulation de registre avancées : (strlen_sse.c)

Reproduire la fonction strlen avec des instructions sse.

![Progression](https://img.shields.io/badge/Progression-80%25-green)

### 🛠 Exercice 8 : Manipulation de registre avancées (substring_sse.c)

Faire une fonction qui renvoie l'index d'une substring trouvé avec des instructions sse.

![Progression](https://img.shields.io/badge/Progression-100%25-green)

Bv !
