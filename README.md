# Sec-Mobile-Lab16

# Analyse et contournement du SSL Pinning sur Android (Frida & Objection)

## 1. Contexte du travail
Ce travail pratique porte sur l’analyse du trafic réseau d’une application Android protégée par SSL pinning. L’objectif est de mettre en place un environnement de test permettant l’interception du trafic HTTPS afin de comprendre les mécanismes de protection et leurs limites dans un cadre d’audit autorisé.

---
Les composants suivants ont été mis en place :

* Frida pour l’instrumentation dynamique des applications
* Objection comme interface simplifiée basée sur Frida
* Frida-tools pour les commandes d’analyse et de gestion

---

## 2. Configuration du proxy HTTPS

Un proxy réseau a été configuré sur l’appareil Android afin d’intercepter le trafic sortant.

### Paramètres configurés :

* Adresse du proxy : 172.17.0.1
* Port : 8081
---

## 3. Installation du certificat CA

Afin de permettre l’interception du trafic HTTPS, un certificat racine a été installé sur l’appareil.

### Méthode Burp Suite :

Accès depuis le navigateur Android :

```
http://burp
```
<img width="1197" height="288" alt="image" src="https://github.com/user-attachments/assets/4967bd4e-6953-4563-9003-ce4440beec70" />

Le certificat a ensuite été téléchargé et installé sur le système.

## 4. Identification de l’application cible

Les applications présentes sur l’appareil ont été listées à l’aide de Frida :

```bash
frida-ps -Uai
```
---

## 5. Désactivation du SSL Pinning

Deux méthodes principales ont été utilisées pour contourner le SSL pinning.

### 5.1 Injection au démarrage (spawn)

Cette méthode consiste à injecter Frida avant le lancement de l’application :

```bash
objection -g com.example.app explore --startup-command "android sslpinning disable"
```

Cette approche est efficace lorsque l’application active ses vérifications SSL dès le démarrage.

---

### 7.2 Injection sur application en cours (attach)

Dans ce cas, l’application est lancée manuellement puis l’injection est effectuée :

```bash
objection -g com.example.app explore
```

Une fois dans la console Objection :

```bash
android sslpinning disable
```

---

## 6. Cas pratique avec l’application Diva

Un test a été réalisé sur l’application Diva afin de valider la méthode :

```bash
objection -g diva explore --startup-command "android sslpinning disable"
```

Le comportement observé confirme la désactivation du SSL pinning et la possibilité d’interception du trafic.

---

## 9. Commande simplifiée

Une commande unique permet de lancer directement l’application avec désactivation du pinning :

```bash
objection -g com.example.app explore --startup-command "android sslpinning disable"
```

---

## 10. Conclusion

Ce TP a permis de comprendre le fonctionnement du SSL pinning et ses limites dans un contexte d’analyse sécuritaire. L’utilisation combinée de Frida et Objection permet d’intercepter le trafic HTTPS d’une application Android dans un environnement contrôlé.

L’expérience met en évidence l’importance de sécuriser correctement les communications réseau dans les applications mobiles et les limites des protections basées uniquement sur la vérification des certificats.

```
