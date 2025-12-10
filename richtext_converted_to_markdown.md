Customer Shopping Trends – Segmentation & Subscription Prediction
=================================================================

This project analyzes a retail “Customer Shopping Trends” dataset and builds two main components:

1.  Customer segments (personas) using all available customer and transaction features
    
2.  A subscription prediction model that explains who is likely to subscribe and why
    

The whole workflow is written to be easy to follow, step‑by‑step, and to look and read like a human‑written analysis.

1\. Dataset overview
--------------------

**Rows and columns**

*   3,900 rows and 18 columns
    
*   No missing values and no obvious data quality issues
    

**Columns**

*   Customer ID – unique customer identifier
    
*   Age – customer age (18–70)
    
*   Gender – Male, Female
    
*   Item Purchased – specific item name (Blouse, Jeans, Sandals, etc.)
    
*   Category – Clothing, Accessories, Footwear, Outerwear
    
*   Purchase Amount (USD) – order value (20–100 USD)
    
*   Location – 50 U.S. states
    
*   Size – clothing size (S, M, L, etc.)
    
*   Color – item color
    
*   Season – Spring, Summer, Fall, Winter
    
*   Review Rating – numeric rating (2.5–5.0)
    
*   Subscription Status – Yes / No
    
*   Shipping Type – Free Shipping, Standard, Store Pickup, Express, Next Day Air, 2‑Day Shipping
    
*   Discount Applied – Yes / No
    
*   Promo Code Used – Yes / No
    
*   Previous Purchases – count of past orders (1–50)
    
*   Payment Method – PayPal, Credit Card, Cash, Debit Card, Venmo, etc.
    
*   Frequency of Purchases – Weekly, Fortnightly, Monthly, Annually, etc.
    

This mix of demographics, purchase behavior, promotions, and satisfaction is ideal both for segmentation and for predicting subscription behaviour.​

2\. Data loading and cleaning
-----------------------------

**Goal:** Load the data safely, inspect structure, and ensure it is ready for analysis without losing information.

2.1 Load data
-------------

*   Read the CSV into a DataFrame.
    
*   Inspect the first few rows, overall shape, and basic info (types, memory usage).
    

Result:

*   Shape: (3900, 18)
    
*   All columns present and correctly typed:
    
    *   Numeric: Age, Purchase Amount (USD), Previous Purchases, Review Rating
        
    *   Categorical: the rest
        

2.2 Check data quality
----------------------

*   Count missing values per column → all zeros.
    
*   Check duplicates (optional) → not problematic for analysis.
    

Conclusion: the dataset is **fully complete** and does not require imputation.​

3\. Basic profiling and EDA
---------------------------

**Goal:** Understand who the customers are and how they behave before building models.

3.1 Customer profile
--------------------

*   Average age ≈ **44 years**
    
*   Gender counts: about **2:1 Male to Female** (2652 males, 1248 females).
    
*   Orders spread across 50 locations with slightly higher counts in states like Montana, California, Idaho, Illinois, Alabama.
    

3.2 Product and season behavior
-------------------------------

*   Categories: Clothing (1737 orders), Accessories (1240), Footwear (599), Outerwear (324).
    
*   Season counts are very balanced: Spring (999), Fall (975), Winter (971), Summer (955).
    

3.3 Spending and ratings
------------------------

*   Average purchase amount ≈ **$59.76**.
    
*   Average review rating ≈ **3.75 / 5**, with most ratings between 3.1 and 4.4.
    

3.4 Promotions and loyalty
--------------------------

*   About **43%** of orders have a discount applied.
    
*   Average spend for discounted orders (~$59.28) is very close to non‑discounted (~$60.13).
    
*   Median previous purchases ≈ **25**, indicating customers are generally returning buyers.
    
*   Correlation between previous purchases and current order value is essentially zero (≈0.008), so loyal customers buy often but not necessarily bigger baskets.
    

These descriptive steps establish a clear, human‑readable picture of the customer base and their spending patterns.​

4\. Column renaming and target creation
---------------------------------------

**Goal:** Make the dataset easier to work with in code and create a clear target for the prediction task.

4.1 Rename columns
------------------

*   Convert spaces to underscores and remove special characters:
    
    *   Purchase Amount (USD) → Purchase\_Amount\_USD
        
    *   Previous Purchases → Previous\_Purchases
        
    *   Subscription Status → Subscription\_Status
        
    *   Frequency of Purchases → Frequency\_of\_Purchases
        
    *   etc.
        

4.2 Create binary subscription target
-------------------------------------

*   New column: is\_subscribed = 1 if Subscription\_Status == "Yes", otherwise 0.
    
*   Distribution: 2,847 non‑subscribers (0) and 1,053 subscribers (1).
    

This sets up a clean target variable for the classification model later.​

5\. Feature preparation for segmentation
----------------------------------------

**Goal:** Build a feature matrix that uses **all columns except ID and the target** for clustering.

5.1 Choose feature set
----------------------

Features used:

*   Numeric: Age, Purchase\_Amount\_USD, Previous\_Purchases, Review\_Rating
    
*   Categorical: Gender, Item\_Purchased, Category, Location, Size, Color, Season, Shipping\_Type, Discount\_Applied, Promo\_Code\_Used, Payment\_Method, Frequency\_of\_Purchases
    

5.2 Encoding and scaling
------------------------

*   Numeric features → standardized with StandardScaler.
    
*   Categorical features → one‑hot encoded with OneHotEncoder(handle\_unknown="ignore").
    
*   Combined using a column‑wise transformer so both types of features form a single matrix.​
    

Result:

*   X\_prepared has shape (3900, 141) – 3,900 customers, 141 encoded features.
    

6\. Customer segmentation with k‑means
--------------------------------------

**Goal:** Convert raw customers into a small set of interpretable segments (personas).

6.1 Clustering
--------------

*   Algorithm: KMeans with n\_clusters=4, random\_state=42, n\_init=10.
    
*   Fit on X\_prepared.
    
*   Add segment labels back to the original prepared DataFrame.
    

Segment sizes:

*   Segment 0: 826 customers
    
*   Segment 1: 1,090 customers
    
*   Segment 2: 851 customers
    
*   Segment 3: 1,133 customers
    

6.2 Numeric segment profile
---------------------------

For each segment, compute mean values of:

*   Age
    
*   Purchase\_Amount\_USD
    
*   Previous\_Purchases
    
*   Review\_Rating
    
*   is\_subscribed (subscription rate)
    

Results:

*   **Segment 0**
    
    *   Age ≈ 57.67
        
    *   Purchase\_Amount\_USD ≈ 59.88
        
    *   Previous\_Purchases ≈ 26.61
        
    *   Review\_Rating ≈ 3.75
        
    *   is\_subscribed ≈ 0.64
        
*   **Segment 1**
    
    *   Age ≈ 44.43
        
    *   Purchase\_Amount\_USD ≈ 39.30
        
    *   Previous\_Purchases ≈ 23.91
        
    *   Review\_Rating ≈ 3.66
        
    *   is\_subscribed ≈ 0.00
        
*   **Segment 2**
    
    *   Age ≈ 31.01
        
    *   Purchase\_Amount\_USD ≈ 58.70
        
    *   Previous\_Purchases ≈ 24.90
        
    *   Review\_Rating ≈ 3.73
        
    *   is\_subscribed ≈ 0.62
        
*   **Segment 3**
    
    *   Age ≈ 43.61
        
    *   Purchase\_Amount\_USD ≈ 80.17
        
    *   Previous\_Purchases ≈ 26.16
        
    *   Review\_Rating ≈ 3.85
        
    *   is\_subscribed ≈ 0.00
        

6.3 Categorical preferences
---------------------------

For each segment, find the most frequent:

*   Category → Clothing for all segments
    
*   Season →
    
    *   Segment 0: Spring
        
    *   Segment 1: Fall
        
    *   Segment 2: Summer
        
    *   Segment 3: Fall
        

6.4 Human‑friendly segment names
--------------------------------

From these profiles, the segments can be interpreted as:

1.  **Segment 0 – Older loyal subscribers**
    
    *   Older customers (~58), moderate spend, strong subscription rate (~64%), slightly higher purchase history, favour spring clothing.
        
2.  **Segment 1 – Low‑value non‑subscribers**
    
    *   Mid‑40s, low order values (~$39), fewer past purchases, almost no subscriptions, fall clothing preference.
        
3.  **Segment 2 – Young loyal subscribers**
    
    *   Early‑30s, average spend (~$59), good purchase history, high subscription rate (~62%), summer clothing preference.
        
4.  **Segment 3 – High‑spend non‑subscribers**
    
    *   Mid‑40s, highest order value (~$80), strong purchase history, almost zero subscriptions, fall clothing preference.
        

These personas make the clusters understandable for non‑technical stakeholders.​

7\. Subscription prediction model
---------------------------------

**Goal:** Predict whether a customer is subscribed (is\_subscribed) using all customer and transaction features.

7.1 Feature set and preprocessing
---------------------------------

*   Use the same 16 features as clustering (no Customer\_ID, no is\_subscribed).
    
*   Numeric vs categorical split identical to the segmentation step.
    
*   Build a preprocessing + model pipeline:
    
    *   Preprocessing: column transformer (scaler + one‑hot encoder)
        
    *   Model: LogisticRegression(max\_iter=2000)
        

This pipeline ensures that the same transformations are applied consistently during training and testing.​

7.2 Train / test split
----------------------

*   Split data into train and test:
    
    *   75% train, 25% test
        
    *   Stratified by is\_subscribed to keep class balance
        

7.3 Model training and evaluation
---------------------------------

*   Fit the pipeline on the training set.
    
*   Predict on the test set and compute confusion matrix and classification report.
    

**Confusion matrix** (rows = true, columns = predicted):

*   \[,\[ 49, 214\]\]
    

Interpretation:

*   596 true non‑subscribers correctly predicted as non‑subscribers
    
*   116 non‑subscribers wrongly predicted as subscribers
    
*   49 subscribers missed (predicted non‑subscriber)
    
*   214 subscribers correctly predicted
    

**Classification report**

*   Class 0 (not subscribed):
    
    *   Precision: 0.924
        
    *   Recall: 0.837
        
    *   F1: 0.878
        
*   Class 1 (subscribed):
    
    *   Precision: 0.648
        
    *   Recall: 0.814
        
    *   F1: 0.722
        
*   Overall accuracy: **0.831** (83.1%)
    
*   Macro F1: 0.800
    

This is clearly better than just guessing the majority class and handles both classes reasonably well, especially recall for subscribers (0.814).​

8\. Interpreting feature importance
-----------------------------------

**Goal:** Turn the logistic regression coefficients into a clear story: what increases or decreases subscription probability?

8.1 Coefficients extraction
---------------------------

Steps:

*   Pull the trained logistic regression from the pipeline.
    
*   Extract one‑hot encoder feature names for categorical variables.
    
*   Combine numeric and categorical names into a single feature list.
    
*   Take coefficients for the positive class (subscriber = 1).
    
*   Sort to find top positive (increase subscription) and top negative (decrease subscription) features.
    

8.2 Top positive drivers
------------------------

Features with the **largest positive** coefficients:

*   Promo\_Code\_Used\_Yes (coef ≈ 1.783)
    
*   Discount\_Applied\_Yes (coef ≈ 1.783)
    
*   Locations: Colorado, Delaware, Nevada, Montana, Arizona, Virginia, Kentucky, Rhode Island, California, Missouri
    
*   Gender\_Male
    
*   Item\_Purchased\_Jeans
    
*   Color\_Turquoise
    

Interpretation:

*   Customers who **use promo codes and discounts** are much more likely to be subscribers.
    
*   Certain states show stronger subscription tendencies, suggesting geographic patterns.
    
*   Male customers, jeans buyers, and customers attracted to some colours (like turquoise) skew more toward subscription.
    

8.3 Top negative drivers
------------------------

Features with the **largest negative** coefficients:

*   Promo\_Code\_Used\_No
    
*   Discount\_Applied\_No
    
*   Locations: Wisconsin, Indiana, Texas, Oregon, Alabama, New Mexico, Maryland, Connecticut
    
*   Gender\_Female
    
*   Item\_Purchased\_Blouse, Item\_Purchased\_Jewelry, Item\_Purchased\_T-shirt
    
*   Color\_Violet
    

Interpretation:

*   Customers who **never use promotions** are much less likely to be subscribers.
    
*   Some locations and product choices (e.g., blouses, jewelry, T‑shirts) correlate with lower subscription likelihood.
    
*   Female customers, on average, subscribe less often in this dataset, even after controlling for other variables.
    

These findings translate into plain rules such as:

> “Promo‑loving jeans buyers in certain states are prime subscription candidates, while blouse or jewelry buyers in several other states are less likely to subscribe.”