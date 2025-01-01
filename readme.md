# **E-commerce Recommendation System**

![E-commerce](images/ecommerce.png)

## **1. Project Overview**

Olist, the largest department store in Brazilian marketplaces, bridges the gap between small businesses and a wide array of sales channels through a seamless and centralized platform. Merchants on Olist can sell their products easily while leveraging Olist's logistics network for efficient delivery. To enhance customer satisfaction and streamline their experience, Olist collects feedback through post-purchase surveys, providing valuable insights into the shopping experience [(Stalidis, et. al, 2023)](https://doi.org/10.3390/su152316151). These customer interactions present an opportunity to develop personalized recommendations, driving both customer engagement and seller success [(Abdul Hussien, Rahma, & Abdulwahab, 2021)](https://doi.org/10.3390/su131910786).

The project focuses on building a recommendation system using content-based filtering and collaborative filtering approaches. A robust recommendation system is essential to enhance the shopping experience, increase customer retention, and support Olist’s mission of empowering small businesses. Leveraging the collected data, the project aims to deliver accurate and personalized recommendations, drawing insights from customer feedback and product attributes to improve customer satisfaction and overall marketplace performance.

## **2. Business Understanding**

### **2.1. Problem Statements**

Olist aims to enhance customer satisfaction and drive sales by offering personalized product recommendations. However, the current system lacks a robust mechanism to analyze customer feedback and product attributes for tailored suggestions, potentially leading to missed opportunities for customer retention and merchant success.  

### **2.2. Goals**

The primary goal is to develop a recommendation system that delivers accurate and personalized product suggestions to customers, enhancing their shopping experience while increasing sales for merchants.  

### **2.3. Solution statements**

1. **Content-Based Filtering**: Analyze product attributes and customer preferences to suggest products similar to those they have purchased.
2. **Collaborative Filtering**: Leveraging patterns in customer interactions and purchase history, recommend products based on the behavior and preferences of similar customers.  

## **3. Data Understanding**

### **3.1. Datasets**

The dataset used for the project is the [**Brazilian E-Commerce Public Dataset by Olist** (kaggle, 2018)](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce). It contains approximately 100,000 orders made at Olist Store between 2016 and 2018. The dataset is anonymized and ideal for developing a recommendation system as it provides valuable insights into customer preferences, product details, and purchasing behavior.

**Tables Used for the Recommendation System**  
1. **Customers**: Contains customer IDs and unique customer identifiers, which are critical for understanding individual purchasing behavior and linking reviews and orders.  
2. **Orders**: Provides order information, including order IDs and customer IDs, which helps in tracking purchasing patterns.  
3. **Order Items**: Includes details about the products purchased in each order, such as product IDs and price, essential for mapping product preferences.  
4. **Order Reviews**: Captures customer feedback in the form of review scores, crucial for building collaborative filtering models by identifying customer satisfaction and preferences.  
5. **Products**: Contains product-level information, including product IDs, names, and physical attributes, which can be used for content-based filtering.  
6. **Product Category**: Maps product categories name to English.

The relationships between these tables are illustrated in the dataset's schema. For example, Order Items links orders to specific products, while Customers connects unique buyers to their respective orders and reviews. Understanding dataset structure is essential for designing a recommendation system that effectively integrates both content-based filtering and collaborative filtering techniques.

![Schema](images/image.png)

#### **3.1.1. Customers**

| Index | customer_id                        | customer_unique_id               |
|-------|------------------------------------|-----------------------------------|
| 0     | 06b8999e2fba1a1fbc88172c00ba8bc7  | 861eff4711a542e4b93843c6dd7febb0 |

| Index | Column               | Type   | Null | Unique | Unique Values                                     | Min  | Q1   | Median | Q3   | Max  |
|-------|----------------------|--------|------|--------|--------------------------------------------------|------|------|--------|------|------|
| 0     | customer_id          | object | 0    | 99441  | [06b8999e2fba1a1fbc88172c00ba8bc7, 18955e83d33...] | NaN  | NaN  | NaN    | NaN  | NaN  |
| 1     | customer_unique_id   | object | 0    | 96096  | [861eff4711a542e4b93843c6dd7febb0, 290c77bc529...] | NaN  | NaN  | NaN    | NaN  | NaN  |

**Customers Table Information**
- The `customers_df` table consists of **99,441 records** and provides customer-level information.  
- It includes two features:  
  1. **`customer_id`**: A unique identifier for each customer’s order, used to link with the `orders_df` table. There are **99,441 unique values.**
  2. **`customer_unique_id`**: A unique identifier for each customer across all orders. With **96,096 unique values**, indicates that some customers placed multiple orders.  
- The table contains **no missing values.**

**Relationship in the Schema**
- The `customer_id` field connects the `customers_df` table to the `orders_df` table, enabling the tracking of customer behavior and linking orders to specific customers.  

#### **3.1.2. Order Items**

| Index | order_id                          | product_id                        | price  |
|-------|-----------------------------------|------------------------------------|--------|
| 0     | 00010242fe8c5a6d1ba2dd792cb16214 | 4244733e06e7ecb4970a6e2683c13e61 | 58.9   |

| Index | Column      | Type    | Null | Unique | Unique Values                                      | Min   | Q1    | Median | Q3    | Max    |
|-------|-------------|---------|------|--------|---------------------------------------------------|-------|-------|--------|-------|--------|
| 0     | order_id    | object  | 0    | 98666  | [00010242fe8c5a6d1ba2dd792cb16214, 00018f77f2f...] | NaN   | NaN   | NaN    | NaN   | NaN    |
| 1     | product_id  | object  | 0    | 32951  | [4244733e06e7ecb4970a6e2683c13e61, e5f2d52b802...] | NaN   | NaN   | NaN    | NaN   | NaN    |
| 2     | price       | float64 | 0    | 5968   | [58.9, 239.9, 199.0, 12.99, 199.9, 21.9, 19.9,...] | 0.85  | 39.9  | 74.99  | 134.9 | 6735.0 |

**Order Items Table Information**
- The `order_items_df` table consists of **112,650 records** and provides product-level details for customer orders.  
- It includes three features:  
  1. **`order_id`**: A unique identifier for each order, used to link with the `orders_df` table. There are **98,666 unique values**, indicating that some orders contain multiple products.  
  2. **`product_id`**: A unique identifier for each product, used to link with the `products_df` table. There are **32,951 unique products** purchased across all orders.  
  3. **`price`**: The price of the purchased product in Brazilian Real. Prices range from **0.85** to **6,735.00**, with a median price of **74.99**.  
- The table contains **no missing values.**

**Relationship in the Schema**
- The `order_id` column connects the `order_items_df` table to the `orders_df` table, enabling the identification of products within each order.  
- The `product_id` column links the `order_items_df` table to the `products_df` table, allowing access to product attributes and categories for each product in an order.

#### **3.1.3. Order Reviews**

| Index | order_id                          | review_score |
|-------|-----------------------------------|--------------|
| 0     | 73fc7af87114b39712e6da79b0a377eb | 4            |

| Index | Column         | Type   | Null | Unique | Unique Values                              | Min | Q1 | Median | Q3 | Max |
|-------|----------------|--------|------|--------|-------------------------------------------|-----|----|--------|----|-----|
| 0     | order_id       | object | 0    | 98673  | [73fc7af87114b39712e6da79b0a377eb, a548...] | NaN | NaN | NaN    | NaN | NaN |
| 1     | review_score   | int64  | 0    | 5      | [4, 5, 1, 3, 2]                           | 1   | 4  | 5      | 5  | 5   | 

**Order Reviews Table Information**
- The `order_reviews_df` table consists of **99,224 records** and provides customer feedback on orders.  
- It includes two features:  
  1. **`order_id`**: A unique identifier for each order, used to link with the `orders_df` table. There are **98,673 unique values**, indicating that most orders have corresponding reviews.  
  2. **`review_score`**: A numerical score given by customers, ranging from **1** (lowest satisfaction) to **5** (highest satisfaction). The median and upper quartile scores are **5**, showing a tendency toward positive feedback.  
- No missing values exist in the table, ensuring that all reviews are complete.  
- The distribution of review scores suggests an overall positive sentiment among customers.  

**Relationship in the Schema**  
- The `order_id` column connects the `order_reviews_df` table to the `orders_df` table, linking reviews to their corresponding orders. The relationship is crucial for analyzing customer satisfaction in relation to specific orders.

#### **3.1.4. Orders**

| Index | order_id                          | customer_id                          |
|-------|-----------------------------------|--------------------------------------|
| 0     | e481f51cbdc54678b7cc49136f2d6af7 | 9ef432eb6251297304e76186b10a928d     |

| Index | Column      | Type   | Null | Unique | Unique Values                              | Min | Q1 | Median | Q3 | Max |
|-------|-------------|--------|------|--------|-------------------------------------------|-----|----|--------|----|-----|
| 0     | order_id    | object | 0    | 99441  | [e481f51cbdc54678b7cc49136f2d6af7, 53cd...] | NaN | NaN | NaN    | NaN | NaN |
| 1     | customer_id | object | 0    | 99441  | [9ef432eb6251297304e76186b10a928d, b083...] | NaN | NaN | NaN    | NaN | NaN | 

**Orders Table Information**
- The `orders_df` table consists of **99,441 records** and provides information about customer orders.  
- It includes two features:  
  1. **`order_id`**: A unique identifier for each order. There are **99,441 unique values**, indicating that each order is distinct.  
  2. **`customer_id`**: A unique identifier for each customer associated with an order, used to link with the `customers_df` table. There are **99,441 unique values**, ensuring that every order has a corresponding customer.  
- The table contains **no missing values**, making it reliable for linking orders to customers and other related data.  

**Relationship in the Schema**  
- The `order_id` column connects the `orders_df` table to the `order_items_df` and `order_reviews_df` tables, enabling the linkage of orders to specific products and customer reviews.  
- The `customer_id` column connects the `orders_df` table to the `customers_df` table, linking each order to the customer who placed it.  

#### **3.1.5. Products**

| Index | product_id                        | product_category_name | product_name_lenght | product_description_lenght | product_photos_qty | product_weight_g | product_length_cm | product_height_cm | product_width_cm |
|-------|-----------------------------------|-----------------------|---------------------|----------------------------|--------------------|------------------|-------------------|-------------------|------------------|
| 0     | 1e9e8ef04dbcff4541ed26657ea517e5 | perfumaria            | 40.0                | 287.0                      | 1.0                | 225.0            | 16.0              | 10.0              | 14.0             |

| Index | Column                      | Type    | Null | Unique | Unique Values                               | Min   | Q1   | Median | Q3   | Max     |
|-------|-----------------------------|---------|------|--------|--------------------------------------------|-------|-------|--------|-------|---------|
| 0     | product_id                  | object  | 0    | 32951  | [1e9e8ef04dbcff4541ed26657ea517e5, 3aa...] | NaN   | NaN   | NaN    | NaN   | NaN     |
| 1     | product_category_name       | object  | 610  | 73     | [perfumaria, artes, esporte_lazer, bebes...] | NaN   | NaN   | NaN    | NaN   | NaN     |
| 2     | product_name_lenght         | float64 | 610  | 66     | [40.0, 44.0, 46.0, 27.0, 37.0, 60.0, 56...] | 5.0   | 42.0  | 51.0   | 57.0  | 76.0    |
| 3     | product_description_lenght  | float64 | 610  | 2960   | [287.0, 276.0, 250.0, 261.0, 402.0, 745...] | 4.0   | 339.0 | 595.0  | 972.0 | 3992.0  |
| 4     | product_photos_qty          | float64 | 610  | 19     | [1.0, 4.0, 2.0, 3.0, 5.0, 9.0, 6.0, nan...] | 1.0   | 1.0   | 1.0    | 3.0   | 20.0    |
| 5     | product_weight_g            | float64 | 2    | 2204   | [225.0, 1000.0, 154.0, 371.0, 625.0, 200...] | 0.0   | 300.0 | 700.0  | 1900.0| 40425.0 |
| 6     | product_length_cm           | float64 | 2    | 99     | [16.0, 30.0, 18.0, 26.0, 20.0, 38.0, 70...] | 7.0   | 18.0  | 25.0   | 38.0  | 105.0   |
| 7     | product_height_cm           | float64 | 2    | 102    | [10.0, 18.0, 9.0, 4.0, 17.0, 5.0, 24.0, ...] | 2.0   | 8.0   | 13.0   | 21.0  | 105.0   |
| 8     | product_width_cm            | float64 | 2    | 95     | [14.0, 20.0, 15.0, 26.0, 13.0, 11.0, 44...] | 6.0   | 15.0  | 20.0   | 30.0  | 118.0   |

**Products Table Information**
- The `products_df` table consists of **32,951 records** and provides detailed information about products available in the Olist marketplace.  

- **Features**:  
  1. **`product_id`**: A unique identifier for each product. There are **32,951 unique values**, ensuring that each product is distinct.  
  2. **`product_category_name`**: The category of the product. It contains **73 unique categories**, with **610 missing values** that can potentially be handled for analysis.  
  3. **`product_name_lenght`**: The character length of product names, ranging from **5** to **76**, with a median of **51**.  
  4. **`product_description_lenght`**: The character length of product descriptions, ranging from **4** to **3,992**, with a median of **595**.  
  5. **`product_photos_qty`**: The number of photos associated with each product. Values range from **1** to **20**, with a median of **1**, indicating many products have only one photo.  
  6. **`product_weight_g`**: The weight of the product in grams, ranging from **0** to **40,425**, with a median of **700g**.  
  7. **`product_length_cm`**: The product's length in centimeters, ranging from **7** to **105**, with a median of **25cm**.  
  8. **`product_height_cm`**: The product's height in centimeters, ranging from **2** to **105**, with a median of **13cm**.  
  9. **`product_width_cm`**: The product's width in centimeters, ranging from **6** to **118**, with a median of **20cm**.  

- The `product_category_name` column has **610 missing values**, which may require imputation or exclusion depending on the analysis.  
- Product attributes such as weight and dimensions show significant variability, which can be useful for personalized recommendations.  

- **Relationship in the Schema**:  
  - The `product_id` column connects the `products_df` table to the `order_items_df` table, allowing products to be linked to specific orders.  
  - The `product_category_name` can be further detailed by linking to the `product_category_df` table, which provides English names for categories.

#### **3.1.6. Product Category**

| Index | product_category_name | product_category_name_english |
|-------|------------------------|-------------------------------|
| 0     | beleza_saude          | health_beauty                |

| Index | Column                       | Type   | Null | Unique | Unique Values                                      | Min   | Q1   | Median | Q3   | Max   |
|-------|------------------------------|--------|------|--------|---------------------------------------------------|-------|-------|--------|-------|-------|
| 0     | product_category_name        | object | 0    | 71     | [beleza_saude, informatica_acessorios, automot...] | NaN   | NaN   | NaN    | NaN   | NaN   |
| 1     | product_category_name_english| object | 0    | 71     | [health_beauty, computers_accessories, auto, b...] | NaN   | NaN   | NaN    | NaN   | NaN   |

**Product Category Table Information**
- The `product_category_df` table consists of **71 records** and provides a mapping between product category names in Portuguese and their corresponding English translations.  

- **Features**:  
  1. **`product_category_name`**: The product category in Portuguese, with **71 unique values**, representing different types of products available in the dataset.  
  2. **`product_category_name_english`**: The English translation of the product category, also with **71 unique values**, ensuring one-to-one correspondence with the Portuguese category names.  

- The table contains **no missing values**, ensuring complete mapping between Portuguese and English product categories.  
- The table is essential for interpreting product categories in a more universally understandable format, particularly when analyzing data or presenting results to a non-Portuguese-speaking audience.  

- **Relationship in the Schema**:  
  - The `product_category_name` column connects the `product_category_df` table to the `products_df` table, enabling the categorization of products into their respective English-named categories.

### **3.2. Exploratory Data Analysis**

#### **3.2.1. Products Price**

![Price Distribution](images/image-1.png)

The analysis of product prices reveals a highly skewed distribution, as shown in the histogram. The majority of product prices are concentrated below **500 BRL**, with only a small number of products priced above **1,000 BRL**. It's suggests that most products are relatively affordable, catering to a broader customer base. The boxplot further highlights the presence of significant outliers, with prices extending up to **6,735 BRL**, representing luxury or specialty products. The interquartile range (IQR) indicates that most product prices fall between approximately **39.9 BRL** and **134.9 BRL**. These insights suggest that while the marketplace primarily targets affordability, the inclusion of high-value products adds diversity to the catalog. These outliers may require careful consideration during the development of the recommendation system to ensure they do not overly influence results.

#### **3.2.2. Review Score**

![Review Scores Distribution](images/image-2.png)

The distribution of review scores provides insights into customer satisfaction levels. The bar chart shows that the majority of reviews, accounting for **57.78% (57,328 reviews)**, have a perfect score of 5, indicating highly satisfied customers. It's is followed by a score of 4, with **19.29% (19,142 reviews)**, also reflecting positive feedback. On the lower end, **11.51% (11,424 reviews)** of reviews have a score of 1, representing dissatisfied customers, while scores of 2 and 3 account for **3.18% (3,151 reviews)** and **8.24% (8,179 reviews)**, respectively.

Review scores distribution demonstrates that the platform generally maintains a high level of customer satisfaction, as more than **77% of reviews** are positive (scores of 4 and 5). However, the presence of a notable proportion of low ratings (1 and 2) suggests areas for improvement in the customer experience, which can be targeted in recommendation strategies.

#### **3.2.3. Products Category**

![Product Category Distribution](images/image-3.png)

The analysis of product category distribution highlights the top 10 most popular categories in the dataset. The **bed, bath, and table** category leads with **3,029 products (9.19%)**, followed by **sports leisure** at **2,867 products (8.70%)**, and **furniture decor** with **2,657 products (8.06%)**. Other notable categories include **health beauty (7.42%)**, **housewares (7.09%)**, and **auto (5.77%)**. The categories **computers accessories**, **toys**, **watches gifts**, and **telephony** each make up between **3.44% and 4.97%** of the total products.

The distribution shows that the marketplace offers a wide range of categories, with household and lifestyle-related products dominating the catalog. These insights are crucial for developing the recommendation system, as they help in identifying key product categories that contribute significantly to customer engagement. Tailoring recommendations based on these popular categories could enhance customer satisfaction and increase sales.

| Index | Value              | Potential_Match    | Similarity |
|-------|--------------------|--------------------|------------|
| 0     | eletrodomesticos   | eletrodomesticos_2 | 94.12%     |
| 1     | casa_conforto      | casa_conforto_2    | 92.86%     |

The analysis of the `product_category_name` in `products_df` and `product_category_df` column identified 2 potential typos: "eletrodomesticos" and "eletrodomesticos_2" (94.12% similarity) and "casa_conforto" and "casa_conforto_2" (92.86% similarity). These findings suggest minor inconsistencies in category naming that may require standardization to ensure data quality and consistency in analysis.

| Index | Value                   | Potential_Match       | Similarity |
|-------|-------------------------|-----------------------|------------|
| 0     | fashion_male_clothing  | fashio_female_clothing | 93.02%     |
| 1     | home_appliances        | home_appliances_2      | 93.75%     |

The analysis of the `product_category_name_english` column in `product_category_df` identified 2 potential typos. First, "fashio_female_clothing" should be corrected to "fashion_female_clothing" to maintain consistent naming, while "fashion_male_clothing" is a valid and distinct category that requires no correction. Second, "home_appliances_2" should be standardized to "home_appliances" to ensure uniformity within the `product_category_name_english` column. These adjustments will improve data quality and consistency.

```python
Missing categories:
pc_gamer
nan
portateis_cozinha_e_preparadores_de_alimentos
```

The comparison of unique categories between `products_df` and `product_category_df` revealed three missing categories in `product_category_df`: "pc_gamer," "portateis_cozinha_e_preparadores_de_alimentos," and `nan`. It's indicates that not all categories in `products_df` have corresponding entries in `product_category_df`. Addressing these discrepancies, such as adding the missing categories or standardizing the data, is necessary to ensure complete and accurate mapping between the two tables.

## **4. Data Preparation**

### **4.2. Data Cleaning**

#### **4.2.1. Products**

- Null values in `product_name_lenght`, `product_description_lenght`, and `product_photos_qty` were filled with **0** to retain all products in the dataset. Rows with null or zero values in product measurement columns (`product_weight_g`, `product_length_cm`, `product_height_cm`, `product_width_cm`) were removed to ensure the accuracy of physical product attributes. These steps are necessary to maintain data consistency and reliability for analysis and modeling.

- The numerical columns in `products_df`, including `product_name_length`, `product_description_length`, `product_photos_qty`, `product_weight_g`, `product_length_cm`, `product_height_cm`, and `product_width_cm`, were converted to integer format. The transformation optimizes memory usage and ensures consistency, as the data contains only integer-like values.

- A new feature, `volume_cm3`, was created by calculating the product of `product_length_cm`, `product_height_cm`, and `product_width_cm`, representing the product's volume in cubic centimeters. The original columns used for the calculation (`product_length_cm`, `product_height_cm`, `product_width_cm`) were dropped to simplify the dataset and avoid redundancy. The dataset enhances by consolidating dimensional attributes into a single, more useful feature for analysis.

- The `product_category_name` column in `products_df` was cleaned through typo correction. In a single iteration, 2 corrections were made, ensuring consistency and standardization of category names. The cleaning process confirmed that no further typos remain, resulting in a fully standardized `product_category_name` column.

#### **4.2.2. Product Category**

- The `product_category_name` column in `product_category_df` was cleaned through typo correction. In a single iteration, 2 corrections were made, ensuring the consistency and standardization of category names. The cleaning process confirmed that no further typos remain, resulting in a fully standardized `product_category_name` column.

- The `product_category_name_english` column in `product_category_df` was cleaned by replacing "fashio_female_clothing" with "fashion_female_clothing" and "home_appliances_2" with "home_appliances." These corrections ensure consistency and accuracy in the English category names, aligning them with the intended standard naming conventions.

- Three new categories were successfully added to `product_category_df` to address previously identified missing entries. These include "portateis_cozinha_e_preparadores_de_alimentos" with its English equivalent "portable_kitchen_food_preparers," "pc_gamer," and a placeholder category for missing or undefined values with the English equivalent "others." The update ensures complete mapping between Portuguese and English category names and resolves gaps in the dataset.

- The `products_df` dataset was enriched with English category names by merging it with `product_category_df` using the `product_category_name` column. The original Portuguese category name column was dropped, and the English category name column was renamed to `product_category_name` for consistency. The transformation ensures the dataset is standardized and easier to interpret for further analysis.

### **4.3. Data Merging**

#### **4.3.1. Orders, Customers, Order Items, and Order Reviews**

| Index | order_id                          | customer_unique_id             | product_id                        | price  | review_score |
|-------|-----------------------------------|---------------------------------|-----------------------------------|--------|--------------|
| 0     | e481f51cbdc54678b7cc49136f2d6af7 | 7c396fd4830fd04220f754e42b4e5bff | 87285b34884572647811a353c7ac498a | 29.99  | 4            |

```python
(112372, 5)
```

| Index | Column              | Type     | Null | Unique | Unique Values                                                                                          | Min   | Q1    | Median | Q3    | Max    |
|-------|---------------------|----------|------|--------|------------------------------------------------------------------------------------------------------|-------|-------|--------|-------|--------|
| 0     | order_id            | object   | 0    | 97917  | [e481f51cbdc54678b7cc49136f2d6af7, 53cdb2fc8bc...]                                                 | NaN   | NaN   | NaN    | NaN   | NaN    |
| 1     | customer_unique_id  | object   | 0    | 94721  | [7c396fd4830fd04220f754e42b4e5bff, af07308b275...]                                                 | NaN   | NaN   | NaN    | NaN   | NaN    |
| 2     | product_id          | object   | 0    | 32789  | [87285b34884572647811a353c7ac498a, 595fac2a385...]                                                 | NaN   | NaN   | NaN    | NaN   | NaN    |
| 3     | price               | float64  | 0    | 5948   | [29.99, 118.7, 159.9, 45.0, 19.9, 147.9, 49.9,...]                                                 | 0.85  | 39.9  | 74.9   | 134.9 | 6735.0 |
| 4     | review_score        | int64    | 0    | 5      | [4, 5, 2, 1, 3]                                                                                      | 1     | 4     | 5      | 5     | 5      |

The datasets were merged to create a unified table for analysis.

1. **`orders_df` with `customers_df`**
   - The two tables were joined on the `customer_id` column to associate each order with its corresponding unique customer ID. The `customer_id` column was dropped after the merge to avoid redundancy.  

2. **`orders_df` with `order_items_df`**
   - Order details connected with the products purchased, enabling a link between orders and products via the `product_id` column.  

3. **`orders_df` with `order_reviews_df`**
   - Reviews were added to the unified table by joining on the `order_id`, allowing the inclusion of customer feedback for each order.  

The resulting table contains **112,372 records** and five columns.
- **`order_id`**: Unique identifier for each order.  
- **`customer_unique_id`**: Unique identifier for customers, ensuring customer-level analysis.  
- **`product_id`**: Identifier for purchased products.  
- **`price`**: Price of each purchased product.  
- **`review_score`**: Customer feedback score for each order.  

Consolidated dataset is essential for connecting customer, product, and review data, forming the foundation for building the recommendation system.

#### **4.3.2. Filtered Orders**

| Index | order_id                            | customer_unique_id            | product_id                        | price | review_score |
|-------|-------------------------------------|------------------------------|-----------------------------------|-------|--------------|
| 0     | e481f51cbdc54678b7cc49136f2d6af7   | 7c396fd4830fd04220f754e42b4e5bff | 87285b34884572647811a353c7ac498a | 29.99 | 4            |

```python
(13803, 5)
```

| Index | Column                | Type     | Null | Unique | Unique Values                                                                 | Min  | Q1   | Median | Q3   | Max   |
|-------|-----------------------|----------|------|--------|-------------------------------------------------------------------------------|------|------|--------|------|-------|
| 0     | order_id              | object   | 0    | 8239   | [e481f51cbdc54678b7cc49136f2d6af7, dcb36b511fc...]                            | NaN  | NaN  | NaN    | NaN  | NaN   |
| 1     | customer_unique_id    | object   | 0    | 5384   | [7c396fd4830fd04220f754e42b4e5bff, ccafc1c3f27...]                            | NaN  | NaN  | NaN    | NaN  | NaN   |
| 2     | product_id            | object   | 0    | 7796   | [87285b34884572647811a353c7ac498a, 009c09f4399...]                            | NaN  | NaN  | NaN    | NaN  | NaN   |
| 3     | price                 | float64  | 0    | 1919   | [29.99, 132.4, 90.9, 39.5, 14.49, 36.9, 84.9, ...]                            | 0.85 | 35.0 | 63.3   | 109.99| 4799.0 |
| 4     | review_score          | int64    | 0    | 5      | [4, 5, 1, 3, 2]                                                              | 1    | 2    | 4      | 5    | 5     |

The dataset was filtered to include only customers who have purchased more than one unique product. The filtered dataset now contains **13,803 records** and **5,384 unique customers**. Noise reduced by focusing on customers with diverse purchasing behaviors, which are more informative for building recommendation models.

#### **4.3.3. Product Price**

| Index | product_id                          | price |
|-------|-------------------------------------|-------|
| 0     | 0011c512eb256aa0dbbb544d8dffcf6e    | 52.0  |

```python
(7796, 2)
```

| Index | Column        | Type    | Null | Unique | Unique Values                                                                                   | Min   | Q1    | Median | Q3    | Max    |
|-------|---------------|---------|------|--------|------------------------------------------------------------------------------------------------|-------|-------|--------|-------|--------|
| 0     | product_id    | object  | 0    | 7796   | [0011c512eb256aa0dbbb544d8dffcf6e, 001b72dfd63...]                                           | NaN   | NaN   | NaN    | NaN   | NaN    |
| 1     | price         | float64 | 0    | 2120   | [52.0, 34.99, 32.98, 79.99, 129.9, 39.9, 13.99...]                                             | 0.85  | 34.9  | 64.9   | 119.9 | 4799.0 |

- The average price for each product was calculated by grouping the data by `product_id`. Products were grouped by `product_id`, and the mean price for each product was computed.  
- The resulting dataset contains **7,796 unique products** with their average prices.  
- Prices range from **0.85 BRL** to **4,799 BRL**, with a median price of **64.9 BRL**.  

#### **4.3.4. Products and Product Price**

| Index | product_category_name | product_id                              | product_description_length | product_photos_qty | product_weight_g | volume_cm3 | price  |
|-------|-----------------------|----------------------------------------|----------------------------|--------------------|------------------|------------|--------|
| 0     | health_beauty         | 50556c630443502c11acde1c320fe278      | 257                        | 1                  | 250              | 640        | 129.9  |

```python
(7795, 7)
```

| Index | Column                     | Type     | Null | Unique | Unique Values                                                                                                                                         | Min   | Q1    | Median | Q3    | Max    |
|-------|----------------------------|----------|------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------|-------|-------|--------|-------|--------|
| 0     | product_category_name       | object   | 0    | 69     | [health_beauty, computers_accessories, auto, books, sports, electronics, furniture, etc.]                                                             | NaN   | NaN   | NaN    | NaN   | NaN    |
| 1     | product_id                  | object   | 0    | 7795   | [50556c630443502c11acde1c320fe278, 12827caf723..., etc.]                                                                                           | NaN   | NaN   | NaN    | NaN   | NaN    |
| 2     | product_description_length  | int64    | 0    | 1802   | [257, 3939, 677, 1935, 763, 991, 572, 803, 180...                                                                                                   | 0     | 296   | 528    | 878   | 3976   |
| 3     | product_photos_qty          | int64    | 0    | 17     | [1, 6, 3, 2, 5, 4, 7, 10, 9, 8, 11, 13, 12, 15, etc.]                                                                                              | 0     | 1     | 1      | 3     | 17     |
| 4     | product_weight_g            | int64    | 0    | 955    | [250, 150, 750, 300, 217, 350, 2550, 100, 975, ...                                                                                                  | 50    | 300   | 650    | 1647  | 30000  |
| 5     | volume_cm3                  | int64    | 0    | 1932   | [640, 4368, 4096, 4455, 4000, 3315, 3332, 990, ...                                                                                                  | 352   | 2576  | 6300   | 16500 | 288000 |
| 6     | price                       | float64  | 0    | 2120   | [129.9, 57.5, 35.0, 139.56, 55.5, 35.79, 37.9, ...                                                                                                  | 0.85  | 34.9  | 64.9   | 119.9 | 4799.0 |

The `products_df` dataset was enriched by merging it with the product price dataset. The merge linked `product_id` column in both datasets , adding the average price of each product to the `products_df`.

- The merged dataset contains **7,795 records** and **7 columns**, including product attributes such as category, weight, volume, and now the average price (`price`).  
- No missing values were introduced during the merge, ensuring data completeness.

### **4.4. Content-Based Filtering**

Categorical features were created from continuous numerical variables to facilitate content-based filtering. Using the `pd.qcut` function, each numerical variable was divided into three quantile-based categories:

- **Product Description Length** was segmented into:
  - `short_description`, `medium_description`, `long_description`

- **Number of Photos** was categorized into:
  - `few_photos`, `moderate_photos`, `many_photos`

- **Product Weight** was classified as:
  - `light_weight`, `medium_weight`, `heavy_weight`

- **Product Volume** was divided into:
  - `small_volume`, `medium_volume`, `large_volume`

- **Product Price** was categorized into:
  - `low_price`, `medium_price`, `high_price`

Converting these numerical variables into categorical ones simplifies the comparison between products based on these attributes. The transformation is essential for content-based filtering, as it allows the recommendation system to identify and suggest products with similar characteristics more effectively.

Here is a summary of the quartile values for the specified columns:

| Column                     | 33rd    | 66th | 100th    |
|----------------------------|-------|-------------|-------|
| product_description_length  | 365   | 713         | 3976  |
| product_photos_qty          | 1     | 2           | 17    |
| product_weight_g            | 375   | 1150        | 30000 |
| volume_cm3                  | 3564  | 11352       | 288000|
| price                       | 42    | 94          | 4799  |

The quantiles for key numerical variables were calculated to establish thresholds for categorizing them into groups for content-based filtering. The results for each variable are as follows above. These thresholds define the boundaries for the categories created in the previous step, ensuring that the grouping is based on the natural distribution of each variable. Data-driven segmentation improves the precision and relevance of the content-based filtering approach.

| product_category_name | product_id                           | description_category | photos_category | weight_category | volume_category | price_category |
|-----------------------|--------------------------------------|----------------------|-----------------|-----------------|-----------------|----------------|
| health_beauty         | 50556c630443502c11acde1c320fe278     | short_description    | few_photos      | light_weight    | small_volume    | high_price     |
| health_beauty         | 12827caf723c4ff8030d64dcffe3cdab     | long_description     | many_photos     | light_weight    | medium_volume   | medium_price   |
| health_beauty         | 4a37bc772078a013df64fd661ae5ee6c     | medium_description   | few_photos      | medium_weight   | medium_volume   | low_price      |
| health_beauty         | dbbda25f6b7c74669cf2203b2e3139a0     | long_description     | many_photos     | light_weight    | medium_volume   | high_price     |
| health_beauty         | 2fffbf105cbbc6e86144f00ecf571635     | long_description     | few_photos      | light_weight    | medium_volume   | medium_price   |

The dataset was prepared for content-based filtering by separating categorical and numerical data.
- The `cb_data` dataset contains categorical features for modeling in content-based filtering.  
- The `products_df` remains as a separate dataset, focusing on numerical attributes for other analyses.  

| product_id                           | combined_features                                      |
|--------------------------------------|--------------------------------------------------------|
| 50556c630443502c11acde1c320fe278     | health_beauty short_description few_photos light_weight small_volume high_price |

```python
(7795, 2)
```

| Column             | Type   | Null | Unique | Unique Values                                                      | Min | Q1 | Median | Q3 | Max |
|--------------------|--------|------|--------|-------------------------------------------------------------------|-----|----|--------|----|-----|
| product_id         | object | 0    | 7795   | [50556c630443502c11acde1c320fe278, 12827caf723...]               | NaN | NaN | NaN    | NaN | NaN |
| combined_features  | object | 0    | 2254   | [health_beauty short_description few_photos light_weight small_volume high_price, ...] | NaN | NaN | NaN    | NaN | NaN |

A new column, `combined_features`, was created to consolidate all categorical attributes into a single textual representation for each product. The categorical attributes were concatenated into a single string per product. Textual representation captures all the relevant features for content-based filtering, allowing for easier similarity comparisons.  

- The dataset contains **7,795 records** with no missing values.  
- There are **2,254 unique combinations** of product features, reflecting the diversity of products in the dataset.  

Dataset is prepared and now ready for modeling in content-based filtering, where similarity between products will be calculated based on their combined textual features.

### **4.5. Collaborative Filtering**

A user-item matrix was created to prepare data for collaborative filtering. The matrix models relationships between customers and products based on their review scores. The `orders_df` was transformed into a pivot table where:
- Rows (`index`) represent unique customers (`customer_unique_id`).  
- Columns represent unique products (`product_id`).  
- Values are the average review scores (`review_score`) left by customers for each product.  
    
```python
DataFrame shape: (5384, 7796)
NaN values: 41961803 (99.97%)
Not NaN values: 11861 (0.03%)
```

- The resulting matrix contains **5,384 rows (customers)** and **7,796 columns (products)**.  
- The matrix has **41,961,803 NaN values (99.97%)** and **11,861 non-NaN values (0.03%)**, reflecting the sparsity typical of collaborative filtering data, as customers interact with only a small subset of available products.  

The user-item matrix enables collaborative filtering models to predict ratings for products that customers have not interacted with by analyzing patterns in existing customer-product interactions [(datacamp, 2024)](https://app.datacamp.com/learn/courses/building-recommendation-engines-in-python).

![Holdout Set](images/image-4.png)

The user-item matrix was split to create a training set for collaborative filtering modeling.

1. **Splitting Rows and Columns**
   - The matrix was partitioned by masking **50% of the rows and columns**, setting their values to `NaN` to simulate unseen data for model training.  

2. **Removing Fully NaN Rows**
   - Rows where all values were `NaN` after masking were dropped to ensure valid data for training.

3. **Extracting Values and Mask**:  
   - The processed training set was converted to an array (`training_set_value`) with a corresponding mask (`training_mask`) identifying non-`NaN` values.

```python
Training set shape: (4735, 7796)
Training set size: (8853,) (74.64%)
[2. 2. 5. 4. 5. 5. 1. 3. 5. 1.]
```

Partitioning the matrix allows the model to learn from existing customer-product interactions while withholding part of the data for validation. This approach ensures the robustness of collaborative filtering models in predicting unseen ratings.

- The resulting training set has a shape of **4,735 rows and 7,796 columns**.  
- A total of **8,853 non-NaN values (74.64% of the original non-NaN values)** are available for training.  

A holdout set was created to evaluate the performance of the collaborative filtering model.

1. **Partitioning the Holdout Set**
   - The holdout set was created by retaining only the **masked rows and columns** (50% of the original matrix).  
   - The holdout set excludes rows that were completely `NaN` after the initial split.  

2. **Extracting Values and Mask**:  
   - The holdout set was converted to an array (`holdout_set_value`) with a corresponding mask (`holdout_mask`) identifying non-`NaN` values.

```python
Holdout set shape: (2043, 3898)
Holdout set size: (1661,) (14.00%)
[5. 4. 5. 3. 5. 2. 1. 1. 1. 5.]
```

The holdout set serves as unseen data for evaluating the model's ability to predict customer ratings for products they have not interacted with during training. Using a holdout set ensures an unbiased assessment of the collaborative filtering model's performance.

- The holdout set has a shape of **2,043 rows and 3,898 columns**.  
- A total of **1,661 non-NaN values (14.00% of the original non-NaN values)** are available for evaluation.  

```python
[ 0.  0.  0.  0.  0.  2. -2.  0.  0.  0.]
```

The review scores in the training set were normalized to center each customer’s ratings around zero.

1. **Centering Review Scores**
   - The average review score for each customer was calculated using `training_set.mean(axis=1)`.  
   - The average score was subtracted from each customer’s individual review scores, effectively centering the ratings around zero. This normalization helps account for customer rating bias (e.g., consistently giving higher or lower ratings).  

2. **Filling Missing Values**
   - Missing values (`NaN`) in the centered training set were replaced with zeros. This step ensures compatibility with matrix-based algorithms while retaining the sparse nature of the data.

## **5. Modeling**

### **5.1. Content-Based Filtering**

Content-Based Filtering (CBF) is a recommendation system approach that suggests items to users based on the characteristics of items they have previously interacted with. The system relies on the content (features) of items and the user's historical preferences, without requiring data from other users.

---

#### **How Content-Based Filtering Works**

1. **Item Feature Representation**
   - Each item in the dataset is represented by a set of descriptive attributes (features). These features can be categorical (e.g., genre, category) or numerical (e.g., price, ratings).  
   - In CBF, items are often encoded as vectors in a feature space using techniques such as.
     - **TF-IDF**: Converts textual features into numerical vectors by assigning weights to terms based on their importance.
     - **One-Hot Encoding** or **Embedding**: Used for categorical features.  

2. **User Profile Generation**
   - A **user profile** is built by aggregating the features of items the user has interacted with (e.g., purchased, liked, rated).  
   - For example, the profile may take the average feature values of the items the user interacted with to create a vector representing the user's preferences.  

3. **Similarity Calculation**:  
   - The system calculates the similarity between the user profile and the feature vectors of other items in the dataset. **Cosine Similarity** is a commonly used metric.

4. **Ranking and Recommendation**:  
   - Items are ranked based on their similarity to the user's profile.  
   - The system recommends the top-N items with the highest similarity scores.

---

#### **Advantages of Content-Based Filtering**

1. **Personalization**
   - Recommendations are tailored to each user based on their preferences, leading to highly personalized suggestions.  

2. **No Cold Start for Items**
   - CBF does not depend on user-to-user interactions, so new items with sufficient descriptive features can be recommended immediately.  

3. **Transparency**
   - The system can explain recommendations by pointing to item features that match the user’s preferences (e.g., "Recommended because it matches your interest in health and beauty products").  

---

#### **Challenges of Content-Based Filtering**

1. **Cold Start for Users**
   - The system requires historical data (e.g., purchases or ratings) to generate a user profile. For new users with no interaction history, CBF may struggle.  

2. **Lack of Diversity**
   - The system tends to recommend items similar to what the user has already interacted with, which may lead to a "filter bubble" and limit discovery of diverse content.  

3. **Limited Feature Space**
   - The quality of recommendations depends on the richness and relevance of item features. Poorly described items or sparse features can degrade performance.  

4. **Scalability**
   - For large datasets, computing similarities between all items and users can become computationally expensive.  

---

#### **Applications of Content-Based Filtering**

- **E-commerce**: Suggesting products based on categories, prices, or descriptions.  
- **Streaming Services**: Recommending movies or songs based on genres, ratings, or tags.  
- **Online Education**: Proposing courses based on skill tags or user learning history.  

Content-Based Filtering is a powerful technique when detailed item features are available, and user preferences are well-understood. Its combination with other methods, such as collaborative filtering, can help address its limitations and enhance recommendation quality.

#### **5.1.1. Term Frequency-Inverse Document Frequency**

The implementation prepares text data for CBF by converting combined product features into numerical vectors using **TF-IDF (Term Frequency-Inverse Document Frequency)**. TF-IDF is a text representation technique that assigns weights to terms based on their importance within a document and their rarity across the entire corpus.

1. **Term Frequency (TF)**
   - Measures how frequently a term appears in a document.  
   - **Mathematical Formula**
    
     $$TF(t, d) = \frac{\text{Number of times term } t \text{ appears in document } d}{\text{Total number of terms in document } d}$$

2. **Inverse Document Frequency (IDF)**
   - Weighs terms by their rarity across all documents in the corpus. Rare terms are given higher weights, while common terms receive lower weights.  
   - **Mathematical Formula**
     
     $$IDF(t) = \log \left( \frac{\text{Total number of documents}}{\text{Number of documents containing term } t} \right) + 1$$

3. **TF-IDF Score**
   - Combines TF and IDF to assign a final weight to each term.  
   - **Mathematical Formula**
     
     $$TF\text{-}IDF(t, d) = TF(t, d) \times IDF(t)$$

The TF-IDF approach prioritizes meaningful and unique terms in the product descriptions while minimizing the influence of common terms. This representation is fundamental for calculating product similarities, enabling the recommendation system to identify and recommend similar products effectively.

1. **Vectorization**
   - The `TfidfVectorizer` object was instantiated and applied to the `combined_features` column of `cb_data`.  
   - Each term was assigned a weight based on its TF-IDF score, capturing its importance relative to the product it belongs to and its rarity in the entire dataset.  

2. **Feature Extraction**
   - Distinct terms (features) were extracted from the combined features text.  
   - Products were represented as sparse vectors in a high-dimensional space, where each dimension corresponds to a term's TF-IDF weight.

| product_id                              | short_description | agro_industry_and_commerce | market_place | drinks | cine_photo |
|-----------------------------------------|-------------------|----------------------------|--------------|--------|------------|
| e799a2b8707a6a256fa3c040b75d2713       | 0                 | 0                          | 0            | 0      | 0          |
| 1c6fb703c624b381a20f21f757694866       | 0                 | 0                          | 0            | 0      | 0          |
| 6d0a373c460a041c86167a92a5d3383e       | 0                 | 0                          | 0            | 0      | 0          |
| 40678c9096047877fa74a25cc6f0a726       | 0                 | 0                          | 0            | 0      | 0          |
| b8a0d73b2a06e7910d9864dccdb0cda2       | 0.358             | 0                          | 0            | 0      | 0          |

The TF-IDF matrix was converted into a DataFrame for easier processing.

- The TF-IDF sparse matrix was transformed into a dense DataFrame with terms as columns and TF-IDF scores as values.
- The `product_id` was set as the index to link each row to a specific product.

The DataFrame represents products as numerical vectors based on TF-IDF scores, making it ready for similarity calculations and recommendations.

#### **5.1.2. Cosine Similarity**

Cosine similarity measures the cosine of the angle between two vectors in a multi-dimensional space. It determines how similar two vectors are, regardless of their magnitude, making it ideal for comparing textual data represented as TF-IDF vectors.  

**Mathematical Formula**

$$\text{Cosine Similarity} = \frac{\mathbf{A} \cdot \mathbf{B}}{\|\mathbf{A}\| \|\mathbf{B}\|}$$

Where:  
- $\mathbf{A}$ and $\mathbf{B}$ are the TF-IDF vectors of two products.  
- $\|\mathbf{A}\|$ and $\|\mathbf{B}\|$ are the magnitudes (norms) of the vectors.  
- $\mathbf{A} \cdot \mathbf{B}$ is the dot product of the vectors.  

The similarity ranges from **0** (completely dissimilar) to **1** (identical), making it easy to interpret.  

|                                      | e799a2b8707a6a256fa3c040b75d2713 | 1c6fb703c624b381a20f21f757694866 | 6d0a373c460a041c86167a92a5d3383e | 40678c9096047877fa74a25cc6f0a726 | b8a0d73b2a06e7910d9864dccdb0cda2 |
|--------------------------------------|-----------------------------------|-----------------------------------|-----------------------------------|-----------------------------------|-----------------------------------|
| e799a2b8707a6a256fa3c040b75d2713    | 1.000                             | 0.231                             | 1.000                             | 0.114                             | 0.131                             |
| 1c6fb703c624b381a20f21f757694866    | 0.231                             | 1.000                             | 0.231                             | 0.272                             | 0.114                             |
| 6d0a373c460a041c86167a92a5d3383e    | 1.000                             | 0.231                             | 1.000                             | 0.114                             | 0.131                             |
| 40678c9096047877fa74a25cc6f0a726    | 0.114                             | 0.272                             | 0.114                             | 1.000                             | 0.000                             |
| b8a0d73b2a06e7910d9864dccdb0cda2    | 0.131                             | 0.114                             | 0.131                             | 0.000                             | 1.000                             |

The cosine similarity matrix was generated to compute the similarity between products based on their TF-IDF vectors.

- The `cosine_similarity` function was applied to the TF-IDF matrix, generating a square matrix where each entry represents the similarity between two products.
- The similarity matrix was converted into a DataFrame, with rows and columns indexed by `product_id`. This allows for intuitive lookup of similarity scores between specific products.

The `cosine_similarity_df` provides pairwise similarity scores between products. These scores enable the recommendation system to identify and rank products most similar to a given product based on their features, forming the foundation of the CBF approach.

#### **5.1.3. Product Recommendation**

| product_id                             | 4d5bb93bfa70f67cda10b1428f2a252c |
|----------------------------------------|-----------------------------------|
| e65ab426efaf65e083b917d2e9eac80d      | 0.941                             |
| 1589d392201547c9b78a117c0e2191dd      | 0.881                             |
| 44feb28a74abb0f2f303412d60160750      | 0.881                             |
| 652683d85a89f3b134e44989a601bc58      | 0.881                             |
| 1da502ed939a029f4bee6cdc8341b4ac      | 0.822                             |

The recommendation system generates product suggestions by identifying the most similar products to a given one based on cosine similarity scores.

- The cosine similarity scores for the product with ID `4d5bb93bfa70f67cda10b1428f2a252c` were retrieved from the `cosine_similarity_df`.
- The similarity scores were sorted in descending order, ranking products from most to least similar to the selected product.
- The top 5 most similar products were selected, representing the closest matches based on their features.

The system outputs a ranked list of the top 5 recommended products, based on their similarity scores to the selected product. These recommendations demonstrate the practical application of CBF for suggesting similar products to customers.

| product_category_name | product_id                             | product_description_lenght | product_photos_qty | product_weight_g | volume_cm3 | price  |
|-----------------------|----------------------------------------|----------------------------|--------------------|------------------|------------|--------|
| books_technical       | 4d5bb93bfa70f67cda10b1428f2a252c      | 250                        | 1                  | 500              | 5808       | 130.8  |

| product_category_name | product_id                             | product_description_lenght | product_photos_qty | product_weight_g | volume_cm3 | price  |
|-----------------------|----------------------------------------|----------------------------|--------------------|------------------|------------|--------|
| books_technical       | 44feb28a74abb0f2f303412d60160750      | 1120                       | 1                  | 500              | 1350       | 127.00 |
| books_technical       | 1589d392201547c9b78a117c0e2191dd      | 1499                       | 1                  | 450              | 4356       | 24.97  |
| books_technical       | e65ab426efaf65e083b917d2e9eac80d      | 445                        | 1                  | 500              | 6292       | 147.99 |
| books_technical       | 1da502ed939a029f4bee6cdc8341b4ac      | 1345                       | 1                  | 600              | 1104       | 29.45  |
| books_technical       | 652683d85a89f3b134e44989a601bc58      | 246                        | 1                  | 1100             | 1764       | 34.30  |

The CBF system identified the top 5 most similar products to the product with ID `4d5bb93bfa70f67cda10b1428f2a252c`. All recommended products belong to the same category, **books_technical**, demonstrating the system's ability to effectively identify similar products.  

Features such as **description length**, **photos quantity**, **weight**, **volume**, and **price** are closely aligned with the target product, ensuring the recommendations are relevant. The results showcase the effectiveness of the content-based filtering model in generating tailored product suggestions based on shared attributes.

### **5.2. Collaborative Filtering**

Collaborative Filtering (CF) is a recommendation system technique that suggests items to users based on the preferences and behaviors of other users. It assumes that users who have shown similar preferences in the past are likely to agree on future preferences.

---

#### **Types of Collaborative Filtering**

1. **User-Based Collaborative Filtering**
   - Compares a target user’s preferences with those of other users to find similar users (neighbors).  
   - Recommends items that the similar users (neighbors) have interacted with but the target user has not.  

2. **Item-Based Collaborative Filtering**
   - Compares items based on how users have rated or interacted with them.  
   - Recommends items similar to those the target user has interacted with.  

3. **Matrix Factorization (Model-Based Collaborative Filtering)**
   - Uses mathematical techniques such as **Singular Value Decomposition (SVD)** to decompose the user-item interaction matrix into latent factors.  
   - These latent factors represent hidden patterns in user and item relationships, enabling the system to make recommendations.  

---

#### **How Collaborative Filtering Works**

1. **User-Item Matrix**
   - CF relies on a user-item interaction matrix where rows represent users and columns represent items.  
   - The matrix contains ratings or interactions (e.g., purchases), with missing values indicating no interaction.  

2. **Similarity Measurement**
   - **User-Based CF**: Measures similarity between users using metrics like cosine similarity, Pearson correlation, or Euclidean distance.  
   - **Item-Based CF**: Measures similarity between items based on user interactions.  

3. **Prediction**
   - For user-based or item-based CF, predictions are made by aggregating the interactions of similar users or items.  
   - For model-based CF (e.g., SVD), predictions are made by reconstructing the interaction matrix from latent factors.  

---

#### **Advantages of Collaborative Filtering**

1. **Personalization**
   - Generates highly personalized recommendations by leveraging user behavior patterns.  

2. **No Need for Explicit Features**
   - Does not rely on predefined item attributes or features, as recommendations are based purely on interaction data.  

3. **Scalability with Data**
   - Improves as more users and items are added, revealing deeper patterns in preferences.  

---

#### **Challenges of Collaborative Filtering**

1. **Cold Start Problem**
   - Struggles with recommending items to new users (user cold start) or recommending newly added items (item cold start) due to a lack of interaction data.  

2. **Data Sparsity**
   - User-item matrices are often highly sparse, as users typically interact with only a small subset of items.  

3. **Scalability**
   - User-based or item-based CF can become computationally expensive as the number of users or items grows.  

4. **Over-Specialization**
   - Recommends items similar to those already interacted with, potentially limiting discovery of new or diverse items.  

---

#### **Applications of Collaborative Filtering**

1. **E-Commerce**: Suggesting products based on purchase history or customer ratings.  
2. **Streaming Services**: Recommending movies, music, or TV shows based on user preferences.  
3. **Social Media**: Suggesting friends, groups, or content based on shared interests or behaviors.  
4. **Online Education**: Proposing courses or study materials based on interactions and ratings.

CF is a widely used recommendation technique due to its flexibility and ability to leverage implicit patterns in user behavior, making it ideal for scenarios where explicit item features are limited.

#### **5.2.1. Singular Value Decomposition**

Singular Value Decomposition (SVD) is a mathematical technique used to factorize a matrix into three components: two orthogonal matrices and a diagonal matrix of singular values. In collaborative filtering, SVD is applied to the user-item interaction matrix to uncover latent factors that represent hidden relationships between customers and products.

**Mathematical Formula**

For a given matrix $R$ of dimensions $m \times n$ (e.g., a user-item matrix):

$$R = U \Sigma V^T$$

Where:
- $U$ is an $m \times k$ matrix containing the left singular vectors, representing customer latent factors.
- $\Sigma$ is a $k \times k$ diagonal matrix of singular values, representing the importance of each latent factor.
- $V^T$ is a $k \times n$ matrix containing the right singular vectors, representing product latent factors.
- $k$ is the rank of the matrix (or a reduced dimension for approximation).

**Core Concepts**

- **Latent Factor Discovery**

    SVD decomposes the interaction matrix into latent factors for customers and products, which capture hidden patterns such as customer preferences and product characteristics.

- **Dimensionality Reduction**

    The decomposition allows retaining only the top $k$ singular values and their associated vectors, effectively reducing noise and focusing on the most significant relationships.

- **Reconstruction for Prediction**

    An approximation of the original matrix can be reconstructed using a subset of the singular values.
    
    $$\hat{R} = U_k \Sigma_k V_k^T$$

    This reconstruction predicts missing values in the user-item matrix.

![SVD](images/image-5.png)

SVD is a foundational technique in recommendation systems, enabling robust prediction of missing values and enhancing the ability to personalize recommendations based on hidden customer and product characteristics [(Pu & Faltings, 2013)](https://app.datacamp.com/learn/courses/building-recommendation-engines-in-python).

```python
[[3.464 0.    0.    0.    0.    0.   ]
 [0.    3.464 0.    0.    0.    0.   ]
 [0.    0.    3.477 0.    0.    0.   ]
 [0.    0.    0.    4.085 0.    0.   ]
 [0.    0.    0.    0.    4.103 0.   ]
 [0.    0.    0.    0.    0.    4.155]]
 ```

The Singular Value Decomposition (SVD) process was applied to decompose the user-item interaction matrix into three components:

1. **Decomposing the Matrix**
   - The `training_set_centered` matrix was decomposed using the `svds` function, resulting in three matrices.
     - $U$: Contains customer latent factors.
     - $\Sigma$: A diagonal matrix of singular values representing the importance of each latent factor.
     - $V^T$: Contains product latent factors.

2. **Converting $\Sigma$ into a Diagonal Matrix**
   - The singular values extracted by `svds` were converted into a diagonal matrix using `np.diag`, making them ready for reconstruction or dimensionality reduction.

The diagonal matrix $\Sigma$ indicates the significance of the corresponding latent factors in explaining variations in the data. Larger singular values (e.g., 4.155) signify more influential latent factors, which are critical for reconstructing the user-item matrix and predicting missing interactions.
This decomposition lays the foundation for generating recommendations by reconstructing the interaction matrix with the dominant latent factors.

#### **5.2.2. Interaction Matrix Reconstruction**

| product_id                              | 609b1b50202d276e56b2468006fa82aa | b57469faf40b556e16b9a49308953700 | 5c973b4202aac5c372a483e712306f4f | 21fb5057dd6a737df6851a7ab7a130da | 0449db5eede617c5fd413071d582f038 |
|-----------------------------------------|-----------------------------------|-----------------------------------|-----------------------------------|-----------------------------------|-----------------------------------|
| customer_unique_id                      |                                   |                                   |                                   |                                   |                                   |
| 93affc403753dca0735740ee2458860a       | 1.0                               | 1.0                               | 1.0                               | 1.0                               | 1.0                               |
| 1610d8d6bc3013aaf6e558ec18cc8350       | 5.0                               | 5.0                               | 5.0                               | 5.0                               | 5.0                               |
| 5a85f706b2059c3cd55287e105d8d5ea       | 3.5                               | 3.5                               | 3.5                               | 3.5                               | 3.5                               |
| fcda364be74555da695b4afe5166c2b1       | 4.0                               | 4.0                               | 4.0                               | 4.0                               | 4.0                               |
| d92462d83762d5e5c112bccc344119e6       | 5.0                               | 5.0                               | 5.0                               | 5.0                               | 5.0                               |

The interaction matrix was reconstructed using the components obtained from Singular Value Decomposition (SVD). This process predicts the missing values in the user-item interaction matrix, enabling recommendations for unseen user-item interactions.

1. **Matrix Reconstruction**
   - The dot product of $U$ (customer latent factors) and $\Sigma$ (singular values) was calculated to obtain $U \Sigma$.
   - The result was further multiplied with $V^T$ (product latent factors), reconstructing the interaction matrix in its centered form $( U \Sigma V^T)$.

2. **Adding Row Means**
   - The row-wise mean ratings (`avg_ratings`) were added back to the reconstructed matrix to restore the original scale of review scores. This adjustment ensures that the predicted values align with the actual rating scale.

3. **Saving Reconstructed Matrix**
   - The final reconstructed matrix, containing predicted review scores for all user-item pairs, was converted into a DataFrame (`predictions_df`) with customers as rows and products as columns.

The `predictions_df` now contains predicted review scores for all customers and products, filling in the missing values in the original matrix. These predictions form the basis for recommending products to customers by identifying those with the highest predicted scores. This reconstructed matrix captures the latent relationships between customers and products, enabling the system to predict customer preferences for products they have not interacted with.

#### **5.2.3. Product Recommendation**

| product_id                              | 175b8225bdd99e95a64e07a6c06e8746 |
|-----------------------------------------|------------------------------------|
| 0011c512eb256aa0dbbb544d8dffcf6e       | 5.0                                |
| aacee6f1f7be193e891a8cca7a3a849a       | 5.0                                |
| abe171a94bee936786955f928bd764ab       | 5.0                                |
| abda62f854cffdc94184e6bccadc2286       | 5.0                                |
| abd7f7a1bd327a8f1f363abfc0f87391       | 5.0                                |

The CF model generates product recommendations for a specific customer based on predicted review scores from the reconstructed interaction matrix.

- A customer ID was randomly selected from those who have provided a review score of 5, ensuring that the recommendations target a highly engaged customer.
- Predicted review scores for all products were retrieved from the `predictions_df` for the selected customer.
- The predicted review scores were sorted in descending order, ranking products from most to least preferred.
- The top 5 products with the highest predicted review scores were selected as recommendations.

CF model’s predict customer preferences based on latent factors derived from the interaction matrix. The selected products are highly tailored to the customer’s inferred interests, enabling a personalized recommendation experience.  

| product_category_name | product_id       | product_description_lenght | product_photos_qty | product_weight_g | volume_cm3 | price  |
|-----------------------|------------------|----------------------------|--------------------|------------------|------------|--------|
| bed_bath_table        | 8d944f9367ba7f153e0ab5b6dc7d063b | 237                        | 1                  | 1500             | 13690      | 114.9  |
| bed_bath_table        | 151d7733b44e0c7b292d7e2efb5424a2 | 305                        | 1                  | 3950             | 19200      | 279.9  |

| product_category_name          | product_id       | product_description_lenght | product_photos_qty | product_weight_g | volume_cm3 | price  |
|--------------------------------|------------------|----------------------------|--------------------|------------------|------------|--------|
| auto                           | 0011c512eb256aa0dbbb544d8dffcf6e | 177                        | 1                  | 100              | 3840       | 52.00  |
| bed_bath_table                 | abe171a94bee936786955f928bd764ab | 553                        | 1                  | 1300             | 7875       | 81.80  |
| housewares                     | abda62f854cffdc94184e6bccadc2286 | 810                        | 4                  | 800              | 16038      | 68.00  |
| fashion_bags_accessories       | aacee6f1f7be193e891a8cca7a3a849a | 726                        | 6                  | 100              | 1584       | 79.90  |
| fashion_bags_accessories       | abd7f7a1bd327a8f1f363abfc0f87391 | 367                        | 1                  | 100              | 612        | 39.99  |

The CF system identified the top 5 recommended products for the customer with ID `175b8225bdd99e95a64e07a6c06e8746`. The recommendations span diverse categories, including **auto**, **housewares**, **fashion_bags_accessories**, and **bed_bath_table**, showcasing the system's ability to balance relevance and exploration.

Features such as **description length**, **photos quantity**, **weight**, **volume**, and **price** vary across the recommended products, introducing diversity while partially aligning with the customer’s previous preferences in the **bed_bath_table** category. The results demonstrate the effectiveness of the CF model in generating personalized recommendations that combine familiar and novel options.

## **6. Evaluation**

### **6.1. Content-Based Filtering**

#### **6.1.1. Customer Profile Recommendation**

The effectiveness of the CBF system was evaluated by analyzing the alignment of recommended products with the customer’s preferences, using both qualitative insights and quantitative metrics.

| Feature             |                   |
|---------------------|-------------------|
| baby                | 0.696             |
| many_photos         | 0.352             |
| medium_volume       | 0.317             |
| long_description    | 0.316             |
| light_weight        | 0.301             |

A customer profile was generated based on the products they have purchased. The profile represents the average attributes of the products, highlighting the customer's preferences.  

- A subset of the TF-IDF DataFrame (`tfidf_df`) was created, containing only the rows corresponding to the products purchased by the customer.  
- The average scores of the features for the purchased products were calculated, forming the customer's profile.  
- Features with the highest average scores indicate the customer's most significant preferences.  

The customer's profile reveals preferences for the following top features.
- **baby**: Indicates interest in baby-related products.  
- **many_photos**: Suggests a preference for products with more images.  
- **medium_volume**: Highlights a tendency toward medium-sized products.  
- **long_description**: Shows a preference for products with detailed descriptions.  
- **light_weight**: Indicates favorability toward lighter products.

| Product ID                              | Similarity Score |
|-----------------------------------------|------------------|
| 103aed057190376889bc9e796affa24e        | 0.975            |
| c8078bb7927143cdb008e3563230ca61        | 0.923            |
| 3b213ba02fcd0799a85c9c3580a3033d        | 0.877            |
| 14ad6805c263d8d758d648f46a06570e        | 0.877            |
| cac9e5692471a0700418aa3400b9b2b1        | 0.877            |

The recommendation system generates product suggestions for a customer by identifying products most similar to their profile based on cosine similarity.  

- The `tfidf_df` was filtered to exclude products the customer has already purchased (`list_of_products_bought`). This ensures recommendations include only new products.  
- The customer's profile vector was compared with the remaining products in the `tfidf_subset_df` using cosine similarity, generating a similarity score for each product.  
- The resulting similarity scores were wrapped in `similarity_df` for easier analysis.  
- The products were sorted in descending order by their similarity scores, ranking the most relevant products at the top.  

The top 5 product recommendations for the customer are identified based on their similarity to the customer’s preferences. These recommendations are tailored to the customer's profile, suggesting products most aligned with their past purchasing behavior and interests.

| Product Category Name | Product ID                             | Product Description Length | Product Photos Quantity | Product Weight (g) | Volume (cm³) | Price  |
|-----------------------|----------------------------------------|----------------------------|--------------------------|--------------------|--------------|--------|
| baby                  | ac946196783ec18b207ead8a00d3f5c4       | 740                        | 3                        | 167                | 4725         | 54.9   |
| baby                  | b865aecbf934fe78d172e5a0f7a73ec0       | 1344                       | 3                        | 400                | 5120         | 149.9  |

| Product Category Name | Product ID                             | Product Description Length | Product Photos Quantity | Product Weight (g) | Volume (cm³) | Price  |
|-----------------------|----------------------------------------|----------------------------|--------------------------|--------------------|--------------|--------|
| baby                  | 103aed057190376889bc9e796affa24e       | 1355                       | 4                        | 332                | 3840         | 149.33 |
| baby                  | 3b213ba02fcd0799a85c9c3580a3033d       | 1640                       | 1                        | 350                | 8000         | 54.90  |
| baby                  | c8078bb7927143cdb008e3563230ca61       | 1978                       | 4                        | 250                | 7865         | 19.90  |
| baby                  | 14ad6805c263d8d758d648f46a06570e       | 782                        | 1                        | 350                | 4536         | 60.66  |
| baby                  | cac9e5692471a0700418aa3400b9b2b1       | 2440                       | 1                        | 375                | 8120         | 119.93 |

The recommendation system generates product suggestions for a customer based on their profile. Below are insights into the purchased and recommended products.

**Customer's Purchased Products**

The customer has previously purchased two products in the **baby** category.
- Products with moderate description lengths (**740** and **1344 characters**).
- Consistent photo count (**3 photos** for both products).
- Lightweight products (**167 g** and **400 g**) with moderate volumes.

**Recommended Products**

The system recommended five new products in the **baby** category, which align with the customer’s preferences.
- Products with detailed descriptions (ranging from **782 to 2440 characters**).
- Varied photo counts (**1 to 4 photos**), including higher photo counts than previously purchased products.
- Lightweight products (weights between **250 g and 375 g**) and moderate to large volumes.

The recommended products closely match the customer’s preference for the **baby** category, lightweight products, and moderate-to-detailed descriptions. The recommendation system effectively suggests products that complement the customer’s profile while introducing new options, such as varying photo counts and slightly different volumes.

#### **6.1.2. Quantitative Evaluation Metrics**

1. **Precision@5**

   Measures the proportion of top-5 recommended products that match the customer's preferences.  

   **Calculation**

   Out of the 5 recommendations, all products matched the customer’s preference for the **baby** category.
   
   $$\text{Precision@5} = \frac{\text{Relevant Recommendations}}{\text{Total Recommendations}} = \frac{5}{5} = 1.0 \, (100\%)$$  

2. **Diversity Score**

   Assesses variation in product features among recommendations.  
   - The recommendations showed variation in **description lengths** (782–2440 characters), **photo counts** (1–4 photos), and **volume** (4536–8120 cm³).  
   - The diversity in features ensures the recommendations cater to the customer’s general preferences while introducing variety.  

3. **Hit Rate**

   Evaluates whether at least one recommended product matches the customer’s true preferences.  
   - All 5 recommendations matched the customer’s interest in the **baby** category, yielding a **Hit Rate = 1.0 (100%)**.  

4. **Mean Reciprocal Rank (MRR)**

   Determines the rank of the first relevant product in the recommendation list. Since all recommendations are relevant.
   
   $$\text{MRR} = \frac{1}{\text{Rank of First Relevant Recommendation}} = 1.0$$  

### **6.2. Collaborative Filtering**

#### **6.2.1. Root Mean Squared Error**

Root Mean Squared Error (RMSE) is a standard metric for evaluating the accuracy of predicted values in regression-based systems, including collaborative filtering using SVD. RMSE provides a measure of the average difference between predicted ratings (from the reconstructed user-item matrix) and actual ratings, making it an essential evaluation method for recommendation systems.

**Mathematical Formula**

$$RMSE = \sqrt{\frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2}$$

Where:
- $y_i$: Actual customer rating for an product.
- $\hat{y}_i$: Predicted rating for the same product (from the reconstructed matrix).
- $n$: Total number of predicted ratings being evaluated.

RMSE outputs a single value that quantifies the prediction error, expressed in the same unit as the ratings (e.g., on a scale of 1 to 5).

```python
# The first 20 prediction values that match the holdout mask.
[5. 4. 5. 3. 5. 2. 1. 1. 1. 5. 1. 1. 5. 3. 4. 4. 5. 4. 5. 5.]

# The first 20 actual values from the holdout set that match the holdout mask.
[5. 4. 5. 3. 5. 2. 1. 1. 1. 5. 1. 1. 5. 3. 3. 5. 4. 4. 5. 5.]
```

The comparison between predicted and actual review scores highlights the collaborative filtering model’s accuracy in approximating customer preferences. Predicted ratings from the reconstructed interaction matrix closely align with actual ratings from the holdout set, with minimal deviations observed. The analysis demonstrates strong performance and forms the basis for calculating evaluation metrics such as RMSE, which quantifies the model's prediction error.

```python
RMSE of SVD Predictions: 0.86
```

The performance of the collaborative filtering model using SVD was evaluated by calculating the Root Mean Squared Error (RMSE) between predicted and actual review scores. The RMSE value of **0.86** indicates the average deviation of the predicted ratings from the actual ratings. This low RMSE demonstrates that the model effectively captures customer preferences and provides accurate predictions, confirming its reliability for generating recommendations.

![RMSE](images/image-6.png)

The RMSE analysis across review scores reveals that the collaborative filtering model performs best for higher ratings. The RMSE for **review score 4** is the lowest at **0.70**, followed by **0.82** for score 5. Prediction errors are higher for lower scores, with RMSE reaching **1.07** for score 1. This indicates the model predicts higher ratings more accurately while struggling with lower ratings.

### **6.3. Insight**

Recommendation system successfully developed using **content-based filtering** and **collaborative filtering** approaches, delivering personalized product suggestions to improve customer satisfaction and merchant success on the Olist platform.  

1. **Content-Based Filtering Results**:
   - Content-based filtering using **TF-IDF** and **cosine similarity** recommended products based on shared attributes such as **category**, **description length**, **number of photos**, **weight**, **volume**, and **price**.
   - The system demonstrated strong relevance by recommending products closely aligned with the customer’s purchasing history while maintaining diversity in features to enhance product discovery. 

2. **Collaborative Filtering Results**:  
   - Collaborative filtering using **Singular Value Decomposition (SVD)** predicted customer review scores for products, enabling recommendations based on latent patterns in customer behavior.  
   - The model achieved an overall **RMSE of 0.86**, indicating accurate prediction performance. Analysis across review scores revealed the model performed best for higher ratings, with the lowest RMSE of **0.70** for review score 4.  

The system effectively balances relevance and diversity, ensuring that customers receive accurate, tailored recommendations while discovering new products. Improved recommendation accuracy supports Olist’s goals of increasing customer engagement, boosting sales, and empowering small businesses across its platform.

## **7. References**

Abdul Hussien, F. T., Rahma, A. M. S., & Abdulwahab, H. B. (2021). An E-Commerce Recommendation System Based on Dynamic Analysis of Customer Behavior. Sustainability, 13(19), 10786. https://doi.org/10.3390/su131910786

datacamp. (2024). https://app.datacamp.com/learn/courses/building-recommendation-engines-in-python

kaggle. (2018). https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

Pu, Li & Faltings, Boi. (2013). Understanding and improving relational matrix factorization in recommender systems. 41-48. https://www.researchgate.net/publication/262311994_Understanding_and_improving_relational_matrix_factorization_in_recommender_systems

Stalidis, G., Karaveli, I., Diamantaras, K., Delianidi, M., Christantonis, K., Tektonidis, D., Katsalis, A., & Salampasis, M. (2023). Recommendation Systems for e-Shopping: Review of Techniques for Retail and Sustainable Marketing. Sustainability, 15(23), 16151. https://doi.org/10.3390/su152316151
