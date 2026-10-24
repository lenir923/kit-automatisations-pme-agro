# Automatisation 1 : les commandes reçues par mail

## Ce que ça fait

L'automatisation surveille la boîte mail où arrivent vos commandes. Dès qu'un mail arrive, elle se déclenche (elle vérifie toutes les minutes).

D'abord, une IA rapide trie : est-ce que ce mail est une commande ? Une question sur une facture, une publicité ou une newsletter s'arrêtent là. Rien n'est touché.

Si c'est une commande, elle ouvre la pièce jointe. Un PDF (même scanné ou écrit à la main), un fichier Excel, ou simplement le texte du mail s'il n'y a pas de pièce jointe.

Ensuite, l'IA lit la commande comme le ferait une personne. Elle sort le client, chaque produit, chaque quantité en cartons et la date de livraison souhaitée. Elle rapproche chaque produit de votre catalogue, même quand le client écrit « galettes pur beurre » au lieu de votre référence, et elle reconnaît le client dans votre liste.

Puis elle vérifie. Si tout est bon, elle ajoute une ligne par produit dans l'onglet Commandes de votre tableau (numéro de commande, client, produit, quantité, prix, total, date de livraison, statut « À préparer »), et elle envoie au client un accusé de réception qui récapitule sa commande.

Si quelque chose cloche, elle ne devine pas. Un produit qui n'est pas au catalogue, un client inconnu, une quantité bizarre, une date déjà passée, un mail déjà traité ou un doute de l'IA : rien n'est ajouté au tableau, et vous recevez une alerte qui explique ce qui ne va pas. C'est une personne qui regarde.

## Ce qu'il vous faut

- n8n (la version Cloud ou une installation sur votre propre serveur).
- La boîte Gmail où arrivent vos commandes.
- Un Google Sheet avec trois onglets, en-têtes en première ligne :
  - **Produits** : `ref`, `produit`, `format`, `unites_par_carton`, `prix_ht_carton`. Votre catalogue.
  - **Clients** : `id_client`, `client`, `ville`, `contact` (prénom et nom), `email`. L'adresse mail sert à reconnaître le client et à lui répondre.
  - **Commandes** : `n_commande`, `date_reception`, `id_client`, `client`, `ref`, `produit`, `quantite_cartons`, `prix_ht_carton`, `total_ht`, `date_livraison_souhaitee`, `statut`, `id_mail`. Il peut être vide au départ.
- Une clé OpenRouter (openrouter.ai) pour l'IA. La lecture des commandes et le tri utilisent Claude Haiku, un modèle rapide et peu cher qui lit aussi les PDF scannés. Avec une clé Anthropic directe, il faut remplacer les deux nœuds IA par leurs équivalents Anthropic.

**Le plus simple pour un premier essai** : faites une copie du modèle de Google Sheet. Il a déjà tous les onglets, avec les données fictives d'une biscuiterie : https://docs.google.com/spreadsheets/d/14VMEcwF7slksXibpQYda5ddPOj7KVSCEKcpd9DB0iDY/copy (ou `modele/modele-erp-demo.xlsx` dans ce dépôt, à importer dans Google Sheets). Les adresses mail du modèle sont fictives et ne reçoivent rien : mettez la vôtre sur deux ou trois lignes pour voir les mails arriver.

## Installation, pas à pas

1. Dans n8n, créez un nouveau workflow, ouvrez le menu (les trois points en haut à droite) puis **Import from File** et choisissez `1-commandes-par-mail.json`.
2. Ouvrez le premier nœud, **Surveiller la boîte des commandes**. Dans le filtre de recherche, remplacez `commandes@votre-entreprise.fr` par l'adresse où arrivent vos commandes. Le filtre s'écrit comme dans la barre de recherche de Gmail : `to:commandes@votre-entreprise.fr`. Si vos commandes arrivent dans votre boîte personnelle, ce filtre est ce qui empêche l'automatisation de lire tout le reste.
3. Ouvrez le nœud **Réglages** et collez l'ID de votre Google Sheet à la place de `COLLEZ_ICI_L_ID_DE_VOTRE_GOOGLE_SHEET`. L'ID, c'est la longue suite de caractères dans l'adresse du Sheet, entre `/d/` et `/edit`. Mettez aussi votre nom d'entreprise, la signature des mails et l'adresse qui reçoit les alertes.
4. Branchez vos comptes. Les identifiants ont été retirés du fichier, il faut les choisir dans chaque nœud :
   - **Surveiller la boîte des commandes**, **Répondre au client** et **Alerte : à vérifier** : votre compte Gmail.
   - **Charger le catalogue**, **Charger les clients**, **Charger les commandes déjà saisies** et **Ajouter à l'ERP** : votre compte Google Sheets.
   - **IA rapide pour le tri** et **Lire la commande (IA)** : votre clé OpenRouter.
5. Pour vos premiers essais, protégez vos clients : mettez votre propre adresse comme email de deux ou trois clients de test dans l'onglet Clients. Les accusés de réception arriveront chez vous.
6. Envoyez-vous une commande (un PDF, un Excel, ou trois lignes dans le corps du mail) vers l'adresse surveillée. Dans n8n, cliquez sur **Execute workflow** : il prend le dernier mail reçu. Regardez le tableau se remplir et l'accusé de réception arriver.
7. Quand tout vous convient, remettez les vrais emails clients, et activez le workflow (bouton **Publish** ou **Active** en haut, selon votre version de n8n). Il tourne alors seul.

## Ce que vous pouvez adapter

Dans le nœud **Réglages** :

- `quantite_max_par_produit` : au-dessus, la quantité est jugée bizarre et part en alerte. 200 cartons par défaut.
- `premier_numero_commande` : le numéro de la première commande. Ensuite, le numéro suit le dernier de l'onglet Commandes.
- `modele_ia` : le modèle qui lit les commandes.
- `nom_entreprise`, `signature`, `adresse_alerte`.

Le texte de l'accusé de réception se change dans le nœud **Répondre au client**. Celui de l'alerte, dans **Alerte : à vérifier**. Ce que l'IA considère comme une commande se règle dans **Est-ce une commande ?** (la description des deux catégories). Les règles de lecture (unités, dates, doutes) sont écrites en clair dans **Lire la commande (IA)**, et les contrôles (produit inconnu, quantité, date, doublon) dans **Vérifier la commande**.

Si vous avez un vrai ERP, le nœud **Ajouter à l'ERP** peut être remplacé par l'outil de votre ERP (API, base de données, fichier d'import). Commencez par le tableau : c'est le plus simple pour valider que tout marche.

## Bon à savoir

- Le délai : n8n vérifie la boîte toutes les minutes, puis l'IA met 10 à 30 secondes à lire. Comptez une minute environ entre l'arrivée du mail et l'accusé de réception.
- Le client est reconnu par son adresse mail, ou à défaut par ce qui est écrit dans la commande (nom, ville, signature). L'accusé part toujours à l'adresse inscrite dans l'onglet Clients.
- Un même mail n'est jamais saisi deux fois : s'il est déjà dans le tableau, il part en alerte.

## Combien ça coûte

- L'IA : environ un dixième de centime par commande lue (un scan coûte un peu plus qu'un Excel). Le tri des mails qui ne sont pas des commandes coûte presque rien. Avec 30 commandes par jour, comptez 1 à 2 € par mois.
- Google Sheets et Gmail : rien de plus que ce que vous avez déjà.
- n8n : gratuit si vous l'installez sur votre serveur (un petit serveur coûte autour de 5 à 10 € par mois), ou l'abonnement n8n Cloud.
