# Automatisation 3 : la relance des clients dormants

## Ce que ça fait

Chaque lundi matin, l'automatisation lit l'historique de vos ventes. Pour chaque client, elle calcule son rythme habituel : il commande en moyenne tous les combien de jours. Puis elle regarde depuis quand il n'a rien commandé.

Si un client n'a rien commandé depuis plus de deux fois son rythme habituel, il passe en alerte. Un magasin qui commande tous les 14 jours et qui n'a rien commandé depuis 30 jours, par exemple.

Pour chaque client en alerte, l'IA écrit un petit mail personnalisé : elle cite les produits qu'il commande d'habitude, présente votre nouveauté et propose de lui mettre de côté sa commande habituelle. Le mail arrive en brouillon dans Gmail. Rien n'est envoyé : votre commercial relit, ajuste, envoie. Ou il décroche son téléphone.

Les clients qui ont moins de 3 commandes dans l'historique sont ignorés : pas assez de recul pour parler de rythme.

## Ce qu'il vous faut

- n8n (la version Cloud ou une installation sur votre propre serveur).
- Un Google Sheet avec deux onglets :
  - **Ventes** : une ligne par produit commandé, avec au minimum les colonnes `date`, `id_client`, `produit`, `quantite_cartons`, `ca_ht`. C'est l'export des ventes de votre ERP ou de votre logiciel de facturation.
  - **Clients** : `id_client`, `client`, `type`, `ville`, `contact` (prénom et nom), `email`.
- Un compte Gmail (celui du commercial, c'est là qu'arrivent les brouillons).
- Une clé OpenRouter (openrouter.ai) pour l'IA. Le modèle utilisé est Claude Haiku, rapide et peu cher.

Les dates peuvent être au format `2026-10-19` ou `19/10/2026`, les montants avec ou sans le signe €.

**Le plus simple pour un premier essai** : faites une copie du modèle de Google Sheet. Il a déjà tous les onglets, avec les données fictives d'une biscuiterie : https://docs.google.com/spreadsheets/d/14VMEcwF7slksXibpQYda5ddPOj7KVSCEKcpd9DB0iDY/copy (ou `modele/modele-erp-demo.xlsx` dans ce dépôt, à importer dans Google Sheets). Les adresses mail du modèle sont fictives et ne reçoivent rien : mettez la vôtre sur deux ou trois lignes pour voir les mails arriver.

## Installation, pas à pas

1. Dans n8n, créez un nouveau workflow, ouvrez le menu (les trois points en haut à droite) puis **Import from File** et choisissez `3-clients-dormants.json`.
2. Ouvrez le nœud **Réglages** et collez l'ID de votre Google Sheet à la place de `COLLEZ_ICI_L_ID_DE_VOTRE_GOOGLE_SHEET`. L'ID, c'est la longue suite de caractères dans l'adresse du Sheet, entre `/d/` et `/edit`. Remplacez aussi `Votre prénom et nom` et `Votre entreprise` : c'est la signature des mails.
3. Branchez vos comptes. Les identifiants ont été retirés du fichier, il faut les choisir dans chaque nœud :
   - **Lire l'historique des ventes** et **Lire les clients** : votre compte Google Sheets.
   - **Claude (OpenRouter)** : votre clé OpenRouter.
   - **Créer le brouillon dans Gmail** : votre compte Gmail.
4. Rien n'est jamais envoyé : l'automatisation crée seulement des brouillons. Vous pouvez donc tester avec vos vrais clients, et supprimer les brouillons ensuite.
5. Cliquez sur **Execute workflow** (ou sur le bouton de lecture du nœud **Lancer à la main**). Regardez la sortie du nœud **Garder les clients en alerte** : c'est votre liste de clients dormants. Puis ouvrez vos brouillons Gmail.
6. Quand tout est bon, activez le workflow (interrupteur en haut). Il tournera seul chaque lundi à 8 h.

## Ce que vous pouvez adapter

Tout se change dans le nœud **Réglages** :

- `seuil_alerte` : 2 par défaut, soit deux fois le rythme habituel. Mettez 1.5 pour être prévenu plus tôt, 3 pour ne voir que les cas graves.
- `nom_commercial` et `nom_entreprise` : la signature des mails.
- `nouveaute` : le produit à mettre en avant dans la relance.
- `date_de_reference` : laissez vide (on calcule par rapport à aujourd'hui). Utile seulement pour tester sur un vieil export, au format `2026-10-23`. Avec le modèle de démonstration, mettez `2026-10-23` : ses ventes s'arrêtent à cette date.

Le ton du mail se règle dans le nœud **Écrire le message** (le texte « Règles »). L'heure et le jour de passage, dans le nœud **Chaque lundi à 8 h**.

Si vos colonnes ne portent pas les mêmes noms, le plus simple est de renommer les en-têtes de votre Sheet. Sinon, il faut adapter les noms de colonnes dans le code du nœud **Calculer le rythme de chaque client** (ils sont écrits en clair : `vente.date`, `vente.produit`...).

## Combien ça coûte

- L'IA : moins d'un centime pour toute une semaine de relances. Quelques centimes par an.
- Google Sheets et Gmail : rien de plus que ce que vous avez déjà.
- n8n : gratuit si vous l'installez sur votre serveur (un petit serveur coûte autour de 5 à 10 € par mois), ou l'abonnement n8n Cloud.
