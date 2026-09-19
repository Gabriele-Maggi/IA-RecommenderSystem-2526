# IA-RecommenderSystem-2526 🎬

An anime recommender system built as a project for the Artificial Intelligence course (2025/2026). The application combines **text and visual embeddings**, **clustering of user preferences**, and a vector search engine (**FAISS**) to generate personalized recommendations, wrapped in an interactive **Streamlit** interface.

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Project Architecture](#project-architecture)
- [Repository Structure](#repository-structure)
- [Requirements](#requirements)
- [Installation](#installation)
- [Running with Docker](#running-with-docker)
- [Usage](#usage)
- [Evaluation](#evaluation)
- [License](#license)

## Overview

The system builds a user profile from their watchlist (watched anime and ratings), computes the "centers" of their preferences via clustering over the embedding space, and retrieves the most similar anime from a vector index. Users can refine the results in several ways:

- **Manual selection** of genres and animation studios
- **Free-text description** in natural language (e.g. *"I want a Romance anime, in the style of studio Mappa"*)
- **Synopsis/plot search**, finding anime with a similar storyline
- **Image search**, finding anime that are visually similar to an uploaded image

## Key Features

- **Multimodal embeddings**: text encoders (synopsis, based on `sentence-transformers`) and visual encoders (posters/images) to represent anime in a shared vector space.
- **Vector search with FAISS** for fast retrieval of the most similar anime.
- **Node2Vec** for learning graph-based representations of relationships/interactions between anime.
- **Goal parsing**: interpretation of natural-language requests, automatically translated into filters (genre, studio, synopsis, image).
- **Dynamic user profiling**: interest clusters are computed from the user's rating history and updated in real time with every new rating.
- **Advanced filtering**: "append" or "influence" filter modes, with an adjustable magnitude.
- **Interactive web interface** built with Streamlit, including anime detail views, user history, and rating cards.
- **Evaluation suite** (including a parallelized version) for measuring recommendation quality, with LLM-based metrics included.

## Project Architecture

The application flow, mainly handled by `main.py`, works as follows:

1. **User login**: the user enters a username/ID and the system loads (or creates) the corresponding profile.
2. **Cluster computation**: interest centers are computed in the embedding space from the user's watchlist.
3. **Retrieval**: the FAISS index is used to retrieve the anime closest to the interest centers.
4. **Optional filtering**: the user can refine results with manual, text, synopsis, or image filters, interpreted by the goal-parsing module.
5. **Feedback loop**: every new rating updates the user profile and recomputes recommendations.

## Repository Structure

```
├── Embeddings/            # Modules for generating embeddings (text/visual)
├── Encoders/               # Encoders for synopsis, images, and tabular features
├── Libs/                   # Core libraries: indexing, goal parsing, user management
├── evaluation_results/      # Evaluation results for the system
├── visualizations/          # Visualizations (e.g. t-SNE) of the embedding spaces
├── main.py                  # Main Streamlit application
├── Evaluation.py             # Evaluation script for the recommender system
├── Eval_parallel.py           # Parallelized version of the evaluation
├── EvalutationLLM.py          # LLM-based evaluation
├── tsne.py                   # t-SNE visualization of the embeddings
├── debug_db.py                # Debug utility for the vector database
├── test_*.py                  # Test scripts for the various components (genre, goal, index, node2vec, retrieve, tf-idf, watched)
├── Dockerfile / docker-compose.yml   # Configuration for containerized execution
├── requirements.txt            # Python dependencies (local environment)
├── requirements_dock.txt        # Python dependencies (Docker environment)
└── setup.py                     # Package setup
```

## Requirements

- Python 3.9+ (recommended)
- Main libraries used (see `requirements.txt`):
  - `numpy`
  - `torch`, `torchvision`
  - `transformers`, `sentence-transformers`
  - `node2vec`
  - `faiss-cpu`
  - `streamlit`
  - `pyarrow`
  - libraries for interacting with MyAnimeList (`mal-api.py`, `malclient-upgraded`)

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/Gabriele-Maggi/IA-RecommenderSystem-2526.git
   cd IA-RecommenderSystem-2526
   ```

2. Create a virtual environment (optional but recommended) and install the dependencies:
   ```bash
   python -m venv venv
   source venv/bin/activate   # on Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. Make sure the dataset (e.g. `Dataset/AnimeList.csv` and the corresponding images) is available in the folder structure expected by the application.

4. Run the application:
   ```bash
   streamlit run main.py
   ```

## Running with Docker

Alternatively, the project can be run via Docker:

```bash
docker compose up --build
```

`Dockerfile` and `docker-compose.yml` handle building the image and starting the service, using the dependencies listed in `requirements_dock.txt`.

## Usage

Once the application is running:

1. Enter your username or ID (e.g. from MyAnimeList) in the sidebar to load your recommendations.
2. Browse the suggested anime on the **Recommendations** page.
3. Refine the results by choosing between:
   - **Manual Selection**: filter by genres and studios.
   - **Text Description**: describe in words what you're looking for.
   - **Synopsis Search**: search based on a plot description.
   - **Image Search**: upload an image to find anime with a similar visual style.
4. Rate anime directly from the card to update your profile and get increasingly accurate recommendations.
5. Check the **User History** page to review your watch history and past ratings.

## Evaluation

The project includes dedicated scripts for evaluating the recommender's performance:

- `Evaluation.py` / `Eval_parallel.py`: compute recommendation quality metrics (also runnable in parallel for larger datasets).
- `EvalutationLLM.py`: qualitative evaluation using a language model.
- Results are saved in the `evaluation_results/` folder, while `visualizations/` and `tsne.py` allow visual inspection of the embedding space.

## License

This project is distributed under the **Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)** license. See the [LICENSE](./LICENSE) file for full details. In short: the material may be freely shared and adapted for **non-commercial** purposes, provided appropriate attribution is given.
