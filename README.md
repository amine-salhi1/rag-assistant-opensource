# Assistant RAG open source, 100 % local

Notebook unique : `rag_pipeline.ipynb`.

Assistant qui répond en français à des questions RH à partir d'un règlement interne fictif et de 7 articles Wikipédia (292 segments), en **citant ses sources** et en **refusant les questions hors sujet**. Tout tourne en local : embeddings `intfloat/multilingual-e5-small`, base vectorielle **ChromaDB**, génération `Qwen/Qwen2.5-1.5B-Instruct` (LangChain, Hugging Face).

## Points clés

- Découpage adapté à la source (un sujet par segment pour le règlement), préfixes `query:` / `passage:` de e5, collection vidée avant indexation (pas de doublons), *chat template* de Qwen, décodage déterministe.
- Refus à deux niveaux : seuil de pertinence avant d'appeler le LLM, consigne de refus répétée en fin de message.
- **Analyse d'échec** : « Quel est le prix d'une Tesla ? » n'était pas refusée (l'article *Télétravail* mentionne Tesla).
- **Évaluation chiffrée** (section 5) : 25 questions, comparaison dense / BM25 / hybride (RRF), Hit@k, MRR, AUROC de refus, seuil choisi sur les données, évaluation de bout en bout.

## Résultats

<!-- Coller ici le tableau de comparaison et le bilan de bout en bout de la section 5. -->

## Lancer

Placer `entreprise_data.txt` à la racine, `pip install -r requirements.txt`, puis exécuter le notebook (CPU suffisant ; le premier lancement télécharge les modèles).
