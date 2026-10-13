# Copie HDF : journal des ajouts et recette

## Fichier de référence
`Focus_DR_HDF_Atlas_EnBas_v2.html`, tel qu'il est enregistré dans ce dossier. Il contient :
- les ajouts de Claude (ORT, monuments historiques et abords, loyers, zoom sur l'adresse, explication des droits de réservation, libellés QPV/commune dans le Portrait) ;
- les ajouts faits ensuite à la main : encadré « Comment lire cet indicateur » et guide des 6 indicateurs dans l'Atlas, nouvel encadré « Périmètre de chaque colonne » dans les droits de réservation.

Attention : `patch.py` reproduit seulement les ajouts de Claude à partir de la copie d'origine. Il ne contient pas les ajouts faits à la main. Pour toute nouvelle modification, il faut partir du fichier HTML de ce dossier, pas relancer `patch.py`.

## Recette du 13/10/2026 (version enregistrée dans ce dossier)
Version comparée à la copie d'origine, sur un navigateur Chromium automatisé :

| Contrôle | Copie d'origine | Version v2 |
| --- | --- | --- |
| Erreurs JavaScript (tous les onglets) | 0 | 0 |
| Atlas : communes dessinées | 667 | 667 |
| Atlas : 6 indicateurs, changement d'indicateur | oui | oui, et l'encadré explicatif change avec l'indicateur |
| Focus carto | s'affiche | s'affiche |
| Portrait : 11 thèmes, 8 lignes « Population et âges » | oui | oui, libellés « QPV » et « communes » ajoutés |
| Adresse : blocs de synthèse | 15 | 17 (ORT et patrimoine, loyers) |
| Adresse : sources affichées sous les blocs | 11 | 13 |
| Adresse : lignes de l'export CSV | 31 | 38 |
| Boutons Imprimer et CSV | présents | présents |
| Méthodes | 17 483 caractères | 18 314 (ajout des limites ORT, monuments, loyers, droits) |
| Archive des bases | | intègre (test unzip sans erreur) |

Adresse testée : Fère-en-Tardenois (commune en ORT, Petites villes de demain). Lille et Saint-Quentin ont aussi été testées pendant le développement.

## Contenu de `Sources_ajouts`
- `app_plus.js` : code ajouté (ORT, monuments, loyers).
- `patch.py` : script qui applique les ajouts sur la copie d'origine.
- `ort_hdf.json`, `mh_hdf.txt`, `loyers_prives_hdf_compact.txt`, `loyers_sociaux_rpls2024_HDF.csv` : données compactées intégrées dans le HTML. Les bases brutes sont dans `Bases_HDF_ORT_monuments_loyers.zip`.
