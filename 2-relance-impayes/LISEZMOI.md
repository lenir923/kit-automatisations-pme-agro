# Automatisation 2 : la relance des impayés

## Ce que ça fait

Chaque matin à 8 h, l'automatisation lit la liste de vos factures. Elle garde seulement celles qui ne sont pas payées et dont l'échéance est passée, puis elle calcule depuis combien de jours chacune est en retard.

Elle ne dit pas la même chose selon le retard :

- **Dès le premier jour** : un mail très poli. « Sauf erreur de notre part, la facture du 15 septembre est arrivée à échéance hier. Si le règlement est déjà parti, merci d'ignorer ce message. »
- **À partir de 8 jours** : un rappel plus direct, avec la facture en pièce jointe.
- **À partir de 15 jours** : un mail ferme, qui demande une date de paiement.
- **À partir de 30 jours** : plus de mail au client. Vous recevez une alerte : « Ces clients ne répondent pas aux relances, il faut les appeler », avec le montant, la facture et le numéro de téléphone de chacun.

Chaque relance n'est envoyée qu'une fois. L'automatisation note dans le tableau le niveau de relance déjà envoyé et la date. Le lendemain, elle ne renvoie rien tant que la facture n'a pas changé de palier.

Dès que la facture est marquée payée dans le tableau, tout s'arrête pour elle.

## Ce qu'il vous faut

- n8n (la version Cloud ou une installation sur votre propre serveur).
- La boîte Gmail de la comptabilité.
- Un Google Sheet avec un onglet **Factures**, en-têtes en première ligne : `n_facture`, `id_client`, `client`, `email`, `telephone`, `date_facture`, `date_echeance`, `montant_ttc`, `payee` (OUI ou NON), `date_paiement`, `niveau_relance`, `date_derniere_relance`. Votre logiciel de compta sait exporter cette liste. Les deux dernières colonnes peuvent être vides au départ : c'est l'automatisation qui les remplit.
- Les dates au format `2026-10-23` dans le Sheet (format de date AAAA-MM-JJ).

Pas besoin d'IA pour celle-ci : les mails sont des modèles, il n'y a rien à inventer.

**Le plus simple pour un premier essai** : faites une copie du modèle de Google Sheet. Il a déjà tous les onglets, avec les données fictives d'une biscuiterie : https://docs.google.com/spreadsheets/d/14VMEcwF7slksXibpQYda5ddPOj7KVSCEKcpd9DB0iDY/copy (ou `modele/modele-erp-demo.xlsx` dans ce dépôt, à importer dans Google Sheets). Les adresses mail du modèle sont fictives et ne reçoivent rien : mettez la vôtre sur deux ou trois lignes pour voir les mails arriver.

## Installation, pas à pas

1. Dans n8n, créez un nouveau workflow, ouvrez le menu (les trois points en haut à droite) puis **Import from File** et choisissez `2-relance-impayes.json`.
2. Ouvrez le nœud **Réglages** et collez l'ID de votre Google Sheet à la place de `COLLEZ_ICI_L_ID_DE_VOTRE_GOOGLE_SHEET`. L'ID, c'est la longue suite de caractères dans l'adresse du Sheet, entre `/d/` et `/edit`. Mettez aussi le nom et l'adresse de votre entreprise (ils apparaissent sur la facture jointe), la signature et l'adresse du dirigeant qui reçoit l'alerte « à appeler ».
3. Branchez vos comptes. Les identifiants ont été retirés du fichier, il faut les choisir dans chaque nœud :
   - **Lire les factures** et **Noter la relance dans le tableau** : votre compte Google Sheets.
   - **Mail poli**, **Rappel direct + facture**, **Mail ferme** et **Alerte au dirigeant : à appeler** : votre compte Gmail.
4. Pour vos premiers essais, protégez vos clients : mettez votre propre adresse dans la colonne `email` de quelques factures de test. Les relances arriveront chez vous.
5. Cliquez sur le bouton de lecture du nœud **Lancer à la main (démo)**. Regardez la sortie de **Garder les factures en retard** puis vos mails. Dans le tableau, `niveau_relance` est passé à 1, 2, 3 ou 4.
6. Quand tout vous convient, remettez les vrais emails clients, et activez le workflow (bouton **Publish** ou **Active** en haut, selon votre version de n8n). Il tourne alors seul chaque matin à 8 h.

## Ce que vous pouvez adapter

Dans le nœud **Réglages** :

- `jours_rappel_direct` (8), `jours_mail_ferme` (15), `jours_appel` (30) : les paliers, en jours de retard.
- `nom_entreprise`, `adresse_entreprise`, `signature`, `adresse_dirigeant`.

Le texte de chaque mail se change directement dans son nœud : **Mail poli**, **Rappel direct + facture**, **Mail ferme**, **Alerte au dirigeant : à appeler**. Changez-les pour qu'ils vous ressemblent. L'heure de passage se règle dans **Chaque matin à 8 h**.

La facture jointe au rappel est fabriquée à partir du tableau par le nœud **Joindre la facture PDF**, sur un modèle très simple. Chez vous, le mieux est de joindre le vrai PDF de votre logiciel de facturation : par exemple le chercher dans Google Drive par son numéro de facture, à la place de ce nœud.

Une relance qui a déjà été envoyée ne repart pas. Pour renvoyer un niveau, remettez `niveau_relance` à la valeur d'avant dans le tableau.

## Combien ça coûte

- Pas d'IA, donc aucun coût par relance.
- Google Sheets et Gmail : rien de plus que ce que vous avez déjà.
- n8n : gratuit si vous l'installez sur votre serveur (un petit serveur coûte autour de 5 à 10 € par mois), ou l'abonnement n8n Cloud.
