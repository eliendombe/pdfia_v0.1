# ⚡ Guide d'Installation Rapide - PDF Insight AI

## ✅ Checklist de Configuration

Cette page vous guide étape par étape pour mettre en place PDF Insight AI en moins de 10 minutes.

---

## 📋 Prérequis

Avant de commencer, vérifiez que vous avez :

- [ ] **Python 3.8+** installé
  ```bash
  python --version
  # Output: Python 3.x.x (3.8+ required)
  ```

- [ ] **Clé API OpenRouter**
  - Créer un compte sur https://openrouter.ai
  - Aller à https://openrouter.ai/keys
  - Générer une clé API (format: `sk-...`)

- [ ] **Navigateur web moderne** (Chrome, Firefox, Safari, Edge)

- [ ] **Éditeur de texte** (VS Code, Notepad++, etc.)

---

## 🚀 Installation (5 minutes)

### Étape 1: Préparer les Fichiers (2 min)

```bash
# Le dossier pdfia doit contenir:
pdfia/
├── index.html          ✅ Fourni
├── styles.css          ✅ Fourni
├── script.js           ✅ Fourni
└── api/
    ├── main.py         ✅ Fourni
    └── requirements.txt ✅ Fourni
```

**✅ Tous les fichiers doivent être fournis. Si manquant, contactez l'administrateur.**

---

### Étape 2: Installer les Dépendances Python (2 min)

Ouvrir PowerShell/Terminal dans le dossier `api/` :

```powershell
# Naviguer vers le dossier pdfia
cd f:\CODEURS\CHALLENGE\PROJET\pdfia\api

# Créer un environnement virtuel Python
python -m venv venv

# Activer l'environnement virtuel (Windows)
venv\Scripts\activate

# Activer l'environnement virtuel (macOS/Linux)
# source venv/bin/activate

# Installer les dépendances
pip install -r requirements.txt

# Vérifier l'installation
python -c "import fastapi; print('✅ FastAPI installing correctly')"
```

**Résultat attendu:**
```
✅ FastAPI installing correctly
```

---

### Étape 3: Configurer la Clé API (1 min)

Créer un fichier `.env` dans le dossier `api/` :

```
# Fichier: api/.env

# Coller votre clé API OpenRouter
OPENROUTER_API_KEY=sk-votre_cle_api_ici
```

**Important:**
- Remplacer `sk-votre_cle_api_ici` par votre vrai clé
- Le fichier commençant par le point (`.`) est caché sur macOS/Linux
- Sur Windows, VS Code l'affichera normalement

**Vérifier la configuration:**
```powershell
# Depuis le dossier api/
python << 'EOF'
import os
from dotenv import load_dotenv

load_dotenv()
key = os.getenv("OPENROUTER_API_KEY")

if key and key.startswith("sk-"):
    print("✅ Clé API trouvée et valide")
else:
    print("❌ Clé API introuvable ou invalide")
    print("   Vérifier le fichier .env")
EOF
```

---

## 🎮 Lancer l'Application (2 minutes)

### Étape 4: Démarrer le Serveur Backend

```powershell
# Depuis le dossier api/ (avec venv activé)
python -m uvicorn main:app --reload

# Vous devriez voir:
# INFO:     Uvicorn running on http://127.0.0.1:8000
# INFO:     Application startup complete
```

**Laisser ce terminal ouvert (ne pas fermer).**

---

### Étape 5: Ouvrir l'Application Frontend

Ouvrir le fichier `index.html` dans le navigateur web:

**Option A: Double-cliquer le fichier**
- Aller à `pdfia/index.html`
- Double-cliquer pour ouvrir dans le navigateur par défaut

**Option B: Serveur local (recommandé)**
```powershell
# Ouvrir un NOUVEAU terminal (ne pas fermer l'autre)
# Naviguer vers le dossier racine pdfia
cd f:\CODEURS\CHALLENGE\PROJET\pdfia

# Lancer un serveur web simple
python -m http.server 3000

# Ouvrir dans le navigateur
# http://localhost:3000
```

Vous devriez voir l'interface PDF Insight AI :

```
┌────────────────────────────────────────┐
│  📄 PDF Insight AI                    │
│  Analyseur de Documents Intelligent   │
│                                       │
│  📁 Charger votre document PDF       │
│  (Glissez-déposez ici)               │
│                                       │
│  [Choisir un PDF]                    │
└────────────────────────────────────────┘
```

---

## ✨ Premier Test (2 minutes)

### Tester l'Application

1. **Préparer un fichier PDF**
   - Choisir un fichier PDF test
   - Taille recommandée: 1-10 MB pour le test

2. **Charger le PDF**
   - Cliquer sur "Choisir un PDF" ou glisser-déposer
   - Attendre l'extraction (1-5 secondes)

3. **Poser une Question**
   - Exemple: "De quoi parle ce document?"
   - Cliquer "Analyser" ou appuyer sur Entrée
   - Attendre la réponse (3-10 secondes)

4. **Voir la Réponse**
   ```
   Q: De quoi parle ce document?
   
   🤖 Réponse IA (gpt-4o-mini):
   Ce document parle de...
   ```

---

## 🧪 Dépannage Rapide

### ❌ "Impossible de contacter l'API"

**Vérifier:**
1. ✅ Terminal avec serveur FastAPI toujours ouvert?
   ```powershell
   # Devrait afficher "Uvicorn running on..."
   ```

2. ✅ URL correcte dans `script.js`?
   ```javascript
   const API_BASE_URL = "http://127.0.0.1:8000";  // ✅ Correct
   ```

3. ✅ Backend vraiment démarré?
   ```bash
   # Tester manuellement
   curl http://127.0.0.1:8000/health
   # Devrait retourner: {"status":"ok",...}
   ```

**Solution:**
```powershell
# Redémarrer le serveur depuis dossier api/
python -m uvicorn main:app --reload
```

---

### ❌ "Clé API non configurée"

**Vérifier:**
1. ✅ Fichier `api/.env` existe?
2. ✅ Format correct: `OPENROUTER_API_KEY=sk-...`
3. ✅ Clé commence par `sk-`?
4. ✅ Pas d'espacements supplémentaires?

**Solution:**
```powershell
# Vérifier le fichier .env
type api\.env
# Devrait afficher: OPENROUTER_API_KEY=sk-xxxxx
```

---

### ❌ "Le PDF ne se charge pas"

**Vérifier:**
1. ✅ Fichier est vraiment un PDF?
   ```powershell
   # Tester le fichier
   file votrefichier.pdf
   ```

2. ✅ Taille < 50 MB?
   ```powershell
   ls -lh votrefichier.pdf
   ```

3. ✅ PDF n'est pas scanné (sans OCR)?
   - PDF scanné = image (pas de texte)
   - Solution: Utiliser OCR ou convertir le PDF

**Solution:**
- Essayer avec un autre PDF
- Convertir avec un outil online si besoin
- Vérifier la console du navigateur (F12)

---

### ❌ Erreur "ModuleNotFoundError: No module named 'fastapi'"

**Cause:** Dépendances non installées correctement

**Solution:**
```powershell
# S'assurer que l'environnement est activé
# (devrait voir (venv) au début du terminal)

# Réinstaller les dépendances
pip install -r requirements.txt --force-reinstall

# Vérifier
python -c "import fastapi; print('✅ OK')"
```

---

## 📝 Configuration Avancée (Optionnel)

### Changer le Modèle IA

Modifier dans `api/main.py` (ligne ~120):

```python
# Avant (défaut, rapide et économique)
"model": "openai/gpt-4o-mini",

# Après (plus puissant)
"model": "openai/gpt-4"  # À essayer si gpt-4o-mini trop faible
```

**Modèles disponibles:**
- `openai/gpt-4o-mini` - Recommandé ⭐ (rapide, bon prix)
- `openai/gpt-4` - Plus puissant (plus cher)
- `anthropic/claude-3-haiku` - Alternative

---

### Activer Mode Recherche Locale (Sans API)

Si vous n'avez pas de clé API:

Dans `script.js` (ligne ~7):

```javascript
// Avant (API IA)
const USE_AI_API = true;

// Après (Recherche locale)
const USE_AI_API = false;
```

⚠️ **Note:** Mode local moins précis, mais zéro coût et sans internet.

---

### Augmenter la Limite de Taille PDF

Éditer `api/main.py` dans la fonction `truncate_pdf_content()`:

```python
# Avant (8000 caractères max)
def truncate_pdf_content(pdf_text: str, question: str, max_chars: int = 8000):

# Après (12000 caractères max - plus de contexte)
def truncate_pdf_content(pdf_text: str, question: str, max_chars: int = 12000):
```

⚠️ **Attention:** Plus de caractères = plus cher en API

---

## 🎓 Prochaines Étapes

Maintenant que l'application est en place:

1. **Lire la documentation complète**
   - `DOCUMENTATION.md` - Vue d'ensemble générale
   - `api/API.md` - Référence API détaillée

2. **Tester les cas avancés**
   - Batch queries (plusieurs questions à la fois)
   - PDFs volumineux
   - Différents types de documents

3. **Intégrer dans votre système**
   - Utiliser l'API depuis votre application
   - Ajouter de l'authentification en production
   - Mettre sur un serveur en ligne

---

## 📞 Support

En cas de problème:

1. **Vérifier les logs du serveur**
   - Regarder le terminal avec FastAPI
   - Chercher les messages d'erreur (rouge)

2. **Ouvrir la console du navigateur**
   - Appuyer sur F12 ou Ctrl+Maj+I
   - Onglet "Console" pour voir les erreurs JavaScript

3. **Tester manuellement l'API**
   ```bash
   # Depuis PowerShell/Terminal
   curl -X POST http://127.0.0.1:8000/api/query `
     -H "Content-Type: application/json" `
     -d '{"pdf_text":"Test","question":"Question?"}'
   ```

4. **Consulter la documentation complète**
   - Fichier `DOCUMENTATION.md`
   - Section "Dépannage"

---

## ✅ Checklist Finale

Avant de déclarer la configuration réussie:

- [ ] Python 3.8+ installé? `python --version`
- [ ] Clé API OpenRouter configurée? `cat api\.env`
- [ ] Dépendances installées? `pip list | grep fastapi`
- [ ] Serveur FastAPI fonctionne? `http://127.0.0.1:8000/health`
- [ ] Frontend s'affiche? `http://localhost:3000`
- [ ] Premier PDF se charge?
- [ ] Première réponse s'affiche?

Si tous les ✅ sont cochés, **félicitations! 🎉**

---

**Durée totale:** ~10 minutes  
**Difficulté:** Débutant  
**Dernière mise à jour:** 15 mars 2026
