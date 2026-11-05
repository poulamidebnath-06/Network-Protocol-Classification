# YouTube Live Network Protocol Classification Project

This project merges, cleans, analyzes, and uses machine learning to classify network traffic protocols from YouTube Live streams.

Here is an explanation of every step in the project, including a breakdown of the code in simple, everyday English:

---

### Step-by-Step Explanation & Code Breakdowns

#### 1. Merging the Data 
* **What it does:** Reads 5 separate YouTube Live network trace CSV files from Google Drive and combines them into one big file called `youtube_live_merged.csv`.
* **Code Breakdown:**
  * `pd.read_csv(f, encoding='utf-8')` opens and reads each file safely.
  * `pd.concat(dfs, ignore_index=True)` stacks all individual tables vertically on top of each other.
  * `merged_df.to_csv(...)` saves everything back into a single unified CSV file.

#### 2. Triggering Download 
* **What it does:** Triggers an automatic browser download of the newly merged CSV file to your local computer.
* **Code Breakdown:**
  * `files.download(merged_filename)` uses Colab's built-in tools to send the file directly to your downloads folder.

#### 3. Inspecting the Dataset 
* **What it does:** Loads the dataset, checks its shape (rows/columns), inspects variable types, and flags empty cells.
* **Code Breakdown:**
  * `df_merged.shape` tells us we have over 6.2 million rows and 7 columns.
  * `df_merged.isnull().sum()` lists which columns have missing values (only the "Info" column has 463 missing values).
  * `df_merged.duplicated().sum()` checks if there are any repetitive exact row copies (there are none).

#### 4. Handling Missing Values 
* **What it does:** Drops rows missing vital connection details and fills empty informational fields.
* **Code Breakdown:**
  * `dropna(subset=['Source', 'Destination', 'Time'])` throws away any row where critical IP addresses or timestamps are blank.
  * `fillna('Unknown')` fills missing descriptions in the "Info" column with the text "Unknown".

#### 5. Understanding the Target & Plotting 
* **What it does:** Counts how many times each protocol (GQUIC, UDP, TCP) appears and draws a visual bar graph.
* **Code Breakdown:**
  * `df_merged["Protocol"].value_counts()` totals each category (GQUIC is the most frequent with 4.3+ million instances).
  * `sns.countplot(data=df_merged, x="Protocol")` draws a neat bar chart to visualize the distribution.

#### 6. Analyzing Packet Length 
* **What it does:** Analyzes basic mathematical statistics of packet size lengths.
* **Code Breakdown:**
  * `df_merged['Length'].describe()` shows the minimum size (40 bytes), maximum size (10,000 bytes), and average size (1,080 bytes).

#### 7. Converting Times & Sorting
* **What it does:** Changes the text-based timestamps to actual computer-readable date-times and sorts them.
* **Code Breakdown:**
  * `pd.to_datetime(...)` translates text like "2022-05-30..." into real timestamp values.
  * `sort_values(by='Time')` arranges the stream timeline chronologically from start to finish.

#### 8. Extracting Time-Based Features 
* **What it does:** Extracts separate Hour, Minute, and Second values from the datetime column.
* **Code Breakdown:**
  * `df_merged['Time'].dt.hour` grabs just the hour digit (0-23) of each packet so the machine can find patterns linked to the time of day.

#### 9. Scaling Packet Lengths 
* **What it does:** Scales the packet size so it falls between 0 and 1.
* **Code Breakdown:**
  * `scaler.fit_transform(df_merged[['Length']])` maps packet lengths (ranging from 40 to 10,000) into values between 0.0 and 1.0.

#### 10. Setting up Features (X) and Target (y) 
* **What it does:** Separates the values used for making predictions (Features) from the protocol we want to predict (Target).
* **Code Breakdown:**
  * `X = df_merged[X_columns]` contains columns like No., Time, Hour, Minute, and Scaled_Length.
  * `y = df_merged['Protocol']` holds the answers we want the model to learn to predict.

#### 11. Imputing Missing Feature Values 
* **What it does:** Double-checks that no feature columns have empty values.
* **Code Breakdown:**
  * `X.fillna(X.median())` fills in any rare blank fields with the column's middle value so the model doesn't crash during training.

#### 12. Encoding Target Labels 
* **What it does:** Converts categorical string names (like 'GQUIC', 'UDP') into numeric IDs (like 4, 12).
* **Code Breakdown:**
  * `le.fit_transform(y)` assigns a unique number to every unique protocol name.

#### 13. Saving the Clean Preprocessed Dataset
* **What it does:** Drops redundant raw columns to create a lightweight, processed file.
* **Code Breakdown:**
  * `final_df.to_csv('youtube_live_preprocessed_final.csv')` saves our optimized and cleaned data for future use.

#### 14. Splitting Train and Test Sets
* **What it does:** Splits the dataset into 80% for training models and 20% for verifying accuracy.
* **Code Breakdown:**
  * `y != 'ar_drone'` filters out a single-occurrence class that cannot be split stratified.
  * `train_test_split(..., test_size=0.20, stratify=...)` splits the data randomly but maintains equal target proportions in both sets.

#### 15. Standard Feature Scaling 
* **What it does:** Converts timestamps to seconds and applies standard normalization.
* **Code Breakdown:**
  * `StandardScaler().fit_transform(X_train_numeric)` rescales all features to have a mean of 0 and variance of 1, helping optimization algorithms run efficiently.

#### 16. Model 1: Logistic Regression
* **What it does:** Trains a linear classifier baseline model.
* **Code Breakdown:**
  * `logistic_model.fit(X_train_scaled, y_train)` trains the model, achieving 94.97% accuracy.
  * `sns.heatmap(cm, annot=True)` plots a confusion matrix showing exactly what was predicted correctly vs incorrectly.

#### 17. Model 2: K-Nearest Neighbors 
* **What it does:** Classifies packets based on how similar they are to neighboring samples.
* **Code Breakdown:**
  * `knn_model.fit(X_train_scaled[:50000])` trains on a small subset to optimize execution speed, scoring 98.43% accuracy.

#### 18. Model 3: Decision Tree
* **What it does:** Builds a branching flow-chart model of rules to classify network packets.
* **Code Breakdown:**
  * `dt_model.fit(X_train_numeric, y_train)` trains the tree, scoring 99.26% accuracy.
  * `plot_tree(...)` displays the actual structure of the first few splits in our decision tree.

#### 19. Model 4: Random Forest 
* **What it does:** Trains multiple decision trees together (ensemble method) to get the best overall performance.
* **Code Breakdown:**
  * `rf_model.fit(...)` trains 100 decision trees in parallel, reaching our highest accuracy score of **99.59%**.

#### 20. Comparing Models & Importance 
* **What it does:** Builds comparison bar charts and highlights which features were the most valuable.
* **Code Breakdown:**
  * `rf_model.feature_importances_` extracts feature weights. Time and Hour are shown to be the most critical characteristics for classification.

#### 21. Detailed Random Forest Evaluation 
* **What it does:** Displays the detailed classification report and heatmap for our best model.
* **Code Breakdown:**
  * `classification_report(y_test, y_pred_rf)` measures Precision and Recall for individual protocols (most classes score perfect 1.00s!).

#### 22. Custom Packet Demonstration
* **What it does:** Feeds a simulated network packet through the training pipeline to check if the model can classify new, unseen manual entries.
* **Code Breakdown:**
  * Scales the manual input values using the original scaler and passes them to `rf_model.predict(new_packet_processed)`, which successfully outputs **`GQUIC`**.

#### 23. Verifying Correctness on Real Test Data 
* **What it does:** Automatically queries a real, known GQUIC sample directly from the test split to prove the prediction accuracy on screen.
* **Code Breakdown:**
  * Finds a row in `X_test_numeric` where the actual protocol is GQUIC, predicts on those features, and evaluates it.
  * Prints: **`prediction : GQUIC is a correct prediction`**.
