# Complete Step-by-Step Machine Learning Pipeline Guide

This document provides an exhaustive, sequential walkthrough of all 30 steps executed in the YouTube Live Network Traffic Classification notebook.

---

## Sequential Breakdown of Steps

### Step 1: Data Acquisition & Merging
* **Goal:** Load separate raw data files and consolidate them into a unified frame.
* **What we did:** Defined absolute storage paths for 5 raw CSV partitions. Iterated through multiple structural text-encoding formats (`utf-8`, `utf-8-sig`, `cp1252`) to bypass decode exceptions, merged all files safely into a consolidated structure of over **6.2 million rows**, and wrote the result out to a local disk storage as `youtube_live_merged.csv`.

### Step 2: Triggering Merged File Download
* **Goal:** Expose the consolidated dataframe file for localized copy retrieval.
* **What we did:** Employed the `google.colab.files` library to directly trigger an automatic browser session download stream of the newly compiled merged CSV file.

### Step 3: Inspecting Dataset Structural Properties
* **Goal:** Verify loading accuracy and structural integrity.
* **What we did:** Loaded `youtube_live_merged.csv` back into active memory space and printed the precise shape parameter matrix, verifying the row dimensions of (6,263,188, 7).

### Step 4: Accessing Column Attributes
* **Goal:** Verify attribute names present.
* **What we did:** Extracted and listed the data columns: `No.`, `Time`, `Source`, `Destination`, `Protocol`, `Length`, and `Info`.

### Step 5: Auditing Field Data Types
* **Goal:** Understand value representations.
* **What we did:** Discovered that most network flow metadata fields are parsed as generic objects (strings) while the `No.` and `Length` metrics are loaded as 64-bit integers.

### Step 6: Querying Class Information Properties
* **Goal:** Retrieve memory and schema details.
* **What we did:** Ran `df_merged.info()` to print structural indices, column counts, non-null specifications, and confirmed a memory footprint of over 334 megabytes.

### Step 7: Displaying Basic Statistics
* **Goal:** Perform high-level statistical evaluation of dataset values.
* **What we did:** Used `describe(include='all')` to review quantitative attributes, finding categorical fields like `Protocol` had highly dominant classes.

### Step 8: Auditing Missing Elements
* **Goal:** Quantify null or empty data points.
* **What we did:** Calculated column null counts using `isnull().sum()`. Identified 463 missing values specifically located inside the unstructured `Info` column.

### Step 9: Detecting Duplicate Instances
* **Goal:** Ensure data uniqueness.
* **What we did:** Evaluated rows for duplicate occurrences using `duplicated().sum()`, confirming the dataset contained no duplicate entries.

### Step 10: Handling Missing Field Values
* **Goal:** Cleanse missing attributes safely.
* **What we did:** Dropped any row with missing essential headers (Source, Destination, Time) and filled remaining missing text payloads in the `Info` column with the default string placeholder `'Unknown'`.

### Step 11: Inspecting Target Protocol Categories
* **Goal:** Explore classification label properties.
* **What we did:** Printed the set of unique protocols in the target column and computed class frequencies, revealing extreme imbalances (e.g., millions of `GQUIC` packets versus 1 `ar_drone` packet).

### Step 12: Visualizing Target Distribution
* **Goal:** Construct a visual class representation.
* **What we did:** Configured a Seaborn countplot sorted by label frequency to graphically present the distribution of network protocols.

### Step 13: Analyzing Packet Lengths
* **Goal:** Extract descriptive packet length metrics.
* **What we did:** Computed basic length properties including minimum size (40 bytes), maximum size (10,000 bytes), and the overall mathematical mean size (1080.48 bytes).

### Step 14: Converting Time Column
* **Goal:** Format temporal strings to datetime representations.
* **What we did:** Parsed the string `Time` timestamp to standard datetime objects, dropped invalid parsed datetimes, sorted the entire dataset chronologically, and re-indexed the dataframe.

### Step 15: Extracting Time-Based Features
* **Goal:** Derive numerical features for the model.
* **What we did:** Extracted the cyclic time units of `Hour`, `Minute`, and `Second` from the timestamp values to capture structural daily variations.

### Step 16: Scaling Packet Length with Min-Max
* **Goal:** Map packet sizes to an optimized scale.
* **What we did:** Applied `MinMaxScaler` to bind `Length` properties strictly between 0 and 1, creating a new feature column `Scaled_Length`.

### Step 17: Feature Set and Target Definition
* **Goal:** Isolate parameters for ML algorithms.
* **What we did:** Set target column $y$ to `Protocol` and separated features $X$ by dropping non-feature categories like `Source`, `Destination`, and `Info`.

### Step 18: Checking and Imputing Missing Values in Feature Space
* **Goal:** Assert zero null values are fed to algorithms.
* **What we did:** Verified all features in $X$ were completely free of nulls, ensuring robust model compatibility.

### Step 19: Encoding Target Labels with LabelEncoder
* **Goal:** Convert text labels to integers.
* **What we did:** Applied `LabelEncoder` to transform nominal protocol categories into integer values spanning 0 through 13.

### Step 20: Saving Fully Preprocessed Data
* **Goal:** Save a copy of the finalized, clean dataset.
* **What we did:** Excluded raw unneeded strings and wrote the finalized dataframe out to `youtube_live_preprocessed_final.csv`.

### Step 21: Stratifying and Splitting the Dataset
* **Goal:** Divide data into training (80%) and testing (20%) partitions.
* **What we did:** Removed the single-instance class `ar_drone` to satisfy stratified split bounds. Performed an 80/20 train-test split to ensure class ratios were perfectly preserved across both partitions.

### Step 22: Feature Standardization
* **Goal:** Standardize continuous features to mean=0 and variance=1.
* **What we did:** Converted the datetime column to numerical unix values and transformed both sets with a fitted `StandardScaler`.

### Step 23: Model 1 - Logistic Regression
* **Goal:** Establish a baseline linear classifier.
* **What we did:** Initialized a Logistic Regression model (with `max_iter=1000` for optimization stability), fitted it on scaled training features, and recorded predictions.

### Step 24: Classification Report (Logistic Regression)
* **Goal:** Assess per-class metrics.
* **What we did:** Evaluated precision, recall, and F1-scores, achieving **94.97%** accuracy, while identifying model issues classifying minority classes.

### Step 25: Confusion Matrix (Logistic Regression)
* **Goal:** Locate prediction alignments.
* **What we did:** Plotted a confusion matrix, illustrating that the linear model heavily misclassified minor categories as dominant protocols like `UDP`.

### Step 26: Model 2 - K-Nearest Neighbors (KNN)
* **Goal:** Evaluate neighborhood distance metrics.
* **What we did:** Designed a 5-NN classifier. Trained it on a subset of 50,000 samples to keep execution times and RAM consumption within Google Colab resource boundaries.

### Step 27: Detailed Evaluation (KNN)
* **Goal:** Analyze KNN performance properties on corresponding sub-splits.
* **What we did:** Plotted a confusion matrix and generated a classification report, capturing a strong performance of **98.43%** accuracy.

### Step 28: Model 3 - Decision Tree
* **Goal:** Build an interpretable, non-linear tree classifier.
* **What we did:** Configured a Decision Tree with a depth limit of 10, trained it on the numeric training partition, and evaluated test accuracy (**99.26%**).

### Step 29: Visualizing the Decision Tree Topology
* **Goal:** Visually inspect node decision branches.
* **What we did:** Used `plot_tree` up to depth 3, displaying the structural decision boundaries for features like packet `Length`.

### Step 30: Evaluation of Decision Tree & Model 4 - Random Forest
* **Goal:** Implement and evaluate an ensemble model.
* **What we did:**
  * **Decision Tree Evaluation:** Produced a classification report and confusion matrix.
  * **Random Forest:** Trained a 100-estimator ensemble, achieving the highest project accuracy of **99.59%**.
  * **Deployment Simulation:** Tested on a simulated new packet vector, yielding a correct classification matching the test partition labels.
