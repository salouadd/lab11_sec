# LabSec11 – Analyse Dynamique Android avec Frida



---

## Contexte

Ce laboratoire présente l'utilisation de **Frida**, un framework d'instrumentation dynamique permettant d'observer et de modifier le comportement d'une application Android en temps réel.

L'exercice est réalisé sur les applications pédagogiques **OWASP MSTG UnCrackable Level 1** et **UnCrackable Level 2** afin d'étudier les mécanismes de protection intégrés et d'observer l'exécution de certaines méthodes Java.

L'objectif principal consiste à :

* Déployer Frida sur un émulateur Android ;
* Vérifier la présence des mécanismes de détection ;
* Instrumenter l'application à l'exécution ;
* Observer les appels de méthodes Java ;
* Analyser les valeurs retournées par les fonctions ciblées.

---

## Objectifs du Laboratoire

* Installer et démarrer Frida Server sur Android.
* Établir la communication entre Frida et l'émulateur.
* Vérifier le comportement initial de l'application.
* Charger des scripts d'instrumentation Java.
* Observer les appels de méthodes internes.
* Analyser les données retournées par l'application.
* Comprendre l'apport de l'instrumentation dynamique dans les tests de sécurité mobile.

---

## Environnement

| Élément             | Valeur                    |
| ------------------- | ------------------------- |
| Plateforme          | Android Emulator          |
| Framework           | Frida                     |
| Application 1       | `owasp.mstg.uncrackable1` |
| Application 2       | `owasp.mstg.uncrackable2` |
| Communication       | ADB Forwarding            |
| Architecture testée | Android x86_64            |

---

## Architecture du Laboratoire

```text
+----------------------+
| Poste Analyste       |
|                      |
| Frida CLI            |
| Scripts JavaScript   |
+----------+-----------+
           |
           | ADB Forward
           |
+----------v-----------+
| Android Emulator     |
|                      |
| Frida Server         |
| UnCrackable Level 1  |
| UnCrackable Level 2  |
+----------------------+
```

Cette architecture permet à l'outil Frida exécuté sur la machine hôte d'interagir directement avec les processus Android en cours d'exécution.

---

## Préparation de Frida

La première étape consiste à identifier l'architecture du terminal Android afin de déployer la version appropriée de Frida Server.

Le binaire est ensuite transféré dans l'émulateur puis rendu exécutable.

### Déploiement du Frida Server

![Déploiement Frida](https://github.com/user-attachments/assets/d3c1bc42-ec1d-498e-a965-383c81ad499d)

### Configuration du Port Forwarding

![ADB Forward](https://github.com/user-attachments/assets/1fb35142-8bb1-4962-92a0-15d1a6d63ff5)

### Commandes Utilisées

```bash
adb shell getprop ro.product.cpu.abi

adb push .\frida-server-17.9.1-android-x86_64 /data/local/tmp/

adb shell chmod 755 /data/local/tmp/frida-server-17.9.1-android-x86_64

adb shell "/data/local/tmp/frida-server-17.9.1-android-x86_64 -l 0.0.0.0"

adb forward tcp:27042 tcp:27042
adb forward tcp:27043 tcp:27043
```

Une fois ces étapes terminées, Frida est prêt à recevoir les connexions provenant de la machine hôte.

---

## Vérification du Comportement Initial

Avant toute instrumentation, l'application est exécutée dans son état d'origine afin d'observer son comportement par défaut.

### Application Avant Instrumentation

![Root Detected](https://github.com/user-attachments/assets/a72d4c59-4e2f-41f4-98e1-eabe9e443409)

Cette étape permet de confirmer que les mécanismes de protection sont actifs avant l'analyse dynamique.

---

## Instrumentation avec Frida

Frida est ensuite lancé avec un script JavaScript permettant d'interagir avec l'application pendant son exécution.

### Exécution de Frida

![Frida UnCrackable2](https://github.com/user-attachments/assets/b7685bb1-d005-4174-8c35-481bbdc02f57)

### Exemple de Commande

```bash
frida -U -f owasp.mstg.uncrackable2 -l .\bypass_java.js
```

Les journaux affichés dans la console confirment le chargement correct du script et l'instrumentation du processus cible.

---

## Observation d'une Méthode Java

L'analyse se poursuit par l'observation d'une méthode Java de l'application.

Un script Frida est utilisé afin d'afficher les paramètres transmis à la fonction ainsi que la valeur retournée.

### Exemple de Script

```javascript
Java.perform(function() {
    let a = Java.use("sg.vantagepoint.a.a");

    a["a"].implementation = function (bArr, bArr2) {

        console.log(
            `a.a is called: bArr=${bArr}, bArr2=${bArr2}`
        );

        let result = this["a"](bArr, bArr2);

        console.log(`a.a result=${result}`);

        return String(result);
    };
});
```

### Observation des Résultats

![Hook Méthode](https://github.com/user-attachments/assets/e4f85396-15ea-4dbd-9ff5-3b1b3688affd)

### Commande Utilisée

```bash
frida -U -f owasp.mstg.uncrackable1 \
-l .\bypass_java.js \
-l .\flag.js
```

Les sorties affichées dans la console permettent d'étudier le comportement de la méthode instrumentée.

---

## Analyse des Données Observées

La valeur observée est affichée sous forme de codes numériques :

```text
73 32 119 97 110 116 32 116 111 32 98 101 108 105 101 118 101
```

### Conversion des Valeurs

![Conversion](https://github.com/user-attachments/assets/1cb1cbf7-3284-43cf-b676-6a5a7b9b15c6)

### Décodage

![Décodage](https://github.com/user-attachments/assets/446f12f1-8c43-4e47-a74f-253f4435a56a)

L'analyse des valeurs observées permet de reconstituer le message retourné par l'application.

---

## Résultats

Les objectifs du laboratoire ont été atteints :

* ✅ Déploiement de Frida Server sur Android.
* ✅ Communication établie entre Frida et l'émulateur.
* ✅ Observation du comportement initial de l'application.
* ✅ Chargement de scripts JavaScript d'instrumentation.
* ✅ Observation des appels de méthodes Java.
* ✅ Analyse des données retournées par l'application.
* ✅ Compréhension du fonctionnement de l'instrumentation dynamique Android.

---

## Conclusion

Ce laboratoire a permis de découvrir l'utilisation de Frida dans un contexte d'analyse dynamique Android.

L'instrumentation en temps réel offre une visibilité importante sur le comportement interne d'une application et constitue un outil essentiel pour les travaux de sécurité mobile, l'analyse comportementale et la compréhension des mécanismes de protection implémentés dans les applications Android.

Cette approche complète les analyses statiques réalisées précédemment et fournit une vision plus précise de l'exécution réelle du code sur la plateforme Android.
