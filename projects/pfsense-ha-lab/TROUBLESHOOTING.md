# Diagnostic des difficultés

Les diagnostics font partie du projet : j'ai révisé mes conclusions lorsque les tests les contredisaient.

## 1. Échec du ping sur le réseau de synchronisation

**Symptôme :** les deux nœuds communiquaient sur le réseau interne, mais le ping échouait sur l'interface dédiée à la synchronisation.

**Première hypothèse :** le réseau virtuel abandonnait les trames étiquetées. Une entrée voisine incomplète semblait soutenir cette hypothèse, mais elle ne prouvait pas la cause.

**Test discriminant :** ajouter une règle [ICMP (Internet Control Message Protocol)](README.md#icmp) limitée au réseau de synchronisation, puis refaire le test de connectivité `ping` dans les deux sens.

**Cause confirmée au second essai :** l'interface n'avait aucune règle d'autorisation, donc le trafic entrant était bloqué par défaut. Après l'ajout de la règle, le ping de synchronisation a réussi dans les deux sens. L'ancienne entrée [ARP (Address Resolution Protocol, protocole de résolution d'adresses)](README.md#arp) incomplète reste inexpliquée ; je ne la présente pas comme résolue.

**Leçon :** vérifier la politique de l'interface avant de conclure à une panne de commutation ou de liaison.

## 2. Délai dépassé de la réplication en HTTP

**Symptôme :** [XML-RPC (Extensible Markup Language Remote Procedure Call)](README.md#xml-rpc) dépassait le délai sur le port [TCP (Transmission Control Protocol)](README.md#tcp) 80 alors que les règles de synchronisation autorisaient le port TCP 443.

**Cause :** les deux interfaces d'administration utilisaient [HTTP (Hypertext Transfer Protocol)](README.md#http). XML-RPC reprenait donc ce protocole et ce port. Les paramètres de cible de réplication étaient également renseignés par erreur sur le secondaire.

**Correction :** passer les deux interfaces d'administration en [HTTPS (Hypertext Transfer Protocol Secure)](README.md#https) ; garder la cible de réplication uniquement sur le primaire ; laisser les champs de cible vides sur le secondaire ; autoriser la connexion chiffrée sur le réseau de synchronisation.

**Vérification :** les adresses virtuelles et les règles de synchronisation du primaire sont apparues sur le secondaire, puis l'état de bascule a été vérifié sur les deux nœuds.

## 3. Mauvaise correspondance des interfaces au premier essai

**Symptôme :** les interfaces de synchronisation et des DMZ portaient des noms internes différents entre les deux nœuds après plusieurs réassignations.

**Correction :** reconstruire le second essai avec des cartes réseau virtuelles identiques et des assignations correspondantes, puis vérifier celles-ci avant d'activer la réplication de configuration.

**Vérification :** les règles répliquées apparaissaient sur la bonne interface de synchronisation du secondaire.

## 4. Entrées de test erronées

Certains contrôles du premier essai utilisaient une adresse erronée ou un masque `/32` à la place de `/24`. Ces erreurs de saisie ne prouvaient pas une panne du cluster ou du commutateur. Avant de valider les tests suivants, j'ai relu les adresses et les masques dans les écrans d'interface et la console.

