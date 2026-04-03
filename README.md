# Haloscan MCP Server – Setup avec n8n

Ce guide explique comment configurer le **Haloscan MCP Server** dans vos workflows **n8n** afin de créer un système propulsé par l’IA capable de traiter intelligemment les messages de chat et d’exécuter des outils dynamiquement grâce à MCP.

---

## 1. Nœuds de base du workflow n8n (5 nœuds)

1. **When Chat Message Received Node**  
2. **AI Agent Node**  
3. **Chat Model Node**  
4. **Simple Memory Node**  
5. **MCP Client Node**

---

## 2. Fonctionnement du workflow

Ce workflow n8n active un système basé sur l’IA qui traite les messages de chat et appelle dynamiquement des outils via MCP.  

### 2.1 Réception du message  
Le workflow commence lorsqu’un nouveau message est reçu via le nœud **When Chat Message Received**.  

### 2.2 Activation de l’agent IA  
Le message est transmis à un **AI Agent** qui interprète l’intention de l’utilisateur et coordonne les prochaines étapes.  

### 2.3 Compréhension du langage (Chat Model)  
Le nœud **Chat Model (LLM)** analyse le message en tenant compte du contexte et de la mémoire.  

### 2.4 Gestion du contexte (Simple Memory)  
Un nœud **Simple Memory** stocke et récupère le contexte pertinent pour garder une continuité de la conversation.  

### 2.5 Nœud MCP Client  
Un seul nœud qui :  
- Liste les outils disponibles  
- Exécute l’outil choisi par l’agent avec ses paramètres  

---

## 3. Configuration des identifiants (Credentials)

### 3.1 Chat Model Node  
- Ouvrez la configuration du Chat Model  
- Créez un nouvel identifiant : **+ Create new credential**  
- Donnez-lui un nom  
- Collez la clé API de votre modèle LLM (ex. **Claude Sonnet 3.5**)  
- Cliquez sur **Save**  

### 3.2 MCP Client Node  
- Ouvrez la configuration du MCP Client Node  
- **Authentification :** Header Auth  
- Créez un nouvel identifiant Header Auth :  
  - Nom : `Haloscan-Api-Key`  
  - Valeur : `your_haloscan_api_key`  
- Cliquez sur **Save**

---

## 4. Configuration des nœuds du workflow

### 4.1 When Chat Message Received Node  
- Cliquez sur le **+** en haut à droite  
- Cherchez et ajoutez ce nœud  

### 4.2 AI Agent Node  
- Ajoutez l’**AI Agent** et connectez-le au nœud précédent  
- Configurez :  
  - **Source for Prompt (User Message):** Connected Chat Trigger Node  
  - **Prompt (User Message):** `{{ $json.chatInput }}`  
  - **System Message :** Copiez le texte fourni via [ce lien](https://gist.githubusercontent.com/occirank/bb798dd9b5d6f7143b5e81671d66d6b8/raw/a97b449211eb40055dac4a1b5717a90f491ffb26/n8n) et collez-le dans ce champ  

### 4.3 Chat Model Node  
- Ajoutez un **Chat Model**  
- Connectez-le à l’AI Agent  
- Configurez :  
  - Identifiant : choisissez celui créé à l’étape **3.1**  
  - Modèle : Claude Sonnet 3.5 ou supérieur  

### 4.4 Simple Memory Node  
- Ajoutez un **Simple Memory Node**  
- Connectez-le à l’AI Agent  

### 4.5 MCP Client Node  
- Ajoutez un **MCP Client Tool Node**  
- Connectez-le à l’AI Agent  
- Configurez :  
  - **SSE Endpoint :** `https://mcp.haloscan.com/sse`  
  - **Authentification :** Header Auth  
  - **Credential :** Celui créé à l’étape **3.2**  
  - **Tools to Include :** All  

---

## 5. Test du workflow

- Cliquez sur **Save**  
- Lancez le workflow  
- Entrez des prompts dans le noeud **When Chat Message Received Node** pour tester son fonctionnement 

Exemples des prompts:

```text
"Analyse le mot-clé 'marketing digital' pour monsite.com"
ou
"Analyse monsite.com et donne un aperçu SEO complet : visibilité, mots-clés principaux, positions et concurrents."
``` 
- N8N workflow doit retourner des données SEO structurées récupérées depuis l’API Haloscan.

Exemples d’utilisation :

- Tendances de classement des mots-clés  
- Mesures de visibilité des sites web  
- Analyse des concurrents  



