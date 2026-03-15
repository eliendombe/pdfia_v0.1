# 📚 Documentation Générale - PDF Insight AI

## 📖 Table des Matières
1. [Présentation](#présentation)
2. [Architecture](#architecture)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Utilisation](#utilisation)
6. [Structure du Projet](#structure-du-projet)
7. [API Référence](#api-référence)
8. [Dépannage](#dépannage)

---

## 📋 Présentation

**PDF Insight AI** est une application web moderne qui permet d'analyser des documents PDF de manière intelligente en utilisant l'IA. L'application offre une interface utilisateur intuitive pour :

- ✅ Charger des fichiers PDF (jusqu'à 50 MB)
- ✅ Poser des questions sur le contenu des documents
- ✅ Obtenir des réponses précises basées sur le contenu
- ✅ Analyser plusieurs questions successivement
- ✅ Fonctionnement sécurisé et privé

### Caractéristiques Clés

| Feature | Description |
|---------|-------------|
| **Extraction PDF** | Extraction complète du texte depuis fichiers PDF |
| **IA Avancée** | Utilise le modèle GPT-4o-mini via OpenRouter |
| **Interface Moderne** | Design réactif et animations fluides |
| **Drag & Drop** | Chargement facile des fichiers |
| **Support Clavier** | Raccourc : Entrée pour envoyer |
| **CORS Enabled** | Communication frontend-backend sécurisée |

---

## 🏗️ Architecture

### Vue d'ensemble

```
┌─────────────────────────────────────────────────────────────┐
│                       Frontend (Frontend)                    │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  index.html (Interface utilisateur)                  │   │
│  │  script.js  (Logique de lecture PDF & requêtes API) │   │
│  │  styles.css (Design & animations)                   │   │
│  └──────────────────────────────────────────────────────┘   │
│                           ↕️                                  │
│                     Requêtes/Réponses                        │
│                           ↕️                                  │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              Backend (FastAPI)                       │   │
│  │  ┌──────────────────────────────────────────────┐   │   │
│  │  │  main.py - Serveur API                       │   │   │
│  │  │  - GET /health                               │   │   │
│  │  │  - POST /api/query                           │   │   │
│  │  │  - POST /api/batch-query                     │   │   │
│  │  │  - Intégration OpenRouter                    │   │   │
│  │  └──────────────────────────────────────────────┘   │   │
│  └──────────────────────────────────────────────────────┘   │
│                           ↕️                                  │
│                 Appels API OpenRouter                        │
│                           ↕️                                  │
│  ┌──────────────────────────────────────────────────────┐   │
│  │         Service IA Externe (OpenRouter)              │   │
│  │  Modèle: openai/gpt-4o-mini                         │   │
│  │  - Analyse du contenu                              │   │
│  │  - Réponses intelligentes                          │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### Stack Technologique

**Frontend:**
- HTML5 sémantique
- CSS3 avec variables CSS personnalisées
- JavaScript vanilla (ES6+)
- PDF.js (extraction texte PDF)
- Font Awesome 6.5.1 (icônes)

**Backend:**
- Python 3.8+
- FastAPI (framework web moderne)
- Uvicorn (serveur ASGI)
- Pydantic (validation données)
- Requests (appels HTTP)
- Python-dotenv (variables d'environnement)

**Services Externes:**
- OpenRouter API (accès aux modèles LLM)
- GPT-4o-mini (modèle IA utilisé)

---

## 🚀 Installation

### Prérequis

- Python 3.8 ou supérieur
- pip (gestionnaire de paquets Python)
- Navigateur web moderne (Chrome, Firefox, Safari, Edge)
- Clé API OpenRouter (obtenue sur https://openrouter.ai)

### Étapes d'Installation

#### 1. Cloner ou télécharger le projet

```bash
# Naviguer vers le répertoire du projet
cd pdfia
```

#### 2. Configuration du Backend

```bash
# Naviguer dans le répertoire api
cd api

# Créer un environnement virtuel (recommandé)
python -m venv venv

# Activer l'environnement virtuel
# Sur Windows:
venv\Scripts\activate
# Sur macOS/Linux:
source venv/bin/activate

# Installer les dépendances
pip install -r requirements.txt
```

#### 3. Configuration des Variables d'Environnement

Créer un fichier `.env` dans le répertoire `api/` :

```bash
# api/.env
OPENROUTER_API_KEY=votre_clé_api_ici
```

**Comment obtenir une clé API OpenRouter:**
1. Aller sur https://openrouter.ai
2. Créer un compte et se connecter
3. Accéder à la page des clés API
4. Générer une nouvelle clé
5. Copier-la dans le fichier `.env`

#### 4. Lancer le serveur Backend

```bash
# Depuis le répertoire api/
python -m uvicorn main:app --reload --host 127.0.0.1 --port 8000
```

Vous devriez voir :
```
INFO:     Uvicorn running on http://127.0.0.1:8000
INFO:     Application startup complete
```

#### 5. Ouvrir l'Application Frontend

Ouvrir `index.html` dans un navigateur web :
- Double-cliquer sur le fichier, ou
- Utiliser un serveur local :
  ```bash
  # Depuis le répertoire racine pdfia/
  python -m http.server 3000
  ```
  Puis ouvrir http://localhost:3000

---

## ⚙️ Configuration

### Variables d'Environnement Backend

| Variable | Description | Exemple |
|----------|-------------|---------|
| `OPENROUTER_API_KEY` | Clé API OpenRouter | `sk-...` |

### Configuration Frontend

Dans `script.js`, modifiez ces variables globales selon vos besoins :

```javascript
// Configuration de l'API
const API_BASE_URL = "http://127.0.0.1:8000";  // URL du serveur backend
const USE_AI_API = true;  // true = API IA, false = recherche locale
```

### Configuration du Modèle IA

Dans `api/main.py`, modifiez le modèle utilisé (ligne ~120) :

```python
payload = {
    "model": "openai/gpt-4o-mini",  # Changer le modèle ici
    # ...
}
```

**Modèles recommandés:**
- `openai/gpt-4o-mini` (rapide, économique) - **Défaut**
- `openai/gpt-4` (plus puissant, plus cher)
- `anthropic/claude-3-haiku` (alternative)

---

## 📱 Utilisation

### Interface Utilisateur

#### Étape 1 : Charger un PDF

```
┌─────────────────────────────────────────┐
│  Zone de Chargement                    │
│  [Glissez-déposez ou cliquez]          │
│  ☁️ Charger votre document PDF         │
└─────────────────────────────────────────┘
```

Options :
- **Glisser-déposer** : Draggez un fichier PDF dans la zone
- **Cliquer** : Cliquez sur le bouton pour parcourir

Limitations :
- Format : PDF uniquement
- Taille maximale : 50 MB

#### Étape 2 : Poser une Question

```
┌─────────────────────────────────────┐
│ Posez une question sur le document │
│ [_________________________] [Analyser]│
└─────────────────────────────────────┘
```

Conseils :
- Soyez précis dans votre question
- Utilisez des mots clés du document
- Maximum 500 caractères

#### Étape 3 : Voir la Réponse

```
┌─────────────────────────────────────┐
│ Q: Qu'est-ce que... ?              │
│                                    │
│ 🤖 Réponse IA (gpt-4o-mini):      │
│ [Réponse détaillée...]            │
└─────────────────────────────────────┘
```

### Raccourcis Clavier

| Touche | Action |
|--------|--------|
| **Entrée** | Envoyer la question |
| **Maj+Entrée** | Nouvelle ligne (dans l'input) |

### Modes d'Utilisation

#### Mode IA (Recommandé)
- Nécessite une clé API OpenRouter
- Réponses intelligentes et précises
- Comprend le contexte
- Configuration : `USE_AI_API = true`

#### Mode Recherche Locale (Fallback)
- Pas besoin de clé API
- Recherche par mots clés basique
- Moins précis mais rapide
- Configuration : `USE_AI_API = false`

---

## 📁 Structure du Projet

```
pdfia/
│
├── 📄 index.html              # Interface utilisateur (HTML5)
├── 🎨 styles.css              # Feuille de style (CSS3)
├── 📜 script.js               # Logique frontend (JavaScript)
├── 📋 README.md               # Présentation courte
├── 📚 DOCUMENTATION.md        # Ce fichier (documentation complète)
│
└── api/                        # Répertoire Backend
    ├── 🐍 main.py             # Serveur FastAPI principal
    ├── 📦 requirements.txt     # Dépendances Python
    ├── 🔑 .env                # Variables d'environnement (à créer)
    ├── 📚 README.md           # Documentation API
    ├── 🗂️ __pycache__/        # Cache Python (auto-généré)
    │
    └── doc/                    # Documentation supplémentaire
        ├── 🚀 QUICK_START.md   # Démarrage rapide
        ├── ⚙️ README_SETUP.md  # Guide de configuration détaillé
        └── 📝 d.txt            # Notes diverses
```

### Fichiers Clés

#### `index.html`
- Structure HTML5 sémantique
- Meta tags pour sécurité (CSP)
- Responsive design
- Intégration Font Awesome et PDF.js

#### `styles.css`
- Variables CSS personnalisées
- Design responsive
- Animations et transitions fluides
- Thème coloré avec gradients
- 1000+ lignes optimisées

#### `script.js`
- Configuration de PDF.js
- Gestion des événements DOM
- Extraction de texte PDF
- Communication API
- Recherche locale comme fallback
- 500+ lignes documentées

#### `api/main.py`
- Serveur FastAPI
- 3 endpoints principaux
- Intégration OpenRouter
- Gestion d'erreurs robuste
- Logging détaillé
- 200+ lignes

#### `api/requirements.txt`
```
fastapi==0.104.1          # Framework web
uvicorn==0.24.0           # Serveur ASGI
requests==2.31.0          # Client HTTP
python-dotenv==1.0.0      # Gestion .env
pydantic==2.5.0          # Validation données
```

---

## 🔌 API Référence

### Base URL
```
http://127.0.0.1:8000
```

### Endpoints

#### 1. Health Check
```http
GET /health
```

**Description:** Vérifier l'état du serveur

**Réponse (200 OK):**
```json
{
    "status": "ok",
    "service": "PDF Insight AI"
}
```

**Exemple:**
```bash
curl http://127.0.0.1:8000/health
```

---

#### 2. Query (Requête Simple)
```http
POST /api/query
```

**Description:** Analyser une question sur un contenu PDF

**Headers:**
```
Content-Type: application/json
```

**Body (Request):**
```json
{
    "pdf_text": "Contenu complet du PDF en texte...",
    "question": "Votre question ici"
}
```

**Réponse (200 OK):**
```json
{
    "answer": "Réponse détaillée basée sur le PDF...",
    "model": "openai/gpt-4o-mini",
    "usage": {
        "prompt_tokens": 150,
        "completion_tokens": 100,
        "total_tokens": 250
    }
}
```

**Erreurs Possibles:**

| Code | Description | Solution |
|------|-------------|----------|
| 400 | Données manquantes | Vérifier `pdf_text` et `question` |
| 500 | Clé API manquante | Configurer `OPENROUTER_API_KEY` |
| 500 | Erreur OpenRouter | Vérifier clé API, quota |
| 504 | Timeout API | Réessayer, contenu peut être trop long |

**Exemple cURL:**
```bash
curl -X POST http://127.0.0.1:8000/api/query \
  -H "Content-Type: application/json" \
  -d '{
    "pdf_text": "Lorem ipsum dolor sit amet...",
    "question": "De quoi parle ce texte?"
  }'
```

**Exemple JavaScript:**
```javascript
const response = await fetch('http://127.0.0.1:8000/api/query', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        pdf_text: pdfContent,
        question: userQuestion
    })
});

const data = await response.json();
console.log(data.answer);
```

---

#### 3. Batch Query (Requêtes Multiples)
```http
POST /api/batch-query
```

**Description:** Analyser plusieurs questions sur un PDF en une seule requête

**Body (Request):**
```json
{
    "pdf_text": "Contenu du PDF...",
    "questions": [
        "Question 1?",
        "Question 2?",
        "Question 3?"
    ]
}
```

**Réponse (200 OK):**
```json
{
    "results": [
        {
            "question": "Question 1?",
            "answer": "Réponse 1..."
        },
        {
            "question": "Question 2?",
            "answer": "Réponse 2..."
        }
    ]
}
```

---

### Modèles de Données (Pydantic)

#### QueryRequest
```python
class QueryRequest(BaseModel):
    pdf_text: str      # Contenu textuel du PDF
    question: str      # Question de l'utilisateur
```

#### QueryResponse
```python
class QueryResponse(BaseModel):
    answer: str        # Réponse de l'IA
    model: str         # Modèle utilisé
    usage: dict        # Tokens utilisés
```

#### BatchQueryRequest
```python
class BatchQueryRequest(BaseModel):
    pdf_text: str           # Contenu du PDF
    questions: list[str]    # Liste de questions
```

---

## 🐛 Dépannage

### Problème : "Impossible de contacter l'API"

**Causes possibles:**
- Le serveur FastAPI n'est pas lancé
- Mauvaise URL API dans `script.js`
- CORS non configuré

**Solutions:**
1. Vérifier que le serveur tourne :
   ```bash
   python -m uvicorn api.main:app --reload
   ```
2. Vérifier l'URL dans `script.js` :
   ```javascript
   const API_BASE_URL = "http://127.0.0.1:8000";
   ```
3. Vérifier que CORS est activé dans `main.py`

---

### Problème : "Clé API non configurée"

**Cause:** Variable `OPENROUTER_API_KEY` manquante

**Solution:**
1. Créer le fichier `api/.env` :
   ```
   OPENROUTER_API_KEY=sk-votre_clé_ici
   ```
2. S'assurer que le fichier n'est **pas** dans `.gitignore`
3. Redémarrer le serveur

---

### Problème : "Le PDF ne se charge pas"

**Causes possibles:**
- Format invalide (pas du PDF)
- Fichier trop volumineux (>50 MB)
- Erreur dans PDF.js

**Solutions:**
1. Vérifier le fichier PDF :
   ```bash
   file votrefichier.pdf  # Vérifier le format
   ls -lh votrefichier.pdf  # Vérifier la taille
   ```
2. Essayer avec un autre PDF
3. Vérifier la console du navigateur pour les erreurs
4. Vérifier que PDF.js est chargé :
   ```javascript
   console.log(typeof pdfjsLib);  // Devrait être 'object'
   ```

---

### Problème : "Erreur lors de l'extraction du texte"

**Causes:**
- PDF scanné (pas de texte extrait)
- PDF protégé par mot de passe
- Encodage non standard

**Solutions:**
1. Essayer OCR sur le PDF scanné
2. Déverrouiller le PDF si protégé
3. Convertir en PDF texte

---

### Problème : "Réponses vides ou non-pertinentes"

**Causes:**
- Contenu du PDF très long (tronqué automatiquement)
- Question trop vague
- Modèle non approprié

**Solutions:**
1. Poser une question plus précise
2. Utiliser des mots clés du document
3. Essayer un autre modèle (en modifier dans `main.py`)
4. Vérifier la configuration `temperature` (plus bas = plus cognitif)

---

### Problème : Erreur Timeout 504

**Cause:** Requête trop long (>30 secondes)

**Solutions:**
1. Réduire la taille du contenu PDF
2. Poser une question plus ciblée
3. Augmenter le timeout dans `main.py` :
   ```python
   timeout=60  # Augmenter de 30 à 60 secondes
   ```

---

## 🔐 Sécurité

### Points Importants

1. **Variables d'Environnement**
   - Ne jamais commiter `.env` sur Git
   - Ajouter à `.gitignore` :
     ```
     .env
     .env.local
     *.pem
     ```

2. **CORS**
   - Actuellement configuré pour accepter toutes les origines (`allow_origins=["*"]`)
   - En production, remplacer par :
     ```python
     allow_origins=["https://votredomaine.com"]
     ```

3. **Rate Limiting**
   - Considérer l'ajout de rate limiting en production
   - Utility : `slowapi` (pip install slowapi)

4. **HTTPS**
   - En production, utiliser HTTPS seulement
   - Utiliser Let's Encrypt pour certificats gratuits

---

## 📊 Performances

### Optimisations Implémentées

| Optimisation | Bénéfice |
|---|---|
| Troncage intelligent du PDF | Réduit tokens, accélère réponses |
| Recherche par priorité | Trouve contenu pertinent plus vite |
| Compression CSS/JS | Charges plus rapides |
| Lazy loading PDFs | Économise mémoire |

### Métriques

- **Extraction PDF:** ~1-5 secondes (selon taille)
- **Requête API IA:** ~2-10 secondes (selon longueur)
- **Réponse locale:** <100ms
- **Taille initiale:** ~50 KB (HTML+CSS+JS)

---

## 📞 Support & Contact

Pour toute question ou problème :

1. Vérifier la section [Dépannage](#dépannage)
2. Consulter les logs du serveur
3. Ouvrir la console DevTools du navigateur (F12)

---

## 📜 Licence

Ce projet est fourni à titre d'exemple éducatif.

---

**Dernière mise à jour:** 15 mars 2026  
**Version:** 1.0.0
