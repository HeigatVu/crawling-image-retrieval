# Web Scraping & Dataset Creation Pipeline for Machine Learning

This repository contains a complete, automated pipeline for building a custom image dataset for machine learning tasks. The scripts handle everything from finding and downloading images from the web to cleaning, processing, and organizing them into a structured `train`/`test` format.

This project was developed to create a personalized dataset for an [Image Retrieval project](https://github.com/HeigatVu/image-retrieval), which I code previously but is designed to be a standalone, reusable pipeline.

## 📋 Pipeline Overview

The entire process is broken down into four distinct, automated stages:

1.  **URL Scraping**: Programmatically browses a target website (Flickr) to find and collect thousands of direct image URLs based on a list of specified search terms.
2.  **Image Downloading**: Downloads the actual image files from the collected URLs, handling potential errors and organizing the raw images into a nested folder structure.
3.  **Data Cleaning**: Validates the downloaded images, removing corrupted files, tiny thumbnails, and standardizing image formats to ensure a high-quality dataset.
4.  **Dataset Organization**: Automatically splits the cleaned images into `train` and `test` sets for each class, creating the final directory structure required by most deep learning frameworks.

---

## ⚙️ How It Works: A Step-by-Step Guide

### Step 1: Scraping Image URLs from Flickr

A powerful web scraper was built to gather image sources automatically.

* **Target Website**: `flickr.com` was chosen for its vast collection of high-quality, user-submitted photos.
* **Core Technology**:
    * **`Selenium`**: Used to control a web browser programmatically. This is crucial for interacting with modern, dynamic websites like Flickr that use "lazy loading" (where content is loaded as you scroll) and require clicking "Load more" buttons to view all results.
    * **`BeautifulSoup`**: Used to parse the page's HTML source code and precisely extract the `src` attribute from `<img>` tags, which contains the direct URL to the image file.
* **High-Speed Scraping**: The script uses Python's `concurrent.futures` module to implement multi-threading. This allows it to scrape multiple search terms (e.g., "Cat", "Dog", "Monkey") simultaneously, dramatically reducing the time needed to collect thousands of URLs.
* **Output**: The final output of this stage is a single `image_urls.json` file. This file contains a dictionary where keys are the search categories and values are lists of all the image URLs found for that category.

### Step 2: Downloading Images from URLs

This script takes the `image_urls.json` file and downloads the images.

* **Robust Downloader**: The script iterates through every URL for every category.
* **Efficient & Polite**:
    * **Multi-threading**: Just like the scraper, the downloader uses multiple threads to download several images at once, maximizing network usage.
    * **Polite Delay**: To prevent overwhelming the Flickr servers and avoid getting our IP address temporarily blocked for making too many requests, a "polite delay" of a few seconds is added between batches of downloads. This is critical for responsible scraping.
* **Initial Organization**: As images are downloaded, they are automatically saved into a structured directory: `Dataset/{category}/{term}/{image_filename}.jpg`.

### Step 3: Cleaning the Raw Dataset

A raw dataset from the web is often messy. This script ensures data quality.

* **Image Validation**: The script iterates through every file in the `Dataset` directory.
* **Automated Cleaning Actions**:
    1.  **Delete Corrupted Files**: It attempts to open each file. If a file is not a valid image or is corrupted, it is automatically deleted.
    2.  **Remove Small Images**: Images with dimensions smaller than 50x50 pixels are removed, as these are typically low-quality thumbnails or icons unsuitable for training.
    3.  **Standardize Format**: All valid images are converted and saved in the standard `RGB` format to ensure consistency for the model training phase.

### Step 4: Organizing the Final Dataset

The final script prepares the clean data for a machine learning model.

* **Train/Test Split**: The script reads the list of all cleaned image files and, for each class (e.g., "Cat"), it systematically moves a specified number of images into a `data/train/Cat` folder and the remaining images into a `data/test/Cat` folder.
* **Final Output**: The pipeline's final product is a `data` directory, perfectly structured and ready to be fed into a deep learning framework like PyTorch or TensorFlow.

## 🛠️ Technologies Used

* **Python 3**
* **Selenium**: For browser automation and handling dynamic web content.
* **BeautifulSoup4**: For parsing HTML and extracting data.
* **Pillow (PIL)**: For image processing (opening, converting, checking dimensions).
* **Standard Libraries**: `concurrent.futures` (for multi-threading), `os`, `shutil`, `json`, `urllib`.

## 🚀 How to Run the Pipeline

1.  **Setup & Installation**:
    * Clone this repository.
    * Install the required Python packages:
        ```bash
        pip install selenium beautifulsoup4 Pillow
        ```
    * Download and set up the appropriate `WebDriver` for your browser (e.g., `chromedriver` for Google Chrome) and ensure it's in your system's PATH.

2.  **Configure the Scraper**:
    * Open the scraping script (`scrape_urls.py`).
    * Modify the `categories` dictionary to define the search terms you want to collect images for.

3.  **Execute the Scripts in Order**:
    * **1. Run the Scraper**:
        ```bash
        python scrape_urls.py
        ```
        This will create the `image_urls.json` file.

    * **2. Run the Downloader**:
        ```bash
        python download_images.py
        ```
        This will download all images into the `Dataset/` directory.

    * **3. Run the Cleaner**:
        ```bash
        python clean_dataset.py
        ```
        This will process the images inside the `Dataset/` directory.

    * **4. Run the Organizer**:
        ```bash
        python organize_dataset.py
        ```
        This will create the final `data/` directory with `train/` and `test/` subfolders.
