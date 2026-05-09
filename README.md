# mlcv-templates

Templates LaTeX pour [ml-cv](https://github.com/ibrahima-drm/cv-mali). Compilés à la demande par le `mlcv-pdf-service` qui clone ce repo au build.

Pour ajouter un template : push un nouveau dossier dans `templates/`. Le service est redéployé sur Railway et le nouveau template apparaît dans la galerie de ml-cv.

## Structure

```
templates/
└── <template-id>/
    ├── manifest.json       # métadonnées (id, name, category, engine, supportsPhoto…)
    ├── template.tex        # source LaTeX avec placeholders Mustache (<<…>>)
    ├── preview.png         # capture A4 (794×1123 px) affichée dans la galerie
    ├── (optionnel) .cls    # classe LaTeX custom du template
    └── (optionnel) assets  # polices, images, fichiers .sty additionnels
```

## Convention placeholders Mustache

Délimiteurs `<<` et `>>` (pour ne pas conflicter avec les accolades LaTeX).

**Variable :**
```latex
\name{<<basics.name>>}
```

**Section conditionnelle :**
```latex
<<#basics.summary>>
\section{Profil}
<<basics.summary>>
<</basics.summary>>
```

**Boucle sur tableau :**
```latex
<<#work>>
\cventry{<<startDate>> -- <<endDate>>}{<<position>>}{<<name>>}{<<location>>}{}{<<summary>>}
<</work>>
```

**Boucle imbriquée (highlights dans work) :**
```latex
<<#work>>
\cventry{...}{<<position>>}{...}
\begin{itemize}
<<#highlights>>\item <<.>>
<</highlights>>
\end{itemize}
<</work>>
```

`<<.>>` réfère à l'élément courant lorsqu'on itère sur un tableau de strings.

## Échappement automatique

Toutes les valeurs venant de l'utilisateur sont **automatiquement échappées** pour LaTeX par le service (`&` → `\&`, `%` → `\%`, etc.). Tu n'as PAS à échapper toi-même.

## `manifest.json`

```json
{
  "id": "mon-template",
  "name": "Mon Template",
  "category": "modern",
  "description": "Description courte qui apparaît dans la galerie.",
  "engine": "tectonic",
  "supportsPhoto": true,
  "supportsLevels": true,
  "author": "Auteur original (port ml-cv)",
  "license": "MIT"
}
```

Catégories valides : `modern`, `classic`, `minimal`, `creative`, `academic`.

## Format des données reçues

Format **JSON Resume** étendu :

```json
{
  "basics": {
    "name": "Aminata Keïta",
    "label": "Cheffe de projet",
    "email": "...",
    "phone": "...",
    "image": "data:image/jpeg;base64,...",
    "location": { "city": "Bamako", "countryCode": "ML" },
    "profiles": [{ "network": "LinkedIn", "url": "...", "username": "..." }],
    "summary": "..."
  },
  "work": [
    { "name", "position", "location", "startDate", "endDate", "summary", "highlights": [...] }
  ],
  "education": [
    { "institution", "studyType", "area", "startDate", "endDate" }
  ],
  "skills": [
    { "name", "keywords": [...], "levelNum": 1-5 }
  ],
  "languages": [
    { "language", "fluency" }
  ]
}
```

## Photo utilisateur

Quand `basics.image` est une data URL, le service l'extrait et l'écrit dans le dossier de compilation comme `photo.jpg` (ou `.png`). Les templates l'incluent via :
```latex
<<#basics.image>>\includegraphics{photo}<</basics.image>>
```

## Test local

```bash
# Dans le repo cv-mali
cd pdf-service
npm run dev

# Dans un autre terminal
.\test-compile.ps1 simple-classic
.\test-compile.ps1 altacv-classic
```

## Templates inclus

- **simple-classic** — ATS-friendly, une colonne, marges généreuses. Pour fonction publique, banque, ONG traditionnelles.
- **altacv-classic** — Port d'AltaCV (LianTze Lim). Deux colonnes, sérif Roboto Slab, accents bordeaux. Premium.

À venir : ModernCV, McDowell, Awesome-CV, Hipster, latexcv (modern/sidebar/rows), YAAC.

## Licence

Le code de templating dans ce repo est sous **MIT**. Les classes LaTeX sous-jacentes (altacv.cls, etc.) gardent leurs licences d'origine — voir le `manifest.json` de chaque template (`license`) et les fichiers LICENSE qu'elles incluent.
