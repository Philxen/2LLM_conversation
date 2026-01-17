# LLM To LLM (on Windows)

Fun test to put 2 Local LLMs into discussions with each other.


## Installation

- Run the **start-script.bat** to install the env and librairies

- [=**Be careful here we use CUDA 11.8**=]

- [=**Be careful to have installed Microsoft C++ build tools on your computer**=]

- Install Ollama **https://ollama.com/download**

- Pull the model you want (here i used llama3)

- You can modify the main.py to change models and language


## Author

- [@nixiz0](https://github.com/nixiz0)

### Repository explanation

C'est maintenant beaucoup plus clair ! Grâce à la structure complète que vous avez fournie, on peut voir l'organisation en "couches" de l'application. C'est un projet très bien structuré qui sépare la logique de communication, la gestion du débat et la sauvegarde.

Voici l'architecture globale décomposée par responsabilités :

---

### 1. Le Cœur : La boucle de débat (`models_chat.py`)

C'est ici que se trouve le "cerveau" de l'application. La fonction `start_two_llm_chat` orchestre le duel.

* **Initialisation** : Elle crée deux instances de `NarratorVoice` (avec des couleurs différentes, bleu et rouge, probablement pour un affichage visuel ou un sous-titrage).
* **La boucle de ping-pong** :
* Elle commence avec un simple "Bonjour".
* Le LLM 1 répond. Sa réponse devient le `prompt` pour le LLM 2.
* Le LLM 2 répond. Sa réponse devient le nouveau `prompt` pour le LLM 1.


* **Contrôle utilisateur** : Le script écoute le clavier. Si vous appuyez sur `*`, la variable `working` passe à `False`, ce qui arrête proprement le débat.

### 2. Le Moteur de Communication (`model.py`)

Ce fichier est l'outil technique utilisé par le cœur. Comme nous l'avions vu, il s'occupe de :

* Formater l'historique pour qu'Ollama comprenne le contexte.
* Gérer le "Streaming" (recevoir le texte mot à mot).
* Découper le texte par phrases pour que le système de voix (`visual_talk`) puisse commencer à parler sans attendre la fin du paragraphe.

### 3. La Mémoire Persistante (`save_llm_text.py`)

C'est le module de rangement. À chaque tour de boucle dans `models_chat.py`, il sauvegarde l'état actuel de la discussion.

* **Localisation** : Il crée un dossier nommé `conversations_model` directement sur votre **Bureau**.
* **Nommage intelligent** : Il prend les 6 premiers mots du tout premier message pour nommer le fichier CSV (en nettoyant la ponctuation).

---

### Schéma du fonctionnement général

1. **`main.py`** : Lance l'ordre de départ avec les noms des modèles (ex: `llama3`).
2. **`models_chat.py`** :
* Prépare les voix.
* Lance la boucle `while`.


3. **Appel à `model.py**` :
* Envoie la question à l'API Ollama.
* Affiche le texte à l'écran.
* Fait "parler" l'agent via `voices`.


4. **Appel à `save_llm_text.py**` :
* Enregistre l'échange dans un fichier CSV sur le bureau.


5. **Retour au point 3** : Avec l'autre modèle.

---

### Points intéressants à noter :

* **Interruption propre** : L'utilisation de la bibliothèque `keyboard` avec la touche `*` est très pratique pour arrêter l'IA sans "tuer" brutalement le processus Python, ce qui permet de garantir que le fichier CSV est bien finalisé.
* **Localhost** : Tout se passe en local. Si Ollama n'est pas lancé sur votre machine avec le modèle `llama3` téléchargé, le code renverra une erreur de connexion.
* **Extensibilité** : Dans `main.py`, vous pouvez changer `llm2="llama3"` par un autre modèle (ex: `mistral`) pour tester la supériorité d'un modèle sur l'autre.
