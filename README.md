# Kit 4 automatisations pour une PME agroalimentaire (n8n)

Les quatre automatisations de la vidéo « 4 automatisations que toute PME agroalimentaire devrait avoir », prêtes à importer dans n8n.
Elles suivent la vie d'une commande : elle arrive, on se fait payer, le client recommande, et le lundi on voit tout.

Exemple fourni : une biscuiterie fictive, avec 26 clients, 13 produits et plus de 7 000 lignes de ventes.

## Les quatre automatisations

| | Automatisation | Ce qu'elle fait | IA |
|---|---|---|---|
| 1 | [Commandes reçues par mail](1-commandes-par-mail/) | Lit chaque commande (PDF, Excel ou texte du mail), l'ajoute au tableau des commandes, envoie l'accusé de réception. Au moindre doute, elle vous alerte au lieu de deviner. | oui |
| 2 | [Relance des impayés](2-relance-impayes/) | Chaque matin, relance les factures en retard : mail poli, puis rappel avec la facture, puis mail ferme. À 30 jours, elle vous demande d'appeler. | non |
| 3 | [Clients dormants](3-clients-dormants/) | Chaque lundi, repère les clients qui ne commandent plus à leur rythme habituel et prépare pour chacun une relance personnalisée, en brouillon. | oui |
| 4 | [Rapport du lundi](4-rapport-du-lundi/) | Chaque lundi à 7 h, un point d'une page sur la semaine : chiffre d'affaires, clients qui baissent, produits qui montent, factures en retard. | oui |

Chaque dossier contient le fichier à importer dans n8n et un `LISEZMOI.md` qui explique l'installation pas à pas.

## Ce qu'il vous faut

| Quoi | Pourquoi | Lien |
|---|---|---|
| n8n, en version Cloud ou installé sur votre serveur | faire tourner les automatisations | https://n8n.io |
| Un compte Google (Gmail et Google Sheets) | lire et écrire le tableau, envoyer les mails | |
| Une clé OpenRouter, pour les automatisations 1, 3 et 4 | l'IA qui lit les commandes et rédige les messages | https://openrouter.ai/keys |

## Démarrer en 5 minutes

1. Faites une copie du modèle de Google Sheet : https://docs.google.com/spreadsheets/d/14VMEcwF7slksXibpQYda5ddPOj7KVSCEKcpd9DB0iDY/copy
   (ou importez `modele/modele-erp-demo.xlsx` dans Google Sheets). Il contient tous les onglets utilisés par les quatre automatisations.
2. Dans n8n, créez un workflow, menu en haut à droite, **Import from File**, et choisissez le fichier `.json` de l'automatisation qui vous intéresse.
3. Ouvrez le nœud **Réglages** : collez l'ID de votre Google Sheet (la longue suite de caractères entre `/d/` et `/edit` dans son adresse) et remplissez vos informations.
4. Branchez vos comptes Google et votre clé OpenRouter dans les nœuds qui les demandent.
5. Suivez le `LISEZMOI.md` du dossier pour le premier test.

Pas besoin des quatre en même temps. Commencez par celle qui vous fait le plus mal : les matinées à recopier des commandes, la trésorerie tendue, les clients qui décrochent sans qu'on le voie, ou le pilotage à l'aveugle.

## Avant de brancher vos vrais clients

- Les fichiers ne contiennent aucun identifiant. Vos comptes se branchent dans votre n8n et restent chez vous.
- Les adresses mail du modèle sont fictives et ne reçoivent rien. Pour vos essais, mettez votre propre adresse sur quelques lignes.
- Les automatisations qui écrivent à vos clients sont prudentes : la 1 vous alerte au lieu de deviner, la 3 ne fait que des brouillons, la 2 n'envoie chaque relance qu'une fois.

## La vidéo

Tout est montré dans la vidéo, sur la chaîne YouTube Léni Ragaigne.

Vous voulez qu'on les installe chez vous, branchées sur votre ERP ou votre logiciel de facturation ? Il y a une heure offerte pour en parler : https://leniragaigne.com/reserver

## Licence

MIT. Utilisez, modifiez, partagez.
