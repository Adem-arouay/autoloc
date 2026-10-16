# Stratégie de fetch et de cascade - AutoLoc

| Association | Fetch | Cascade | Justification |
|---|---|---|---|
| Contrat → Paiement | LAZY | ALL + orphanRemoval | Un paiement n'existe que rattaché à son contrat (composition) ; supprimer le contrat supprime ses paiements, et retirer un paiement de la liste le supprime en base. |
| Agence → Vehicule | LAZY | Aucune | Un véhicule survit à la suppression de son agence ; la collection n'est chargée que si on l'utilise. |
| Agence → Employe | LAZY | Aucune | Un employé a une existence propre ; supprimer une agence ne doit pas supprimer ses employés. |
| Vehicule ↔ Equipement | LAZY | Aucune | Les équipements sont partagés entre plusieurs véhicules ; une cascade supprimerait des équipements encore utilisés. Un Set évite les doublons. |
| Client → Reservation | LAZY | PERSIST | Enregistrer un client avec de nouvelles réservations les enregistre aussi ; pas de REMOVE pour conserver l'historique des réservations. |
| Reservation → Vehicule | LAZY | Aucune | Le véhicule existe indépendamment de la réservation ; ne pas charger tout le véhicule systématiquement. |
| Reservation ↔ Contrat | LAZY | ALL (côté Reservation) | Le contrat découle de la réservation : il est créé, mis à jour et supprimé avec elle. |
| Vehicule → Maintenance | LAZY | PERSIST | Les maintenances d'un nouveau véhicule peuvent être enregistrées avec lui ; pas de REMOVE pour garder l'historique d'entretien. |

## Principes retenus
- LAZY partout : on évite de charger toute la chaîne d'objets liés ; les accès hors transaction seront traités par des requêtes dédiées (Atelier 7).
- Cascade uniquement en cas de composition (Contrat → Paiement, Reservation → Contrat).
- Pas de @Data sur les entités : évite les StackOverflowError sur les associations bidirectionnelles.