# Lab Android — Rooting et sécurité

## 1. Objectif du lab

Le but de ce lab est de comprendre ce que le **root** change sur Android et de voir son impact sur la sécurité du système.
Le travail a été fait sur un **AVD Genymotion** uniquement, dans un environnement de test.

## 2. Périmètre

* **Application testée :** `app-debug.apk`
* **Support :** AVD / Genymotion
* **Version Android :** Android 8.1 (Oreo)
* **Données :** fictives uniquement
* **Réseau :** environnement de test

## 3. Root de l’AVD

L’AVD a été connecté avec ADB puis passé en mode root.
Les vérifications ont montré que le shell était bien en **root** (`uid=0`).
La commande `adb remount` a aussi réussi, ce qui montre que le système était modifiable.

<img width="896" height="595" alt="im1" src="https://github.com/user-attachments/assets/00a5ae11-6f36-4d0a-97a2-4e150e889232" />


## 4. Installation de l’application

L’application `app-debug.apk` a été installée avec succès sur l’AVD.
Le package détecté est **`ma.ens.myapplication`**.
L’application a ensuite pu être ouverte sur l’émulateur.

<img width="1713" height="701" alt="im2" src="https://github.com/user-attachments/assets/3e3e6aa6-0e9a-472f-87f0-d172b6bb8b0f" />



## 5. Trois scénarios simples

Pour garder des tests simples et répétables, j’ai retenu ces 3 scénarios :

1. Ouvrir l’application
2. Interagir avec l’écran principal
3. Vérifier le résultat affiché

## 6. Résumé Android Security

Android protège les applications avec plusieurs couches de sécurité.
Chaque application est isolée grâce au **sandboxing**.
Les **permissions** contrôlent l’accès aux ressources sensibles.
Le root affaiblit ces protections car il donne des privilèges plus élevés sur le système.

## 7. Verified Boot

Le rôle de **Verified Boot** est de vérifier que le système qui démarre n’a pas été modifié de manière malveillante.
Il repose sur une chaîne de confiance au démarrage.
Dans ce lab, la commande `adb shell getprop ro.boot.verifiedbootstate` n’a retourné aucune valeur sur Genymotion, donc cette information n’était pas visible sur cet AVD.

<img width="1123" height="314" alt="im3" src="https://github.com/user-attachments/assets/ffa86f1a-ff08-474e-a795-601727a699e9" />


## 8. AVB

**AVB (Android Verified Boot)** est une version plus récente de Verified Boot.
Il améliore la vérification de l’intégrité du système.
Il ajoute aussi une protection contre le **rollback**, pour éviter de réinstaller une ancienne version vulnérable.

## 9. Définition du rooting

Le rooting permet d’obtenir les privilèges **super-utilisateur** sur Android.
Il donne accès à des parties du système normalement protégées.
C’est utile en labo pour observer certains comportements.
Mais cela réduit la sécurité normale du système.

## 10. Intérêt du lab

Le root en laboratoire permet d’observer des éléments du système normalement inaccessibles.
Il aide aussi à analyser le comportement de l’application à un niveau plus bas.
Ces manipulations doivent rester dans un **labo autorisé uniquement**.

## 11. Risques

1. Les résultats peuvent être biaisés car l’intégrité du système n’est plus normale.
2. Si des données réelles sont utilisées, elles peuvent être exposées.
3. Le système peut devenir instable.
4. Une mauvaise traçabilité peut rendre les tests difficiles à reproduire.

## 12. Mesures défensives

1. Utiliser uniquement des données fictives
2. Travailler sur un AVD dédié au test
3. Ne connecter aucun compte personnel
4. Remettre l’AVD à zéro à la fin

## 13. Deux idées de tests MASTG

1. Vérifier si l’application stocke des données sensibles en clair
2. Vérifier dans les logs si des informations sensibles apparaissent

## 14. Remise à zéro

À la fin du lab, l’AVD doit être remis à zéro pour supprimer les données et les modifications faites pendant le test.

## 14. autre simulation de DivaApp.apk

<img width="1918" height="912" alt="im4" src="https://github.com/user-attachments/assets/09577219-9a5d-448a-bf75-e1ab63f996d9" />

<img width="408" height="680" alt="im5" src="https://github.com/user-attachments/assets/98dff448-2632-49e7-b577-2458177ade3a" />

<img width="1418" height="146" alt="im6" src="https://github.com/user-attachments/assets/91f96418-0498-42e2-a9a2-2a53f46bd666" />




