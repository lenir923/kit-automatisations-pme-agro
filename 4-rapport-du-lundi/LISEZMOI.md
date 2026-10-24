# Automatisation 4 : le rapport du lundi matin

## Ce que ça fait

Chaque lundi à 7 heures, l'automatisation lit vos ventes, vos factures et vos commandes. Elle calcule :

- le chiffre d'affaires de la semaine écoulée, comparé à la même semaine l'an dernier, et le cumul depuis janvier ;
- les clients qui baissent et ceux qui montent (les 4 dernières semaines comparées aux mêmes 4 semaines l'an dernier) ;
- les produits qui montent et ceux qui partent le mieux ;
- les factures en retard, les clients dormants et les commandes reçues dans la semaine.

Tous les chiffres sont calculés par le workflow, au centime près. L'IA ne fait que rédiger : un point d'une page, en français, comme un collaborateur qui vous ferait le point. Les 3 clients qui baissent avec une action conseillée, les 3 produits qui montent, et une phrase « à regarder cette semaine ». Le mail arrive mis en page, avec les quatre chiffres clés en haut.

La « semaine écoulée », ce sont les 7 derniers jours présents dans votre export de ventes. La comparaison avec l'an dernier se fait à 52 semaines pile, pour comparer un lundi avec un lundi.

## Ce qu'il vous faut

- n8n (la version Cloud ou une installation sur votre propre serveur).
- Un Google Sheet avec trois onglets :
  - **Ventes** : une ligne par produit commandé, avec au minimum `date`, `client`, `type_client`, `produit`, `ca_ht`. C'est l'export des ventes de votre ERP.
  - **Factures** : `n_facture`, `client`, `date_echeance`, `montant_ttc`, `payee` (OUI ou NON).
  - **Commandes** : `n_commande`, `date_reception`. Il peut rester vide : c'est l'onglet que remplit l'automatisation 1 (les commandes reçues par mail).
- Un compte Gmail pour envoyer le rapport.
- Une clé OpenRouter (openrouter.ai) pour l'IA. Le modèle utilisé est Claude Sonnet : il rédige mieux, et un rapport par semaine ne coûte presque rien.

Les dates peuvent être au format `2026-10-19` ou `19/10/2026`, les montants avec ou sans le signe €.

**Le plus simple pour un premier essai** : faites une copie du modèle de Google Sheet. Il a déjà tous les onglets, avec les données fictives d'une biscuiterie : https://docs.google.com/spreadsheets/d/14VMEcwF7slksXibpQYda5ddPOj7KVSCEKcpd9DB0iDY/copy (ou `modele/modele-erp-demo.xlsx` dans ce dépôt, à importer dans Google Sheets). Les adresses mail du modèle sont fictives et ne reçoivent rien : mettez la vôtre sur deux ou trois lignes pour voir les mails arriver.

## Installation, pas à pas

1. Dans n8n, créez un nouveau workflow, ouvrez le menu (les trois points en haut à droite) puis **Import from File** et choisissez `4-rapport-du-lundi.json`.
2. Ouvrez le nœud **Réglages** :
   - collez l'ID de votre Google Sheet à la place de `COLLEZ_ICI_L_ID_DE_VOTRE_GOOGLE_SHEET` (la longue suite de caractères dans l'adresse du Sheet, entre `/d/` et `/edit`) ;
   - remplacez `dirigeant@votre-entreprise.fr` par l'adresse qui doit recevoir le rapport, `Votre prénom` par votre prénom et `Votre entreprise` par le nom de votre entreprise.
3. Branchez vos comptes. Les identifiants ont été retirés du fichier, il faut les choisir dans chaque nœud :
   - **Lire les ventes**, **Lire les factures**, **Lire les commandes** : votre compte Google Sheets.
   - **Claude (OpenRouter)** : votre clé OpenRouter.
   - **Envoyer le rapport au dirigeant** : votre compte Gmail.
4. Pour vos essais, mettez votre propre adresse dans `email_dirigeant` (nœud **Réglages**).
5. Cliquez sur **Execute workflow** (ou sur le bouton de lecture du nœud **Lancer à la main**) et ouvrez votre boîte mail.
6. Quand tout est bon, activez le workflow (interrupteur en haut). Il tournera seul chaque lundi à 7 h.

## Ce que vous pouvez adapter

- Dans **Réglages** : le nom de l'entreprise, le seuil des clients dormants (2 = rien commandé depuis deux fois le rythme habituel), et `date_de_reference` (laissez vide ; utile seulement pour tester sur un vieil export, au format `2026-10-23` ; avec le modèle de démonstration, mettez `2026-10-23`, ses ventes s'arrêtent à cette date).
- Le ton et le plan du rapport : le texte du nœud **Écrire le rapport**.
- Les couleurs et la mise en page du mail : en haut du nœud **Mettre en page le mail**.
- Le jour et l'heure : le nœud **Chaque lundi à 7 h**.

Si vos colonnes ne portent pas les mêmes noms, le plus simple est de renommer les en-têtes de votre Sheet. Sinon, il faut adapter les noms dans le code du nœud **Calculer les chiffres de la semaine** (ils sont écrits en clair : `v.date`, `v.ca_ht`...).

## Combien ça coûte

- L'IA : un à deux centimes par rapport, moins d'un euro par an.
- Google Sheets et Gmail : rien de plus que ce que vous avez déjà.
- n8n : gratuit si vous l'installez sur votre serveur (un petit serveur coûte autour de 5 à 10 € par mois), ou l'abonnement n8n Cloud.
