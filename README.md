Analyse de l’application UnCrackable Level 2

. Introduction

Dans ce TP, nous avons analysé l’application Android UnCrackable Level 2 issue du projet OWASP MSTG (Mobile Security Testing Guide).
L’objectif est d’identifier les mécanismes de protection implémentés dans l’application et de comprendre comment la vérification du secret est réalisée.

2. Analyse statique de l’APK

Pour analyser l’application, nous avons utilisé l’outil :JADX
Cet outil permet de décompiler le fichier APK afin d’obtenir le code Java.
Les principaux fichiers analysés sont :
MainActivity
CodeCheck
sg.vantagepoint.a.a
sg.vantagepoint.a.b

- Analyse de la détection du mode Debug

Dans l’application, la classe suivante est utilisée pour détecter si l’application est exécutée en mode debug.

<img width="1350" height="411" alt="image" src="https://github.com/user-attachments/assets/ec573770-335e-409f-95aa-5c4f689c0d73" />

L’application implémente une protection contre le débogage. Elle vérifie si le flag FLAG_DEBUGGABLE est actif dans les informations de l’application. Si ce flag est détecté, l’application considère que l’environnement est suspect et se ferme immédiatement.

Analyse de la détection du Root

L’application implémente plusieurs mécanismes pour détecter si le téléphone Android est rooté.
Ces vérifications sont implémentées dans la classe :

<img width="1991" height="883" alt="image" src="https://github.com/user-attachments/assets/01e85779-1779-404f-8670-8e3ebf7ca7ed" />

L’application implémente plusieurs techniques de détection du root afin d’empêcher son analyse dans un environnement compromis. Elle vérifie la présence du binaire su, la présence du tag test-keys dans le système Android ainsi que l’existence de fichiers associés aux outils de root tels que SuperSU ou Superuser. Si l’une de ces conditions est détectée, l’application affiche un message d’alerte et se ferme automatiquement.

Analyse de la classe CodeCheck

La classe suivante est utilisée pour vérifier si la chaîne entrée par l’utilisateur correspond au secret attendu par l’application.
<img width="783" height="285" alt="Capture d&#39;écran 2026-03-11 194704" src="https://github.com/user-attachments/assets/186b8213-4c9e-49f1-b39f-4a5bf13f1562" />

La vérification du mot de passe n’est pas implémentée directement dans le code Java. Elle est déléguée à une fonction native (bar) située dans la bibliothèque libfoo.so. Cette technique est utilisée pour rendre l’analyse du code plus difficile, car elle nécessite l’utilisation d’outils de reverse engineering pour analyser la bibliothèque native et découvrir le secret.

<img width="695" height="231" alt="image" src="https://github.com/user-attachments/assets/7f89cd94-aa92-4ceb-967a-e213033f4b1c" />

L’application charge une bibliothèque native (libfoo.so) contenant la logique de vérification du mot de passe. Cette technique permet de cacher l’algorithme de validation du secret dans du code natif afin de compliquer son analyse. L’étude de cette bibliothèque avec l’outil Ghidra permet de retrouver la valeur du secret utilisée par l’application.

  Analyse de la bibliothèque native
  Dans l’application UnCrackable Level 2, la vérification du mot de passe n’est pas implémentée directement dans le code Java. Elle est réalisée à l’aide d’une fonction native située dans une bibliothèque partagée nommée libfoo.so. Cette technique est souvent utilisée par les développeurs afin de rendre l’analyse de l’application plus difficile, car le code natif est compilé et plus complexe à comprendre que le code Java.

Afin d’identifier le fonctionnement de cette vérification et de découvrir le secret attendu par l’application, il est nécessaire d’analyser cette bibliothèque native. Pour cela, nous utilisons l’outil Ghidra, un framework de reverse engineering développé par la National Security Agency (NSA). Cet outil permet d’examiner les fichiers binaires, de désassembler le code machine et de générer une représentation pseudo-code proche du langage C.

Dans cette étape, nous allons tout d’abord installer et configurer Ghidra, puis extraire la bibliothèque libfoo.so depuis l’APK de l’application. Ensuite, cette bibliothèque sera importée dans Ghidra afin d’effectuer une analyse statique du code natif. L’objectif de cette analyse est de localiser la fonction responsable de la vérification du mot de passe et de comprendre son fonctionnement afin d’identifier la chaîne secrète attendue par l’application.

<img width="1166" height="892" alt="image" src="https://github.com/user-attachments/assets/52685313-f076-4f40-b266-c4e63b851684" />
Dans cette étape, j’ai créé un projet dans l’outil Ghidra et j’ai importé la bibliothèque native libfoo.so extraite de l’application. Cette bibliothèque contient du code natif utilisé par l’application pour vérifier le mot de passe. L’importation de ce fichier dans Ghidra me permet d’analyser les fonctions présentes dans la bibliothèque et de comprendre comment l’application vérifie le secret.

<img width="616" height="405" alt="image" src="https://github.com/user-attachments/assets/42fda89a-2b45-4209-a6e3-6ede8b4cc0d4" />
Dans cette étape, j’ai ouvert la section Symbol Tree dans Ghidra et j’ai cliqué sur Exports. Cette section affiche les fonctions exportées par la bibliothèque libfoo.so. Cela permet d’identifier les fonctions importantes qui peuvent être appelées par l’application Android et de continuer l’analyse pour comprendre comment le mot de passe est vérifié.

<img width="1619" height="483" alt="image" src="https://github.com/user-attachments/assets/e2f9f90c-2d62-460e-849a-640c30181c6a" />

<img width="1302" height="1060" alt="image" src="https://github.com/user-attachments/assets/6c671fa0-8377-436b-95c2-25cb6e8e0e1a" />
Donc le secret est :
Thanks for all the fish



