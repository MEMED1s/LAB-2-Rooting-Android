# LAB 2 : Rooting Android

**Cours :** Sécurité des applications mobiles  
**Auteur :** Hmami  
**Plateforme :** Android Emulator / Android Studio

---

## Avant de commencer

Ce laboratoire a pour objectif de comprendre ce que le rooting change sur un système Android, comment vérifier qu’un AVD est bien rooté, comment observer les effets sur les protections Android, puis comment remettre l’environnement à zéro à la fin du test.

### Règles appliquées

- Application de test uniquement
- Données fictives uniquement
- Environnement isolé
- Aucun usage sur appareil personnel
- Remise à zéro obligatoire en fin de séance

---

## Étape 1 : Lancer l’AVD de test

L’émulateur utilisé pour ce laboratoire est un AVD nommé **`test`**.  
Un premier essai de lancement sans préciser correctement l’AVD affiche un message d’erreur, puis l’AVD est démarré avec l’option `-writable-system` pour permettre les manipulations nécessaires au laboratoire.

### Commandes exécutées

```bash
emulator test
emulator -avd test -writable-system

Observation

La première commande échoue car aucun AVD valide n’est précisé.

La seconde commande lance correctement l’émulateur test.

L’option -writable-system est utilisée pour autoriser les modifications système pendant le laboratoire.

Screenshot

Étape 2 : Vérifier le root sur l’AVD

Une fois l’émulateur démarré, plusieurs commandes ADB sont exécutées pour vérifier l’état root, l’état de Verified Boot et le comportement de verity.

Commandes exécutées
adb root
adb remount
adb shell id
adb shell getprop ro.boot.verifiedbootstate
adb shell getprop ro.boot.veritymode
adb shell getprop ro.boot.vbmeta.device_state
adb shell "su -c id"
Résultats obtenus
Commande	Résultat
adb shell id	uid=0(root) gid=0(root)
ro.boot.verifiedbootstate	orange
ro.boot.veritymode	enforcing
ro.boot.vbmeta.device_state	valeur vide
adb shell "su -c id"	erreur invalid uid/gid "-c"
Interprétation

uid=0(root) confirme que l’AVD dispose bien des privilèges root.

verifiedbootstate = orange indique que l’intégrité standard du système n’est plus garantie, ce qui est attendu dans un contexte de rooting.

veritymode = enforcing apparaît encore dans les propriétés, mais adb remount indique bien que verity a été désactivé et que les overlayfs sont utilisés.

La valeur vide pour ro.boot.vbmeta.device_state est acceptable sur cet environnement émulé.

La commande su -c id échoue ici à cause de la syntaxe/interprétation du su dans cet environnement, mais cela ne remet pas en cause le fait que le shell est déjà root, car adb shell id renvoie déjà uid=0(root).

Détail important observé dans adb remount

Le terminal indique notamment :

Successfully disabled verity

Using overlayfs for /system

Using overlayfs for /vendor

Using overlayfs for /product

Using overlayfs for /system_dlkm

Using overlayfs for /system_ext

Cela confirme que l’émulateur fonctionne en mode modifié pour le laboratoire.

Screenshot

Étape 3 : Journalisation de vérification root

Afin de garder une trace du test, un export partiel du logcat est sauvegardé dans un fichier texte.

Commande exécutée
adb logcat -d | Select-Object -Last 200 | Out-File logcat_root_check.txt
Observation

Le fichier logcat_root_check.txt est bien créé dans le dossier de travail, ce qui permet de conserver une preuve technique de la session.

Screenshot

Étape 4 : Vérifier que l’AVD est bien détecté

Avant d’installer ou de tester l’application, il faut confirmer que l’émulateur est bien visible par ADB.

Commande exécutée
adb devices
Résultat
emulator-5554    device
Observation

L’AVD test est correctement démarré et reconnu par ADB.

Screenshot

Étape 5 : Exécuter le scénario applicatif

L’application de test affiche un formulaire simple avec deux champs de saisie et un bouton Envoyer.
Le scénario consiste à saisir des données fictives puis vérifier l’affichage d’une notification.

Données utilisées

Nom : hmami

Prénom : mohamed

Résultat observé

Après soumission, une notification apparaît avec les données saisies :

hmami mohamed
Observation

L’application fonctionne normalement sur l’AVD rooté. Le formulaire s’affiche correctement et la soumission produit bien un retour visuel dans l’interface.

Screenshot

Étape 6 : Vérifier le stockage local de l’application

Le stockage interne de l’application est observé depuis l’AVD rooté afin de voir ce qui est conservé après exécution.

Commandes exécutées
adb shell ls /data/data/ma.ens.myapplication/
adb shell ls /data/data/ma.ens.myapplication/shared_prefs/
adb shell cat /data/data/ma.ens.myapplication/files/profileInstalled
Résultats observés

Le dossier de l’application contient :

cache

code_cache

files

Le dossier shared_prefs n’existe pas :

No such file or directory

Le contenu du fichier profileInstalled n’est pas lisible en clair.

Interprétation

L’application écrit des données dans le dossier files/.

Aucune préférence partagée (SharedPreferences) visible n’est utilisée dans ce test.

Les données observées dans profileInstalled ne sont pas directement lisibles comme du texte clair.

Screenshot

Étape 7 : Remise à zéro de l’AVD

À la fin du laboratoire, l’AVD est remis à zéro à partir du Device Manager d’Android Studio via l’option Wipe Data.

Action réalisée

Ouverture du Device Manager

Sélection de l’AVD test

Confirmation de Wipe Data

Observation

Une fenêtre de confirmation apparaît avant l’effacement des données utilisateur de l’AVD.

Screenshot

Étape 8 : Vérifier le retour à un état propre

Après le wipe, l’émulateur redémarre sur un état propre, comparable à un démarrage initial de l’AVD.

Observation

L’écran d’accueil Android réapparaît sans les traces du test précédent, ce qui confirme que la remise à zéro a bien été effectuée.

Screenshot

Résumé sécurité Android

Android repose sur plusieurs mécanismes de protection complémentaires :

Sandboxing des applications : chaque application s’exécute dans un espace isolé.

Gestion des permissions : l’accès aux ressources sensibles doit être autorisé.

Protection de l’intégrité système : Verified Boot et AVB participent à la vérification du système au démarrage.

Partitions protégées : les partitions système sont normalement en lecture seule.

Le rooting contourne une partie de ces protections en donnant un accès privilégié au système.

Verified Boot et état observé

Dans ce laboratoire, la commande suivante a donné :

adb shell getprop ro.boot.verifiedbootstate

Résultat :

orange
Signification

Green : système vérifié et intact

Orange : système modifié, fonctionnement possible mais intégrité non garantie

Red : intégrité compromise

Dans notre cas, l’état orange est cohérent avec un environnement de test rooté.

Impact de adb remount

La commande adb remount a produit plusieurs indices techniques importants :

Successfully disabled verity
Using overlayfs for /system
Using overlayfs for /vendor
Using overlayfs for /product
Using overlayfs for /system_dlkm
Using overlayfs for /system_ext
Verity disabled; overlayfs enabled.
Interprétation

Verity désactivé : la vérification stricte d’intégrité des partitions système n’est plus appliquée comme dans un environnement standard.

Overlayfs activé : les modifications sont rendues possibles par une couche superposée sur certaines partitions.

Contexte de test : ce comportement est acceptable dans un laboratoire, mais ne représente pas un état sûr pour un appareil utilisateur normal.

Intérêt pédagogique du labo

Ce laboratoire permet de :

comprendre ce qu’est réellement un appareil/root shell Android

observer les protections système et leurs limites

examiner le stockage interne d’une application

voir l’impact d’un environnement privilégié sur la sécurité applicative

appliquer une remise à zéro propre en fin de manipulation

Risques d’un environnement rooté

Un environnement rooté présente plusieurs risques :

intégrité système non garantie

exposition plus forte aux modifications non contrôlées

accès facilité aux données applicatives

conclusions de sécurité biaisées si l’on confond labo rooté et appareil utilisateur réel

risque de contamination si le reset final n’est pas effectué

Mesures défensives appliquées

Pendant ce laboratoire, les précautions suivantes sont respectées :

AVD dédié uniquement aux tests

données fictives uniquement

aucune utilisation d’un téléphone personnel

journalisation minimale des actions techniques

remise à zéro finale avec Wipe Data

vérification explicite de l’état root et du stockage local

Fiche environnement
Champ	Valeur
Auteur	Hmami
Support	Android Emulator
AVD	test
Version Android	Android 16.0 / API 36
Architecture	x86_64
App testée	ma.ens.myapplication
Données utilisées	hmami / mohamed
Root actif	Oui
Verified Boot state	orange
Reset final	Oui, via Wipe Data
Commandes principales utilisées
emulator -avd test -writable-system
adb root
adb remount
adb shell id
adb shell getprop ro.boot.verifiedbootstate
adb shell getprop ro.boot.veritymode
adb shell getprop ro.boot.vbmeta.device_state
adb devices
adb logcat -d | Select-Object -Last 200 | Out-File logcat_root_check.txt
adb shell ls /data/data/ma.ens.myapplication/
adb shell ls /data/data/ma.ens.myapplication/shared_prefs/
adb shell cat /data/data/ma.ens.myapplication/files/profileInstalled
Checklist finale
Début de séance

 AVD test lancé

 Option -writable-system utilisée

 Émulateur détecté par adb devices

 Root vérifié avec adb shell id

 Verified Boot observé

 Journalisation créée

Pendant le test

 Formulaire exécuté

 Données fictives saisies

 Notification affichée

 Stockage local observé

Fin de séance

 Wipe Data effectué

 AVD relancé proprement

 Environnement remis à zéro

Arborescence conseillée du projet
LAB-2-Rooting-Android/
│── README.md
│── logcat_root_check.txt
└── screenshots/
    │── etape1_lancement_avd.png
    │── etape2_root_check.png
    │── etape3_logcat_root_check.png
    │── etape4_adb_devices.png
    │── etape5_formulaire_notification.png
    │── etape6_stockage_local.png
    │── etape7_wipe_data_confirmation.png
    └── etape8_avd_apres_reset.png
Conclusion

Ce laboratoire a permis de vérifier qu’un AVD Android peut être démarré en mode modifiable, rooté via ADB, puis utilisé pour observer l’impact du rooting sur les protections système. Les tests montrent un shell root actif, un état verifiedbootstate = orange, l’usage d’overlayfs après désactivation de verity, ainsi qu’un accès au stockage interne de l’application testée. Enfin, l’environnement a été correctement remis à zéro à l’aide de Wipe Data, garantissant une fin de séance propre.
