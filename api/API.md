# 🔧 Documentation API - PDF Insight AI

## Table des Matières

1. [Vue d'ensemble](#vue-densemble)
2. [Installation Backend](#installation-backend)
3. [Configuration](#configuration)
4. [Structure du Code](#structure-du-code)
5. [Endpoints en Détail](#endpoints-en-détail)
6. [Gestion des Erreurs](#gestion-des-erreurs)
7. [Logging](#logging)
8. [Déploiement](#déploiement)

---

## 🎯 Vue d'ensemble

### À propos de l'API

L'API backend de PDF Insight AI est construite avec **FastAPI**, un framework Python moderne et performant pour créer des APIs REST.

### Caractéristiques

- ✅ Framework : FastAPI (async/await support)
- ✅ Serveur : Uvicorn (serveur ASGI haute performance)
- ✅ Validation : Pydantic (validation automatique des données)
- ✅ Validation : Vérification de types avec Pydantic
- ✅ CORS : Middleware CORS configuré
- ✅ Intégration : OpenRouter API (LLM)
- ✅ Logging : Logging complet pour débogage
- ✅ Timeouts : Gestion des timeouts (30s par défaut)

### Stack

```
Frontend (JavaScript)
    ↓ HTTP/JSON
FastAPI Backend
    ↓ HTTP/JSON
OpenRouter API
    ↓ Modèles LLM
GPT-4o-mini (ou autre modèle)
```

---

## 🚀 Installation Backend

### Prérequis

```
Python 3.8+
pip (gestionnaire de paquets)
```

### Vérifier Python

```bash
python --version
# Output: Python 3.x.x
```

### Installation des Dépendances

```bash
# 1. Naviguer vers le répertoire api
cd api

# 2. Créer un environnement virtuel
python -m venv venv

# 3. Activer l'environnement
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# 4. Installer les dépendances
pip install -r requirements.txt

# 5. Vérifier l'installation
python -c "import fastapi, uvicorn; print('✅ Installation réussie')"
```

### Dépendances Détaillées

```
fastapi==0.104.1          # Framework web async moderne
uvicorn==0.24.0           # Serveur ASGI haute performance
requests==2.31.0          # Client HTTP pour appels externes
python-dotenv==1.0.0      # Gestion des variables d'environnement
pydantic==2.5.0           # Validation et sérialisation JSON
```

---

## ⚙️ Configuration

### Variables d'Environnement

Créer un fichier `.env` à la racine du répertoire `api/` :

```bash
# api/.env
OPENROUTER_API_KEY=sk-xxxxxxxxxxxxxxxxxxxxx
```

### Format de la Clé API

- **Provider:** OpenRouter
- **Format:** Commence par `sk-`
- **Longueur:** ~32 caractères
- **Où obtenir:** https://openrouter.ai/keys

### Charger les Variables

Le fichier `.env` est chargé automatiquement par `python-dotenv` :

```python
from dotenv import load_dotenv
import os

load_dotenv()  # Charge le fichier .env
api_key = os.getenv("OPENROUTER_API_KEY")
```

### Vérifier la Configuration

```bash
# Lancer ce script pour vérifier
python << 'EOF'
import os
from dotenv import load_dotenv

load_dotenv()
api_key = os.getenv("OPENROUTER_API_KEY")

if api_key:
    print("✅ Clé API trouvée")
    print(f"   Clé: {api_key[:10]}...{api_key[-5:]}")
else:
    print("❌ Clé API non trouvée")
    print("   Vérifier .env ou variable d'environnement")
EOF
```

---

## 📂 Structure du Code

### Fichier main.py - Organisation

```
main.py
├── Imports
├── Configuration Logging
├── Chargement Variables d'environnement
├── Initialisation FastAPI
├── Middleware CORS
├── Configuration API (clés, URLs)
├── Modèles Pydantic
│   ├── QueryRequest
│   ├── QueryResponse
│   ├── BatchQueryRequest
│   └── BatchQueryResponse
├── Fonctions Utilitaires
│   └── truncate_pdf_content()
├── Endpoints
│   ├── GET /health
│   ├── POST /api/query
│   └── POST /api/batch-query
└── Exécution (main)
```

### Sections Clés du Code

#### 1. **Imports**
```python
from fastapi import FastAPI, HTTPException
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel
import requests
import os
from dotenv import load_dotenv
import json
import logging
```

#### 2. **Configuration Logging**
```python
logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)
```

#### 3. **Initialisation FastAPI**
```python
app = FastAPI(
    title="PDF Insight AI - Backend",
    version="1.0.0"
)
```

#### 4. **Middleware CORS**
```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],       # À limiter en production
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
    expose_headers=["*"],
)
```

---

## 🔌 Endpoints en Détail

### Endpoint 1: Health Check

#### Définition
```python
@app.get("/health")
async def health():
    """Health check endpoint"""
    return {"status": "ok", "service": "PDF Insight AI"}
```

#### HTTP Request
```http
GET /health HTTP/1.1
Host: 127.0.0.1:8000
```

#### HTTP Response
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status": "ok",
    "service": "PDF Insight AI"
}
```

#### Cas d'usage
- Vérifier que le serveur fonctionne
- Readiness check pour les déploiements
- Monitoring / Health checks

#### Exemple cURL
```bash
curl -X GET http://127.0.0.1:8000/health
```

#### Exemple JavaScript
```javascript
const isServerRunning = await fetch('http://127.0.0.1:8000/health')
    .then(r => r.ok);
console.log(isServerRunning ? "✅ Online" : "❌ Offline");
```

---

### Endpoint 2: Query Simple

#### Définition
```python
@app.post("/api/query", response_model=QueryResponse)
async def process_query(request: QueryRequest):
    """
    Process a question against PDF content using OpenRouter API
    """
```

#### HTTP Request
```http
POST /api/query HTTP/1.1
Host: 127.0.0.1:8000
Content-Type: application/json

{
    "pdf_text": "Lorem ipsum dolor sit amet...",
    "question": "De quoi parle ce texte?"
}
```

#### HTTP Response
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "answer": "Ce texte est un placeholder Lorem ipsum...",
    "model": "openai/gpt-4o-mini",
    "usage": {
        "prompt_tokens": 145,
        "completion_tokens": 87,
        "total_tokens": 232
    }
}
```

#### Paramètres (Body)

| Nom | Type | Requis | Description |
|-----|------|--------|-------------|
| `pdf_text` | string | ✅ | Contenu textuel du PDF |
| `question` | string | ✅ | Question de l'utilisateur |

#### Réponse

| Nom | Type | Description |
|-----|------|-------------|
| `answer` | string | Réponse de l'IA |
| `model` | string | Modèle utilisé (ex: gpt-4o-mini) |
| `usage` | object | Tokens utilisés |
| `usage.prompt_tokens` | number | Tokens du prompt |
| `usage.completion_tokens` | number | Tokens de la réponse |
| `usage.total_tokens` | number | Total des tokens |

#### Erreurs Possibles

```http
# Erreur 400 - Données manquantes
HTTP/1.1 400 Bad Request
{"detail": "Both pdf_text and question are required"}

# Erreur 500 - Clé API manquante
HTTP/1.1 500 Internal Server Error
{"detail": "OpenRouter API key not configured..."}

# Erreur 500 - Erreur API OpenRouter
HTTP/1.1 500 Internal Server Error
{"detail": "OpenRouter API error: ..."}

# Erreur 504 - Timeout
HTTP/1.1 504 Gateway Timeout
{"detail": "OpenRouter API request timed out..."}
```

#### Traitement Interne

```
1. Valider les données (Pydantic)
   ↓
2. Vérifier que clé API est configurée
   ↓
3. Vérifier les données non-vides
   ↓
4. Tronquer le PDF si trop volumineux (~8000 chars)
   ↓
5. Préparer les prompts système et utilisateur
   ↓
6. Appeler OpenRouter API (timeout 30s)
   ↓
7. Parser la réponse JSON
   ↓
8. Retourner QueryResponse
```

#### Exemple cURL Complet

```bash
curl -X POST http://127.0.0.1:8000/api/query \
  -H "Content-Type: application/json" \
  -d '{
    "pdf_text": "FastAPI est un framework web Python moderne...",
    "question": "Quel est le sujet principal?"
  }' | jq '.'
```

#### Exemple JavaScript/Node.js

```javascript
async function askQuestion(pdfContent, question) {
    try {
        const response = await fetch('http://127.0.0.1:8000/api/query', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({
                pdf_text: pdfContent,
                question: question
            })
        });

        if (!response.ok) {
            const error = await response.json();
            throw new Error(`API Error: ${error.detail}`);
        }

        const data = await response.json();
        console.log(`✅ Réponse: ${data.answer}`);
        console.log(`📊 Tokens: ${data.usage.total_tokens}`);
        return data;
        
    } catch (error) {
        console.error('❌ Erreur:', error.message);
        throw error;
    }
}
```

#### Exemple Python

```python
import requests
import json

def query_pdf(pdf_text, question):
    url = "http://127.0.0.1:8000/api/query"
    
    payload = {
        "pdf_text": pdf_text,
        "question": question
    }
    
    try:
        response = requests.post(
            url,
            json=payload,
            headers={"Content-Type": "application/json"},
            timeout=30
        )
        
        if response.status_code == 200:
            data = response.json()
            print(f"✅ Réponse: {data['answer']}")
            print(f"📊 Tokens: {data['usage']['total_tokens']}")
            return data
        else:
            print(f"❌ Erreur {response.status_code}: {response.text}")
            
    except requests.exceptions.Timeout:
        print("❌ Timeout après 30 secondes")
    except Exception as e:
        print(f"❌ Erreur: {e}")
```

---

### Endpoint 3: Batch Query

#### Définition
```python
@app.post("/api/batch-query", response_model=BatchQueryResponse)
async def batch_query(request: BatchQueryRequest):
    """
    Process multiple questions against PDF content in one request
    """
```

#### HTTP Request
```http
POST /api/batch-query HTTP/1.1
Host: 127.0.0.1:8000
Content-Type: application/json

{
    "pdf_text": "Contenu du PDF...",
    "questions": [
        "Première question?",
        "Deuxième question?",
        "Troisième question?"
    ]
}
```

#### HTTP Response
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "results": [
        {
            "question": "Première question?",
            "answer": "Réponse 1..."
        },
        {
            "question": "Deuxième question?",
            "answer": "Réponse 2..."
        }
    ]
}
```

#### Paramètres

| Nom | Type | Requis |
|-----|------|--------|
| `pdf_text` | string | ✅ |
| `questions` | array[string] | ✅ |

#### Avantages du Batch

- ⚡ Plus rapide (une seule requête réseau)
- 💰 Aide avec optimisation de coûts API
- 🔄 Traitement séquentiel garanti
- 📊 Meilleur pour les analyses complètes

#### Exemple JavaScript

```javascript
async function batchAnalyze(pdfContent, questions) {
    const response = await fetch('http://127.0.0.1:8000/api/batch-query', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
            pdf_text: pdfContent,
            questions: questions
        })
    });

    const data = await response.json();
    
    data.results.forEach((result, index) => {
        console.log(`Q${index+1}: ${result.question}`);
        console.log(`A${index+1}: ${result.answer}\n`);
    });
}
```

---

## 🚨 Gestion des Erreurs

### Classification des Erreurs

#### Erreurs Validation (400)
```json
{
    "detail": "Both pdf_text and question are required"
}
```

**Cause:** Données manquantes ou invalides
**Solution:** Vérifier le format de la requête

#### Erreurs Serveur (500)
```json
{
    "detail": "OpenRouter API key not configured. Set OPENROUTER_API_KEY in .env"
}
```

**Cause:** Configuration manquante
**Solution:** Configurer OPENROUTER_API_KEY

#### Erreurs API Externe (500)
```json
{
    "detail": "OpenRouter API error: Authorization required"
}
```

**Cause:** Problème avec clé API OpenRouter
**Solution:** Vérifier la clé API, quota disponible

#### Timeout (504)
```json
{
    "detail": "OpenRouter API request timed out after 30 seconds"
}
```

**Cause:** Requête trop longue
**Solution:** Réduire taille PDF, réessayer

### Gestion dans le Code

```python
try:
    # Appel API
    response = requests.post(
        OPENROUTER_API_URL,
        headers=headers,
        json=payload,
        timeout=30
    )
    
    # Vérifier le statut
    if response.status_code != 200:
        raise HTTPException(
            status_code=response.status_code,
            detail=f"OpenRouter API error: {response.text}"
        )
        
    data = response.json()
    return QueryResponse(...)
    
except requests.exceptions.Timeout:
    raise HTTPException(
        status_code=504,
        detail="Request timed out"
    )
    
except requests.exceptions.RequestException as e:
    raise HTTPException(
        status_code=500,
        detail=f"Error: {str(e)}"
    )
```

---

## 📝 Logging

### Configuration

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)
```

### Niveaux de Log

| Niveau | Symbole | Exemple |
|--------|---------|---------|
| INFO | 🔵 | `logger.info("Request received")` |
| WARNING | ⚠️ | `logger.warning("PDF truncated")` |
| ERROR | ❌ | `logger.error("API key not found")` |
| DEBUG | 🔧 | `logger.debug("Processing...")` |

### Logs Générés

```
🔵 Received query request
API Key configured: True
PDF text length: 5432
Question: De quoi parle ce texte?
⚠️  PDF text truncated from 15000 to 8000 characters
🔵 Calling OpenRouter API...
✅ Success! Got response.
```

### Affichage en Temps Réel

```bash
# Lancer le serveur en mode verbose
python -m uvicorn main:app --reload --log-level debug
```

---

## 🚀 Déploiement

### Environnement de Développement

```bash
# Démarrage simplifié
python -m uvicorn main:app --reload

# Avec options personnalisées
python -m uvicorn main:app \
    --reload \
    --host 127.0.0.1 \
    --port 8000 \
    --log-level info
```

### Environnement de Production

#### Option 1: Uvicorn + Gunicorn

```bash
# Installation
pip install gunicorn

# Lancement avec workers multiples
gunicorn -w 4 -k uvicorn.workers.UvicornWorker main:app \
    --bind 0.0.0.0:8000 \
    --workers 4 \
    --worker-class uvicorn.workers.UvicornWorker \
    --access-logfile - \
    --error-logfile -
```

#### Option 2: Docker

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY main.py .
COPY .env .

ENV PYTHONUNBUFFERED=1

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Construire et exécuter:**
```bash
docker build -t pdf-insight-api .
docker run -p 8000:8000 --env-file .env pdf-insight-api
```

#### Option 3: Systemd Service (Linux)

```ini
# /etc/systemd/system/pdf-insight.service
[Unit]
Description=PDF Insight AI API
After=network.target

[Service]
Type=notify
User=www-data
WorkingDirectory=/var/www/pdf-insight
ExecStart=/var/www/pdf-insight/venv/bin/python -m uvicorn main:app --host 0.0.0.0 --port 8000
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

**Activer le service:**
```bash
sudo systemctl daemon-reload
sudo systemctl enable pdf-insight
sudo systemctl start pdf-insight
```

### Variables d'Environnement Production

```bash
# .env pour production
OPENROUTER_API_KEY=sk-xxxxx
CORS_ORIGINS=https://votredomaine.com
LOG_LEVEL=info
```

### Sécurité en Production

```python
# Modifier main.py pour production
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=[
        "https://votredomaine.com",
        "https://www.votredomaine.com"
    ],
    allow_methods=["GET", "POST"],  # Plus restrictif
    allow_headers=["Content-Type"],  # Uniquement nécessaire
)

# Activer HTTPS
# use_ssl=True avec certificats
```

---

## 📊 Performance

### Optimisations Implémentées

1. **Troncage intelligent** (~8000 chars max)
   - Réduit tokens OpenRouter
   - Accélère les réponses
   - Réduit les coûts API

2. **Recherche par priorité**
   - Extrait sections pertinentes
   - Améliore la précision

3. **Gestion mémoire**
   - Async/await pour multi-requêtes
   - Pas de cache mémoire (stateless)

### Benchmarks

| Opération | Temps Typical |
|-----------|--------------|
| PDF extraction | 0.5-2s |
| API call | 3-8s |
| Response parsing | <100ms |
| **Total** | **3-10s** |

### Améliorer les Performances

```python
# Augmenter timeout pour gros PDFs
timeout=60  # au lieu de 30

# Réduire le troncage pour plus de contexte
max_chars=12000  # au lieu de 8000

# Réduire la température pour plus de stabilité
"temperature": 0.5  # au lieu de 0.7
```

---

## 📚 Ressources

- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Pydantic Documentation](https://docs.pydantic.dev/)
- [OpenRouter Documentation](https://openrouter.ai/docs)
- [Uvicorn Documentation](https://www.uvicorn.org/)

---

**Mise à jour:** 15 mars 2026  
**Version API:** 1.0.0
