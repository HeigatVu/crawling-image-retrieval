# End-to-End Image Retrieval System with CLIP and Custom Data Pipeline

This repository contains the complete code for an advanced image retrieval system. The project covers the full machine learning lifecycle: from building a custom dataset by scraping the web to implementing a highly efficient, deep learning-based similarity search engine using CLIP and a vector database.

The project is organized into three main Jupyter notebooks, each handling a distinct stage of the pipeline.

## 📂 Repository Structure

* **`crawler.ipynb`**: The first stage. A web scraper that browses Flickr to collect thousands of image URLs based on specified search terms.
* **`image_urls.json`**: The output of the crawler, containing all the collected image URLs in a structured JSON format.
* **`preprocessing.ipynb`**: The second stage. This notebook handles downloading, cleaning, and organizing the images from the URLs into a clean, structured dataset ready for model training/inference.
* **`filename.txt`**: An output of the preprocessing notebook, listing the file paths of all the cleaned images.
* **`vectorDB-CLIP.ipynb`**: The final stage. This notebook implements the core image retrieval logic using the prepared dataset, the CLIP model, and a vector database for efficient search. [(I was tested L1, L2, Cosine Similarity, and correlation coefficient)](https://github.com/HeigatVu/image-retrieval)

---

## 🚀 Project Pipeline & Workflow

To run this project, the notebooks must be executed in the following order.

### Stage 1: Data Collection (`crawler.ipynb`)

This notebook automates the process of building a large, custom image dataset from the web.

* **Objective**: To gather thousands of image URLs from `flickr.com`.
* **Methodology**:
    * Uses **Selenium** to programmatically control a web browser, which is necessary to handle dynamic web elements like infinite scrolling and "load more" buttons.
    * Uses **BeautifulSoup** to parse the page's HTML and extract the source URLs from `<img>` tags.
    * Employs multi-threading (`concurrent.futures`) to scrape multiple search categories simultaneously, significantly speeding up the collection process.
* **How to Run**:
    1.  Define the `categories` and search terms you wish to collect inside the notebook.
    2.  Execute all cells in `crawler.ipynb`.
* **Output**: A file named **`image_urls.json`** containing all the scraped URLs.

### Stage 2: Data Preprocessing & Organization (`preprocessing.ipynb`)

This notebook takes the raw URLs from the previous stage and turns them into a clean, structured dataset.

* **Objective**: To download, validate, clean, and organize the images.
* **Methodology**:
    1.  **Downloading**: Reads the `image_urls.json` file and downloads each image, using multi-threading and a "polite delay" to avoid server issues.
    2.  **Cleaning**: Performs a quality check on every downloaded image. It automatically deletes corrupted files and any images smaller than 50x50 pixels (which are likely icons or low-quality thumbnails).
    3.  **Standardizing**: Converts all images to the standard `RGB` format for model compatibility.
    4.  **Organizing**: Splits the cleaned images for each class into `train` and `test` sets, creating the final `data/` directory.
* **How to Run**:
    1.  Ensure `image_urls.json` is present.
    2.  Execute all cells in `preprocessing.ipynb`.
* **Output**: A clean `data/` folder structured for machine learning and a `filename.txt` listing all valid image paths.

### Stage 3: Image Retrieval Engine (`vectorDB-CLIP.ipynb`)

This is the core of the project, where the similarity search happens.

* **Objective**: To take a query image and find the most visually similar images from the custom-built dataset.
* **Methodology**:
    1.  **Feature Extraction**: Uses a pre-trained **CLIP (Contrastive Language-Image Pre-Training)** model to convert every image in the `data/` folder into a high-dimensional feature vector (embedding). These embeddings capture the semantic meaning of the image content.
    2.  **Vector Database**: The generated embeddings are stored and indexed in a **ChromaDB** collection. This database is optimized for extremely fast similarity searches over vector data.
    3.  **Similarity Search**: When a user provides a query image, it is converted into an embedding using CLIP. This embedding is then used to query the ChromaDB collection. **Cosine Similarity** is used as the distance metric to find the vectors (and corresponding images) that are closest in the high-dimensional space.
* **How to Run**:
    1.  Ensure the `data/` folder has been created by the preprocessing notebook.
    2.  Execute all cells in `vectorDB-CLIP.ipynb`.
    3.  Provide a path to a query image to see the results.

## 🛠️ Technologies Used

* **Data Collection**: `Selenium`, `BeautifulSoup4`
* **Data Processing**: `Pillow (PIL)`, `NumPy`
* **ML/Vector Search**: `open-clip-torch`, `chromadb`
* **Core Python**: `concurrent.futures`, `os`, `shutil`, `json`
