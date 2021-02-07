## Un effet de bord ? (WORK IN PROGRESS, Ver. 0.1)

Quand on est amené, en tant que développeur, à s'essayer à la programmation fonctionnelle pure, l'une des premières notions qu'on voit apparaître partout est "l'effet de bord".

Cette notion est tellement sous-jacente aux paradigmes les plus classique qu'un étudiant en informatique va étudier (impératif et objet), qu'on ne met même pas un nom dessus, donc on ne se demande jamais ce que c'est.

Plus que ça, je pense que la première chose que j'ai apprise en programmation, c'étais un effet de bord : **l'affectation**. 

```c
int a;
a = 6;
```

Pourtant, jamais on a mis un nom dessus, j'ai seulement appris ce que c'étais qu'une affectation et ce que ça faisait : modifier la variable **a** en lui injectant la valeur 6.


Et maintenant, on nous dit qu'en fait l'affectation ça fait partie des trucs qu'ont appel *effet de bord*, on va même lire qu'un effet de bord ce n'est pas toujours une bonne chose, et comme ça d'un coup, on remet en question tout le style de programmation qu'on a appris et pratiqué plusieurs années ? Voyons ça de plus près.

#### Définition d'un effet de bord (ver. 0.1)

**Commençons d'abord** par regarder comment les anglophones appellent ça : *side effect*.


Ok, tout de suite, c'est plus clair, on parle **d'effet secondaire**, (on peut même traduire side effect par effet **indésirable**). Vous êtes d'accord pour dire qu'en général, on n'aime pas trop les effets secondaires.


Exemple d'actualité : les vaccins


Un effet secondaire, c'est un effet qui vient se rajouter a l'effet principal que l'on voulais opérer, c'est quelque chose qui fait plus que ce qu'on voulait faire à la base.


Et c'est là qu'on retrouve toute l'essence des effets de bord en informatique. Passons à un cran plus technique.

### **Les fonctions pure et impure**

Le parallèle entre les fonctions mathématiques et les fonctions que les informaticiens utilisent en programmation est, je trouve, parfait pour exhiber et parler d'effets de bord.

On peut penser que les fonctions du programmeur sont comme les fonctions du mathématicien, pourtant il y a deux différences fondamentales entre les deux : la **transparence referentielle** et là **pureté**


#### **La transparence referentielle**

La transparence référentielle est une propriété qui dit qu'une expression peut être remplacée par sa valeur (l'expression qui a été réduite au maximum) sans que le comportement du programme ne change.


On peut se convaincre assez facilement que toutes les expressions mathématiques ont cette propriété, si on remplace **3*2** par **5** ou la fonction **sinus(x)** qui, pour tout x, renvoie toujours la même valeur en sortie pour un même x donné, on peut à chaque fois remplacer sin(x) par sa valeur correspondante.

```python
def f1(a):
    return a + (3*5)

def f2(b):
    return b + (3*5)
```

Ici, si on peut remplacer 3*5 par 10, peu importe le nombre de fois qu'on appel f1 ou f2 avec le même argument, on aura la même valeur en sortie, et c'est ça le cœur de la programmation fonctionnelle pur, c'est aussi la notion centrale à comprendre dans cet article.


Par contre, si on a le code suivant, qui additionne l'argument à une fonction qui incrémente une variable globale et renvoie sa nouvelle valeur : 

```python
def f1(a):
    return a + incr()

def f2(b):
    return b + incr()
```

On ne peut pas remplacer **incr()** par sa valeur, parce que cette fonction retourne une valeur différente à chaque appel, si on appel f1 et f2, les valeurs en sortie de incr() ne seraient pas les mêmes, et même si ont appel 50 fois f1 ou f2, la valeur seras différente a chaque fois.


Donc, maintenant, on sait qu'un effet de bord rend nécessairement une fonction impure.


Les fonctions en mathématique sont **pure**, elles respectent la transparence referentielle *(parce qu'elles n'ont pas d'effet de bord)*, peu importe le nombre de fois que vous allez appliquer une fonction mathématique a un même argument, le résultat renvoyé seras le même.

### Les effets et les valeurs

Plus généralement, une fonction pure produit toujours une **valeur**, une expression, en opposition a une fonction impure qui ne produit pas necessairement de valeur, mais produit un ou des **effets**.

Dans une fonction mathematique, non seulement on ne produit pas d'effet, mais en plus tout les calcul sont **local**, aucune variable globale définis 50 lignes plus tot va etre modifiée. Le résultat ne dépend pas, et n'intéragis pas avec le **monde exterieur**, mais seulement des calcul local écrit dans la fonction.

Plus fort encore, en lisant la definition d'une fonction pure, ont peut deviner de tete le résultat qui va etre renvoyé, et ce peu importe le nombre de fois qu'on appel cette fonction.


**Exemple** : la fonction carré **f (x) = x²**

Si je veux appliquer cette fonction à l'argument *2*, je peux deviner de tête le résultat renvoyé : 4, et j'insiste, peu importe le nombre de fois que vous allez appliquer cette fonction carré a 2, le résultat renvoyé seras toujours 4.
**Cette fonction n'as pas d'effet de bord, elle est prévisible, et pure.**


Maintenant prenons cette fonction définie par un programmeur.

```python
import random as r

def plus_r(int arg):
    return arg + r.randint(0,9999)
```

Appelons cette fonction avec l'argument *2*, êtes vous capable de deviner la valeur exacte qui va être renvoyer lors de l'appel ? 
Non, parce que je tire aléatoirement une valeur entre 0 et 9999 pour l'additionner à mon argument, ça pourrais être n'importe quelle valeur entre 2 et 10001, et si j'appel 500 fois cette fonction avec le même argument, je peux avoir 500 valeurs différentes en sortie.

Je pense qu'intuitivement vous comprenez en quoi, c'est un problème ? Ça rend le comportement du code imprévisible (9999 valeurs possibles en sortie au lieu d'une), plus difficilement debbugable, et plus difficilement réutilisable.

#### Le monde exterieur

Plus haut, je parle du **monde exterieur**, et c'est une intuition intéressante à avoir, certains effets de bord peuvent être vus comme des interactions avec le monde extérieur, qui est imprévisible.


Et quand je parle de monde extérieur, c'est relatif à la porter de l'endroit où je suis en train de faire mon calcul. Pour être plus clair, dans une fonction donnée, tout ce qui est local, c'est ce qui est dans la portée de cette fonction.
Le monde extérieur est tout ce qui existe en dehors de la portée de cette fonction, tout ce qui est *global*.

Des exemples : 

```c
int add_v (int v) {
   int side_effect = 0;

   printf("Entrez votre age: ");
   scanf("%d", &side_effect);
   
   return v + side_effect;
}
```

Cette fonction demande à l'utilisateur son age pour l'additionner a l'argument qu'on lui donne : 

- Cette fonction interagis avec **le monde exterieur** : la fonction scanf qui demande une donnée a l'utilisateur
- Le résultat de cette fonction dépend du **monde exterieur** : la valeur que l'utilisateur va rentrer va être additionnée à l'argument.

**Cette fonction opère donc un effet de bord, et est imprévisible et impure.**


Avec toute ces nouvelles informations, essayons de nous faire notre propre definition d'un effet de bord.

#### Définition d'un effet de bord (ver. 0.2)


Un effet de bord, c'est du code qui produit un effet, pas une valeur.
Effet qui peut dépendre du monde extérieur et interagir avec le monde extérieur : affectation, variables globale, affichage sur la sortie standard, l'aléatoire...

Vous avez peut être remarqué que finalement, dans des langages comme C ou python, ou même java, il y a un moyen assez évident de reconnaître les fonctions a effets de bord. On a plusieurs fois mis en opposition le fait de ne pas produire de valeur, mais de produire un effet.

Hors, une fonction C avec le type de retour void, est une fonction qui ne retourne aucune valeur. Ces fonctions existent uniquement pour produire des effets : affichages, modification de variables globales, etc...

En python c'est pareil si on n'utilise pas de return, en java, c'est pareil les fonctions et méthodes de type void..

**Comment savoir si ma fonction est pure ?** *(sans effet de bord)*

*Ok, tout ça c'est peut etre un peu compliqué et brouillon, moi j'ai envie de savoir concrètement quand est ce que ma fonction est pure ou impure ?*

Très bien.
Pour savoir si votre fonction est pure, elle doit respecter deux regles fondamentale : 
- Votre fonction doit retourner exactement les memes valeurs pour les memes arguments
- L'execution de votre fonction ne contient pas d'effet : print, modification de variable local ou global etc.

Une fonction pure doit uniquement calculer et produire une valeur.

#### Interet des fonctions pure

 Pour finir par du concret, j'aimerais vous parler d'une conséquence directe de l'absence d'effet de bord dans une fonction (pure).

Imaginez qu'on a, dans une application, une fonction pure **compute** qui attend un argument et qui calcule un truc très compliqué et long, nous, on sait que peu importe le nombre de fois que l'on va appeler la fonction compute avec les mêmes arguments, les résultats en sortie seront les mêmes, on pourrait peut être exploiter cette propriété pour optimiser le temps que prend compute, a s'exécuter non ?

Ont appel ça la **mémoization**, ici on voudrait memoizer notre fonction, c'est a dire que si j'appel compute pour un argument *x* donné, une fois que j'ai le résultat, je le stock. Et si a un moment, j'ai besoin de rappeler ma fonction compute avec ce même argument, je peux aller chercher la valeur que j'ai stocké précédemment, au lieu de relancer le calcul long et complexe, car je sais déjà que la valeur de sortie sera la même.

C'est un énorme avantage, car on peut avoir le résultat du calcul en temps constant, éviter de perdre des ressources inutilement, et on peut faire ça pour tous les arguments possible pour la fonction compute.