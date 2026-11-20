# Assistant RAG open source, 100 % local

Notebook unique : `rag_pipeline.ipynb`.

Assistant qui répond en français à des questions RH à partir d'un règlement interne fictif et de 7 articles Wikipédia (291 segments), en **citant ses sources** et en **refusant les questions hors sujet**. Tout tourne en local : embeddings `intfloat/multilingual-e5-small`, base vectorielle **ChromaDB**, génération `Qwen/Qwen2.5-1.5B-Instruct` (LangChain, Hugging Face).

## Points clés

- Découpage adapté à la source (un sujet par segment pour le règlement), préfixes `query:` / `passage:` de e5, collection vidée avant indexation (pas de doublons), *chat template* de Qwen, décodage déterministe.
- Refus à deux niveaux : seuil de pertinence avant d'appeler le LLM, consigne de refus répétée en fin de message.
- **Analyse d'échec** : « Quel est le prix d'une Tesla ? » n'était pas refusée (l'article *Télétravail* mentionne Tesla).
- **Évaluation chiffrée** (section 5) : 25 questions, comparaison dense / BM25 / hybride (RRF), Hit@k, MRR, AUROC de refus, seuil choisi sur les données, évaluation de bout en bout.

## Résultats

25 questions de test : 15 dans le périmètre (source attendue connue), 10 hors sujet dont des pièges.

| Recherche | Hit@1 | Hit@4 | MRR | AUROC refus |
|---|---|---|---|---|
| dense (e5) | 0,933 | **1,000** | **0,956** | **1,000** |
| BM25 | 0,933 | 0,933 | 0,933 | 0,953 |
| hybride (RRF) | 0,867 | 0,933 | 0,900 | 0,913 |

**De bout en bout** (Qwen2.5-1.5B, CPU) : 93 % des questions pertinentes bien traitées (14/15), 0 % de faux refus, 100 % des questions hors sujet refusées.

- La recherche dense est la meilleure sur ce corpus ; la fusion hybride RRF dégrade le classement et le signal de refus.
- Choisir le seuil de refus sur les données (0,873 au lieu de 0,79 fixé à la main) corrige le cas Tesla ; ce seuil est toutefois choisi sur les mêmes questions, donc l'estimation est optimiste.
- Échec restant : sources contradictoires (congé paternité TechCorp de 28 jours ouvrés contre 25 jours selon Wikipédia : le modèle répond 25). Le petit LLM ajoute aussi parfois des détails inexacts, non détectés par le critère par mots-clés.

## Lancer

`pip install -r requirements.txt`, puis exécuter le notebook (CPU suffisant ; le premier lancement télécharge les modèles).
