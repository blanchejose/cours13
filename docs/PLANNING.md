# Planning poker et plan de sprints — du 20 octobre au 10 novembre 2026

> Remise finale : **vendredi 13 novembre**. Le plan finit le **mardi 10 novembre**. Les 11, 12 et 13 novembre servent de zone tampon : aucune nouvelle fonctionnalité.

## 1. Échelle d'estimation

Suite de Fibonacci : **1 · 2 · 3 · 5 · 8 · 13**

| Points | Ce que ça représente (avec un agent IA, révision et tests compris) |
|---|---|
| 1 | Changement isolé, moins de 30 min |
| 2 | Petite fonctionnalité, un seul fichier ou composant |
| 3 | Fonctionnalité complète, simple, avec un test |
| 5 | Plusieurs couches (BD + serveur + UI), quelques cas limites |
| 8 | Logique difficile, temps réel ou beaucoup de cas limites |
| 13 | Trop gros : à découper avant de commencer |

Repère : **1 point ≈ 30 min de travail concentré**. Vélocité visée : **≈ 60 points par sprint** (≈ 30 h par semaine).

**Règle de suivi :** à la fin du sprint 1, compare les points réellement terminés à 59. Si tu es sous 45, applique tout de suite le plan B (section 6).

## 2. Hypothèses sur le checkpoint 1

La section 7 de l'énoncé (contenu du checkpoint 1) ne figurait pas dans le texte fourni. Je suppose que ces éléments sont **déjà faits** : TECH-01 à TECH-05, TECH-08 à TECH-10, DES-01 à DES-04, AUTH-01, et le déploiement HTTPS de base.

Les éléments marqués **(CP1?)** dans le backlog ont peut-être aussi été faits au checkpoint. S'ils le sont, retire leurs points : c'est du temps gagné.

## 3. Backlog estimé

### É1 — Cœur de la course (temps réel)
| ID | Story | Pts | Risque | Justification |
|---|---|---|---|---|
| TECH-06 · TECH-07 | Serveur WebSocket (Socket.IO ou `ws`), authentification à la connexion (session ou cookie invité), messages validés par Zod | 8 | Élevé | Base de tout le reste. Avec Next.js, il faut un serveur personnalisé ou un processus séparé. |
| COURSE-01 | Machine à états EN_ATTENTE → DÉCOMPTE → EN_COURSE → RÉSULTATS → EN_ATTENTE ou FERMÉE, avec diagramme et tests | 5 | Moyen | Pure logique, facile à tester unitairement. |
| COURSE-02 | Lancement permis seulement avec ≥ 2 participants, dont ≥ 1 humain | 1 | Faible | Une garde dans la machine à états. |
| COURSE-03 | Décompte 3-2-1 synchronisé sur l'horloge du serveur, texte révélé au début du décompte | 3 | Moyen | Décalage d'horloge entre le client et le serveur. |
| COURSE-04 · CONF-08 | Zone de frappe : affichage correct/incorrect/curseur, MPM et précision en direct, coller bloqué, deux modes d'erreur | 8 | Moyen | Beaucoup d'états d'affichage et les deux modes d'erreur. |
| COURSE-05 · PERF-03 | Piste signature : interpolation, avatars, MPM, joueur local mis en évidence, vue spectateur, fluide à 30 participants | 8 | Élevé | Élément signature du design, et performance à 30 participants. |
| COURSE-06 · PERF-02 | Serveur autoritaire : rejet des sauts et des vitesses irréalistes, envois limités à 10 par seconde, aucune écriture en BD par frappe | 5 | Moyen | Seuils anti-triche à calibrer. |
| COURSE-09 · COURSE-10 | Fin de course (tous terminés ou temps écoulé) et classement à 3 niveaux | 3 | Faible | Fonction pure, à tester. |
| COURSE-07 | Abandon avec confirmation | 1 | Faible | |
| COURSE-08 | Reconnexion dans les 30 s (rechargement, perte de réseau), sinon abandon | 5 | Élevé | Minuteries côté serveur, plusieurs onglets. |
| COURSE-11 | Sur l'écran des résultats : relancer ou fermer | 2 | Faible | |

### É2 — Salles
| ID | Story | Pts | Risque | Justification |
|---|---|---|---|---|
| SALLE-01 · SALLE-02 | Création de salle, hôte participant ou spectateur, code de 6 caractères sans caractères ambigus **(CP1?)** | 2 | Faible | |
| SALLE-03 | Trois visibilités : publique, sur code, privée | 3 | Moyen | Règles d'accès à couvrir par des tests. |
| SALLE-04 | Liens d'invitation : jeton de 128 bits, usage unique lié à l'IP, statuts, reconnexion, révocation | 5 | Élevé | Point de sécurité. L'IP derrière le proxy inverse demande `X-Forwarded-For` fiable. |
| SALLE-05 | Capacité de 2 à 30 participants, spectateurs exclus | 1 | Faible | |
| SALLE-06 | Une seule salle par personne : contrainte unique en BD, proposition de quitter l'autre salle, pas de doublon entre onglets | 5 | Élevé | Contrainte BD et conditions de course. |
| SALLE-07 | Expulsion et bannissement de la salle | 2 | Faible | |
| SALLE-08 | Transfert d'hôte à l'humain connecté présent depuis le plus longtemps, sinon fermeture | 3 | Moyen | |
| SALLE-09 | Accès seulement en attente ou sur l'écran des résultats | 1 | Faible | |
| SALLE-10 | Limite de 10 tentatives de code par minute par IP | 2 | Faible | |

### É3 — Rejoindre une course
| ID | Story | Pts | Risque | Justification |
|---|---|---|---|---|
| JOIN-01 | Champ de code sur la page d'accueil **(CP1?)** | 1 | Faible | |
| JOIN-02 | Explorateur des salles publiques mis à jour en direct, filtres langue et complexité | 5 | Moyen | Diffusion temps réel des changements de salles. |
| JOIN-03 | « Faire une course » : salle la plus près de sa capacité, la plus ancienne en cas d'égalité, états vides | 3 | Faible | Requête plus deux états vides. |

### É4 — Configuration et textes
| ID | Story | Pts | Risque | Justification |
|---|---|---|---|---|
| CONF-02 · CONF-03 (base) | Corpus FR et EN minimal plus dictionnaire, dans le seed | 3 | Faible | Nécessaire dès le sprint 1 pour avoir des textes à taper. |
| CONF-05 · CONF-03 (complet) | Critères de complexité mesurables, documentés, corpus complet | 5 | Moyen | Documentation à écrire dans EXIGENCES.md. |
| CONF-06 · CONF-07 | Générateur aléatoire : ponctuation, nombres, majuscules, accents, inclusion et exclusion de caractères | 5 | Moyen | Comportement en mode cohérent à documenter. |
| CONF-01 · 04 · 09 · 11 · 12 | Formulaire de l'hôte et configuration synchronisée en direct pour tous | 5 | Moyen | |
| CONF-10 · BOT-04 | Ajout et retrait de bots dans l'interface, bots clairement identifiés | 2 | Faible | |

### É5 — Bots
| ID | Story | Pts | Risque | Justification |
|---|---|---|---|---|
| BOT-01 · 02 · 03 · 05 | Moteur déterministe à partir d'une graine, 5 niveaux, variations de vitesse, erreurs et correction, respect du mode d'erreur, tests | 8 | Moyen | Calibration des vitesses. Très testable. |

### É6 — Bonus de remontée
| ID | Story | Pts | Risque | Justification |
|---|---|---|---|---|
| BONUS-01 | Règle des points de contrôle 25/50/75 %, retard, maximum 3 bonus, documentée et testée | 3 | Moyen | |
| BONUS-02 · BONUS-04 | 3 types de bonus (+3 mots, −3 mots, brouillard), progression et MPM cohérents | 5 | Élevé | Changer la longueur du texte en pleine course. |
| BONUS-03 | Annonce sur la piste et chez le joueur ciblé | 2 | Faible | |

### É7 — Résultats, historique et profil
| ID | Story | Pts | Risque | Justification |
|---|---|---|---|---|
| RES-05 | Persistance des résultats et de la série temporelle du MPM | 3 | Faible | |
| RES-01 · RES-02 | Podium et tableau complet (`<table>`) | 3 | Faible | |
| RES-03 | Graphique du MPM de tous les participants et carte de chaleur du clavier | 5 | Moyen | |
| RES-04 | Indicateur de record personnel | 1 | Faible | |
| HIST-01 · HIST-02 | Historique paginé, avec lien vers la page de résultats | 3 | Faible | |
| AUTH-06 | Page de profil : statistiques et graphique de progression | 3 | Faible | |

### É8 — Comptes
| ID | Story | Pts | Risque | Justification |
|---|---|---|---|---|
| AUTH-02 · AUTH-03 | Invité : pseudonyme, cookie signé, avatar généré, création de salle interdite **(CP1?)** | 3 | Moyen | Nécessaire avant de travailler sur les salles. |
| AUTH-04 · SEC-02 | Téléversement de la photo : type réel du fichier (octets magiques), 2 Mo maximum, redimensionnement avec sharp | 3 | Moyen | |
| AUTH-05 | Modifier le pseudonyme | 1 | Faible | |

### É9 — Qualité et transversal
| ID | Story | Pts | Risque | Justification |
|---|---|---|---|---|
| I18N (base) | next-intl, sélecteur de langue, cookie, langue du navigateur par défaut | 2 | Faible | **À faire tôt** : toute nouvelle chaîne passe ensuite par l'i18n. |
| I18N (passe finale) | Aucune chaîne codée en dur, métadonnées, dates et nombres formatés | 6 | Moyen | Long et répétitif. |
| DES-05 | Thèmes clair et sombre sans flash **(CP1?)** | 2 | Faible | |
| DES-06 | Responsive à 360 px, message « clavier physique » sur mobile | 3 | Faible | |
| A11Y-01 à 04 | Audit du contraste, des balises, des libellés et du focus, puis corrections | 3 | Faible | |
| PERF-01 | Lighthouse ≥ 90 partout sur l'accueil | 3 | Moyen | |
| TEST-01 | Compléter la couverture des tests unitaires | 5 | Faible | |
| TEST-02 · TEST-03 (base) | Playwright configuré dans la CI, connexion par mot de passe, premier scénario | 3 | Moyen | |
| TEST-02 (scénarios) | Créer une salle, la rejoindre, faire une course contre un bot, consulter les résultats | 5 | Moyen | Temps réel dans Playwright : utiliser 2 contextes de navigateur. |
| SEC-01 | Audit de toutes les actions réservées à l'hôte côté serveur | 2 | Faible | |
| DEPLOI | WebSocket en production : proxy inverse, `wss://`, redémarrage automatique | 3 | Élevé | **À faire dès le sprint 1**, pas à la fin. |
| DOCS | EXIGENCES.md, matrice de traçabilité, README, diagramme de la machine à états | 3 | Faible | |

**Total : 185 points.**

## 4. Sprints

### Sprint 1 — « Une course jouable en production » · mar. 20 oct. → lun. 26 oct. · **59 pts**

**Objectif vérifiable :** en production, 2 navigateurs et 1 bot font une course complète, du décompte au classement.

| Ordre | Story | Pts |
|---|---|---|
| 1 | SALLE-01 · SALLE-02 (vérifier ou compléter) | 2 |
| 2 | I18N (base) | 2 |
| 3 | TECH-06 · TECH-07 : serveur WebSocket | 8 |
| 4 | **DEPLOI : WebSocket en production** (mar. ou mer.) | 3 |
| 5 | COURSE-01 · COURSE-02 : machine à états | 6 |
| 6 | CONF-02 · CONF-03 (base) : corpus minimal dans le seed | 3 |
| 7 | COURSE-03 : décompte | 3 |
| 8 | COURSE-04 · CONF-08 : zone de frappe | 8 |
| 9 | COURSE-06 · PERF-02 : serveur autoritaire | 5 |
| 10 | COURSE-05 · PERF-03 : piste | 8 |
| 11 | BOT-01 · 02 · 03 · 05 : moteur de bots | 8 |
| 12 | COURSE-09 · COURSE-10 : fin et classement | 3 |

Point de mi-sprint (**jeu. 22 oct.**) : le WebSocket fonctionne en production et la machine à états est testée.

### Sprint 2 — « Des salles complètes et une course corsée » · mar. 27 oct. → lun. 2 nov. · **64 pts**

**Objectif vérifiable :** un invité rejoint une salle privée par lien d'invitation. L'hôte configure la course, ajoute des bots, et les bonus se déclenchent.

| Ordre | Story | Pts |
|---|---|---|
| 1 | AUTH-02 · AUTH-03 : invités | 3 |
| 2 | SALLE-06 : une seule salle (contrainte BD) | 5 |
| 3 | SALLE-03 · 05 · 09 · 10 : visibilité, capacité, accès, limite de tentatives | 7 |
| 4 | SALLE-04 : liens d'invitation | 5 |
| 5 | SALLE-07 · SALLE-08 : expulsion, transfert d'hôte | 5 |
| 6 | JOIN-01 : champ de code | 1 |
| 7 | CONF-01 · 04 · 09 · 11 · 12 : formulaire et synchronisation | 5 |
| 8 | CONF-05 · CONF-03 (complet) : complexité | 5 |
| 9 | CONF-06 · CONF-07 : générateur aléatoire | 5 |
| 10 | CONF-10 · BOT-04 : bots dans l'interface | 2 |
| 11 | COURSE-07 · COURSE-08 · COURSE-11 : abandon, reconnexion, relance | 8 |
| 12 | BONUS-01 · 02 · 03 · 04 : bonus | 10 |
| 13 | TEST-02 · TEST-03 (base) : Playwright dans la CI | 3 |

Point de mi-sprint (**jeu. 29 oct.**) : toutes les règles de salle (SALLE-*) sont testées et déployées.

### Sprint 3 — « Résultats, finition et qualité » · mar. 3 nov. → mar. 10 nov. · **62 pts**

**Objectif vérifiable :** toutes les exigences sont cochées dans la matrice, Lighthouse ≥ 90, la CI est verte avec les tests E2E.

| Ordre | Story | Pts |
|---|---|---|
| 1 | RES-05 : persistance | 3 |
| 2 | RES-01 · RES-02 · RES-04 : podium, tableau, record | 4 |
| 3 | RES-03 : graphiques | 5 |
| 4 | HIST-01 · HIST-02 : historique | 3 |
| 5 | AUTH-06 : profil et statistiques | 3 |
| 6 | AUTH-04 · AUTH-05 · SEC-02 : photo, pseudonyme | 4 |
| 7 | JOIN-02 · JOIN-03 : explorateur, partie rapide | 8 |
| 8 | I18N (passe finale) | 6 |
| 9 | DES-05 · DES-06 : thèmes, responsive | 5 |
| 10 | TEST-02 (scénarios) | 5 |
| 11 | TEST-01 : compléter les tests unitaires | 5 |
| 12 | SEC-01 : audit de l'hôte | 2 |
| 13 | A11Y-01 à 04 · PERF-01 : accessibilité, Lighthouse | 6 |
| 14 | DOCS : EXIGENCES.md, matrice de traçabilité | 3 |

Point de mi-sprint (**ven. 6 nov.**) : les résultats, l'historique et le profil sont faits. Il ne reste que de la finition et de la qualité.

### Zone tampon — mer. 11 nov. → ven. 13 nov.

Gel des fonctionnalités. Seulement : correction de bogues, vérification complète de la production, relecture de la matrice et de la démarche créative, remise.

## 5. Définition de « terminé » (pour chaque story)

- [ ] Fonctionne en production, pas seulement en local
- [ ] Entrées validées par Zod côté serveur (TECH-07)
- [ ] Chaînes passées par l'i18n, en FR et en EN (I18N-01)
- [ ] Test unitaire si la story contient de la logique (TEST-01)
- [ ] `lint`, `tsc --noEmit` et les tests passent dans la CI
- [ ] Ligne ajoutée dans la matrice de traçabilité
- [ ] Choix ambigus notés dans `docs/EXIGENCES.md`

## 6. Plan B si le sprint prend du retard

Tout est noté, donc on ne supprime rien. On **simplifie**, dans cet ordre :

1. RES-03 : carte de chaleur en grille simple plutôt qu'un vrai clavier dessiné
2. BONUS-02 : garder seulement les 3 bonus minimaux
3. JOIN-02 : rafraîchir par sondage (polling) toutes les 5 s plutôt que par WebSocket (« sans rechargement manuel » reste respecté)
4. CONF-07 : désactiver l'option en mode cohérent (permis par l'énoncé, à documenter)
5. PERF-03 : interpolation CSS simple plutôt qu'une boucle `requestAnimationFrame`

À ne **jamais** sacrifier : serveur autoritaire (COURSE-06), la piste (élément signature), la sécurité (SALLE-04, SALLE-06, SEC-*), la production fonctionnelle, la CI verte.
