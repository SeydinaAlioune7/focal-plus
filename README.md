# 📘 README - Évolutions & Documentation Technique
## Projet : Focal-Plus (Observatoire QPV - Action Logement)
**Dépôt GitHub** : [https://github.com/SeydinaAlioune7/focal-plus](https://github.com/SeydinaAlioune7/focal-plus)  
**Déploiement Cloudflare** : [https://focal-plus.dfbc-pom.workers.dev/#atlas](https://focal-plus.dfbc-pom.workers.dev/#atlas)

---

## 🎯 1. Contexte & Objectif des Modifications

### Problématique initiale :
Dans la version précédente de l'onglet **Atlas des communes** :
* La fiche d'information du territoire sélectionné (*Métropole Européenne de Lille*, EPCI, etc.) était positionnée dans une colonne latérale à droite de la carte, contrainte à une largeur de 400px.
* Cette colonne comportait un ascenseur vertical (*scrollbar*) intérieur étriqué, obligeant l'utilisateur à scroller à la fois pour lire les indicateurs clés (KPIs) et pour consulter le tableau des communes avec QPV.

### But recherché :
1. **Sécuriser l'existant** : Ne pas toucher ni casser le travail initial de la région Hauts-de-France (`Focus_DR_HDF_COMPLET.html`).
2. **Repositionner en bas de la carte** : Déplacer la colonne d'informations sous la carte, sur toute la largeur, selon la même logique visuelle que l'onglet *Portrait d'un quartier*.
3. **Optimiser l'espace vertical (Gain d'espace)** : Introduire un système d'onglets horizontaux déroulants (*« boutons durs »*) permettant de basculer instantanément entre la synthèse des chiffres clés et le tableau détaillé des communes sans saturer la hauteur de page.

---

## 📂 2. Périmètre des Fichiers Concernés

| Fichier | Statut | Rôle |
|---|---|---|
| `Focus_DR_HDF_COMPLET.html` | **Non modifié (100% intact)** | Fichier d'origine conservé en sécurité dans `Downloads/qpv a jour/`. |
| `focal-plus/index.html` | **Modifié & Déployé** | Version modernisée avec disposition sous la carte et onglets horizontaux. |
| `Focus_DR_HDF_Atlas_EnBas.html` | **Créé (Copie locale)** | Copie de secours locale dans `Downloads/qpv a jour/`. |
| `focal-plus/wrangler.toml` | **Créé** | Configuration Cloudflare Workers pour le déploiement autonome du Worker `focal-plus`. |
| `focal-plus/.wranglerignore` | **Créé** | Exclusion des dossiers `.git` et `.wrangler` pour optimiser le téléversement. |

---

## 🛠️ 3. Détail Précis des Modifications par Niveau

### Niveau 1 : Hauteur du conteneur iframe parent
* **Emplacement** : `focal-plus/index.html` (CSS de la page principale).
* **Code modifié** :
  ```css
  /* Ancien style */
  #atlasFrame { width:100%; height:calc(100vh - 170px); min-height:680px; ... }

  /* Nouveau style */
  #atlasFrame { width:100%; height:calc(100vh - 110px); min-height:750px; ... }
  ```
* **But** : Élargir la fenêtre visible de l'Atlas de 60px vers le bas pour un confort de visualisation accru.

---

### Niveau 2 : Restructuration de la grille de l'Atlas (`atlasSrc`)
* **Emplacement** : `focal-plus/index.html` (Styles CSS internes du gabarit `atlasSrc`).
* **Code modifié** :
  ```css
  /* Ancienne grille 3 colonnes fixes */
  main {
    height: calc(100vh - 58px) !important;
    grid-template-columns: 280px minmax(0, 1fr) 400px !important;
  }

  /* Nouvelle disposition responsive */
  main {
    height: auto !important;
    min-height: calc(100vh - 58px);
    display: grid !important;
    grid-template-columns: 280px minmax(0, 1fr) !important;
    max-width: 1560px !important;
    margin: 0 auto !important;
  }
  #cat { height: 600px !important; overflow-y: auto !important; }
  #mapwrap { height: 600px !important; min-height: 500px !important; }
  .side {
    grid-column: 1 / -1 !important; /* Toute la largeur sous la carte */
    width: 100% !important;
    overflow: visible !important;
  }
  ```
* **But** : La carte et le filtre d'indicateurs se partagent le haut de l'écran, tandis que la fiche territoire `.side` se déploie majestueusement en dessous sur toute la largeur de l'écran.

---

### Niveau 3 : Barre d'onglets horizontaux & Vues déroulantes
* **Emplacement** : `focal-plus/index.html` (Structure HTML de `.card.side`).
* **Éléments créés** :
  1. En-tête flex avec le nom du territoire et la barre d'onglets :
     * Bouton 1 : `[ 📊 Synthèse & Indicateurs (KPIs) ]`
     * Bouton 2 : `[ 📋 Communes avec QPV (Tableau) ]`
     * Bouton 3 : `[ 👁️ Vue complète ]`
  2. Deux conteneurs distincts :
     * `#side-view-kpis` : englobant la grille `.kpis` des chiffres clés.
     * `#side-view-communes` : englobant le tableau `.tbl` des communes.
* **But** : 
  * Par défaut, seuls les KPIs sont affichés (hauteur compacte d'environ 180px).
  * L'utilisateur peut basculer sur le tableau ou afficher les deux en un clic, économisant l'espace vertical.

---

### Niveau 4 : JavaScript d'interaction & Activation des clics
* **Emplacement** : `focal-plus/index.html` (Balise `<script>` principale de `atlasSrc`).
* **Problème corrigé** :
  La première fonction de bascule avait été insérée dans un sous-bloc d'export qui s'interrompait prématurément hors du mode focus. Les boutons restaient alors inactifs (« boutons durs »).
* **Solution appliquée** :
  Placement de la fonction globale au tout début du script avec double rattachement événementiel :
  ```javascript
  window.switchSideView = function(v) {
    var vk = document.getElementById('side-view-kpis');
    var vc = document.getElementById('side-view-communes');
    var bk = document.getElementById('sbtn-kpis');
    var bc = document.getElementById('sbtn-communes');
    var ba = document.getElementById('sbtn-all');
    if (!vk || !vc) return;
    [bk, bc, ba].forEach(function(b){ if(b) b.classList.remove('on'); });
    if (v === 'kpis') {
      vk.style.display = 'block';
      vc.style.display = 'none';
      if(bk) bk.classList.add('on');
    } else if (v === 'communes') {
      vk.style.display = 'none';
      vc.style.display = 'block';
      if(bc) bc.classList.add('on');
    } else {
      vk.style.display = 'block';
      vk.style.display = 'block';
      if(ba) ba.classList.add('on');
    }
  };
  ```
* **But** : Réactivité instantanée au clic sur chaque bouton d'onglet.

---

### Niveau 5 : Ergonomie et Design « Boutons Durs » (Tactiles & Contrastés)
* **Emplacement** : `focal-plus/index.html` (CSS `.side-nav-btn` et `.side-tabs-nav`).
* **Styles appliqués** :
  * **Bouton inactif** : fond blanc `#FFFFFF`, bordure solide `1.5px solid #CBD5E1`, texte gris foncé contrasté `#334155`, ombre portée légère (`box-shadow: 0 1px 2px rgba(15,23,42,.06)`).
  * **Bouton actif** (`.on`) : fond bleu marine Action Logement `#1C3961`, texte blanc `#FFFFFF`, icône SVG blanche, bordure assortie et ombre de relief accentuée.
  * **Survol (Hover)** : élévation tactile `transform: translateY(-1px)` et curseur `pointer`.
* **But** : Offrir des boutons bien découpés, nets et palpables qui inspirent immédiatement le clic.

---

## 🚀 4. Déploiement & Vérification
1. Dépôt GitHub synchronisé : `main` branch à jour avec les fichiers `index.html`, `wrangler.toml`, `.wranglerignore` et `README.md`.
2. Déployé sur Cloudflare Workers : **`https://focal-plus.dfbc-pom.workers.dev/#atlas`**.
3. Aucune altération du projet historique Hauts-de-France.
