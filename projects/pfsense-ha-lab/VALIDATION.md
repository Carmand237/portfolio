# Compte rendu des validations

Cette page rapporte uniquement les résultats observés lors du second essai avec pfSense. Les preuves détaillées restent en local, car certaines captures exposent les adresses du laboratoire ou les identifiants des pare-feux.

## Matrice de tests

| Test | Procédure | Résultat attendu | Résultat observé |
|---|---|---|---|
| Rôles en régime normal | Lire l'état du [CARP (Common Address Redundancy Protocol)](README.md#carp) sur les deux nœuds. | Primaire `MASTER`, secondaire `BACKUP` pour les quatre adresses virtuelles. | Réussi pour les quatre adresses. |
| Réplication de configuration | Comparer les adresses virtuelles et les règles de synchronisation sur le secondaire. | Les éléments créés sur le primaire apparaissent sur le secondaire. | Quatre adresses virtuelles et trois règles de synchronisation présentes. |
| Synchronisation d'état | Lire l'état de [pfsync](README.md#pfsync) sur les deux nœuds. | Chaque nœud liste les deux membres. | Les deux identifiants apparaissaient sur chacun des nœuds. |
| Bascule | Suspendre la [machine virtuelle (VM)](README.md#vm) primaire et lire l'état du [CARP (Common Address Redundancy Protocol)](README.md#carp) sur le secondaire. | Le secondaire devient `MASTER`. | Le secondaire est devenu `MASTER` pour les quatre adresses virtuelles. |
| Joignabilité de la passerelle | Envoyer un [ping (test utilisant ICMP, Internet Control Message Protocol)](README.md#icmp) vers la passerelle [LAN (Local Area Network, réseau local)](README.md#lan) pendant la suspension du primaire. | La passerelle continue de répondre. | 4 paquets envoyés, 4 reçus, 0 % de perte. |
| Reprise | Reprendre la VM primaire et lire l'état du CARP sur les deux nœuds. | Le primaire redevient `MASTER`, le secondaire `BACKUP`. | Observé pour les quatre adresses ; aucun double maître. |

## Limites des tests

- La VM primaire a été suspendue, et non arrêtée proprement.
- Le ping de passerelle est un échantillon court de quatre paquets ; la durée de bascule n'a pas été mesurée.
- Aucun ping continu, aucune session applicative établie, aucun service externe ni aucune charge de DMZ n'ont été testés pendant la bascule.
- Les objets de configuration initiaux ont été vérifiés sur le secondaire. Une modification ultérieure et sa réplication n'ont pas été testées.

## Gestion des preuves

Le compte rendu détaillé local référence les sections de test 4.23 à 4.26. Les captures brutes ne sont pas incluses dans ce dossier public, car certaines contiennent des adresses privées du laboratoire ou des identifiants de pare-feu.

