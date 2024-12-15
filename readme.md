# **Laporan Proyek Machine Learning - Kevin Aditya Ikhsan**

## **1. Project Overview**

Olist, the largest department store in Brazilian marketplaces, bridges the gap between small businesses and a wide array of sales channels through a seamless and centralized platform. Merchants on Olist can sell their products easily while leveraging Olist's logistics network for efficient delivery. To enhance customer satisfaction and streamline their experience, Olist collects feedback through post-purchase surveys, providing valuable insights into the shopping experience[[1]](https://doi.org/10.3390/su152316151). These customer interactions present an opportunity to develop personalized recommendations, driving both customer engagement and seller success [[2]](https://doi.org/10.3390/su131910786).

The project focuses on building a recommendation system using content-based filtering and collaborative filtering approaches. A robust recommendation system is essential to enhance the shopping experience, increase customer retention, and support Olist’s mission of empowering small businesses. Leveraging the collected data, the project aims to deliver accurate and personalized recommendations, drawing insights from customer feedback and product attributes to improve user satisfaction and overall marketplace performance.

## **2. Business Understanding**

### **2.1. Problem Statements**

Olist aims to enhance customer satisfaction and drive sales by offering personalized product recommendations. However, the current system lacks a robust mechanism to analyze customer feedback and product attributes for tailored suggestions, potentially leading to missed opportunities for customer retention and merchant success.  

### **2.2. Goals**

The primary goal is to develop a recommendation system that delivers accurate and personalized product suggestions to customers, enhancing their shopping experience while increasing sales for merchants.  

### **2.3. Solution statements**

1. **Content-Based Filtering**: Analyze product attributes and customer preferences to suggest items similar to those they have purchased.
2. **Collaborative Filtering**: Leveraging patterns in customer interactions and purchase history, recommend products based on the behavior and preferences of similar users.  

## **3. Data Understanding**

### **3.1. Datasets**

The dataset used for the project is the [**Brazilian E-Commerce Public Dataset by Olist** [3]](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce). It contains approximately 100,000 orders made at Olist Store between 2016 and 2018. The dataset is anonymized and ideal for developing a recommendation system as it provides valuable insights into customer preferences, product details, and purchasing behavior.

**Tables Used for the Recommendation System**  
1. **Customers**: Contains customer IDs and unique customer identifiers, which are critical for understanding individual purchasing behavior and linking reviews and orders.  
2. **Orders**: Provides order information, including order IDs and customer IDs, which helps in tracking purchasing patterns.  
3. **Order Items**: Includes details about the products purchased in each order, such as product IDs and price, essential for mapping product preferences.  
4. **Order Reviews**: Captures customer feedback in the form of review scores, crucial for building collaborative filtering models by identifying user satisfaction and preferences.  
5. **Products**: Contains product-level information, including product IDs, names, and physical attributes, which can be used for content-based filtering.  
6. **Product Category**: Maps product categories name to English.

The relationships between these tables are illustrated in the dataset's schema. For example, Order Items links orders to specific products, while Customers connects unique buyers to their respective orders and reviews. Understanding dataset structure is essential for designing a recommendation system that effectively integrates both content-based filtering and collaborative filtering techniques.

![Schema](images/image.png)

#### **3.1.1. Customers**

<div id="df-35f78171-b893-49ef-8b0e-bf8317400bd5" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>customer_id</th>
      <th>customer_unique_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>06b8999e2fba1a1fbc88172c00ba8bc7</td>
      <td>861eff4711a542e4b93843c6dd7febb0</td>
    </tr>
  </tbody>
</table>
</div>

<div id="df-d613dc3b-93ca-4b7e-927b-5bc77150b5a7" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Column</th>
      <th>Type</th>
      <th>Null</th>
      <th>Unique</th>
      <th>Unique Values</th>
      <th>Min</th>
      <th>Q1</th>
      <th>Median</th>
      <th>Q3</th>
      <th>Max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>customer_id</td>
      <td>object</td>
      <td>0</td>
      <td>99441</td>
      <td>[06b8999e2fba1a1fbc88172c00ba8bc7, 18955e83d33...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>customer_unique_id</td>
      <td>object</td>
      <td>0</td>
      <td>96096</td>
      <td>[861eff4711a542e4b93843c6dd7febb0, 290c77bc529...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

**Customers Table Information**
- The `customers_df` table consists of **99,441 records** and provides customer-level information.  
- It includes two features:  
  1. **`customer_id`**: A unique identifier for each customer’s order, used to link with the `orders_df` table. There are **99,441 unique values.**
  2. **`customer_unique_id`**: A unique identifier for each customer across all orders. With **96,096 unique values**, indicates that some customers placed multiple orders.  
- The table contains **no missing values.**

**Relationship in the Schema**
- The `customer_id` field connects the `customers_df` table to the `orders_df` table, enabling the tracking of customer behavior and linking orders to specific customers.  

#### **3.1.2. Order Items**

<div id="df-84230aa9-b559-4260-a5e2-11f9c93f2328" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>product_id</th>
      <th>price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>00010242fe8c5a6d1ba2dd792cb16214</td>
      <td>4244733e06e7ecb4970a6e2683c13e61</td>
      <td>58.9</td>
    </tr>
  </tbody>
</table>
</div>

<div id="df-47d60a76-a185-46fe-82e9-9202b6643786" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Column</th>
      <th>Type</th>
      <th>Null</th>
      <th>Unique</th>
      <th>Unique Values</th>
      <th>Min</th>
      <th>Q1</th>
      <th>Median</th>
      <th>Q3</th>
      <th>Max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>order_id</td>
      <td>object</td>
      <td>0</td>
      <td>98666</td>
      <td>[00010242fe8c5a6d1ba2dd792cb16214, 00018f77f2f...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>product_id</td>
      <td>object</td>
      <td>0</td>
      <td>32951</td>
      <td>[4244733e06e7ecb4970a6e2683c13e61, e5f2d52b802...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>price</td>
      <td>float64</td>
      <td>0</td>
      <td>5968</td>
      <td>[58.9, 239.9, 199.0, 12.99, 199.9, 21.9, 19.9,...</td>
      <td>0.85</td>
      <td>39.9</td>
      <td>74.99</td>
      <td>134.9</td>
      <td>6735.0</td>
    </tr>
  </tbody>
</table>
</div>

**Order Items Table Information**
- The `order_items_df` table consists of **112,650 records** and provides item-level details for customer orders.  
- It includes three features:  
  1. **`order_id`**: A unique identifier for each order, used to link with the `orders_df` table. There are **98,666 unique values**, indicating that some orders contain multiple items.  
  2. **`product_id`**: A unique identifier for each product, used to link with the `products_df` table. There are **32,951 unique products** purchased across all orders.  
  3. **`price`**: The price of the purchased item in Brazilian Real. Prices range from **0.85** to **6,735.00**, with a median price of **74.99**.  
- The table contains **no missing values.**

**Relationship in the Schema**
- The `order_id` column connects the `order_items_df` table to the `orders_df` table, enabling the identification of items within each order.  
- The `product_id` column links the `order_items_df` table to the `products_df` table, allowing access to product attributes and categories for each item in an order.

#### **3.1.3. Order Reviews**

<div id="df-c8af3a85-75a0-4971-b78d-a9c630790124" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>review_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>73fc7af87114b39712e6da79b0a377eb</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>

<div id="df-0c8be854-f3af-45ff-97bf-9f91df3381c5" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Column</th>
      <th>Type</th>
      <th>Null</th>
      <th>Unique</th>
      <th>Unique Values</th>
      <th>Min</th>
      <th>Q1</th>
      <th>Median</th>
      <th>Q3</th>
      <th>Max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>order_id</td>
      <td>object</td>
      <td>0</td>
      <td>98673</td>
      <td>[73fc7af87114b39712e6da79b0a377eb, a548910a1c6...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>review_score</td>
      <td>int64</td>
      <td>0</td>
      <td>5</td>
      <td>[4, 5, 1, 3, 2]</td>
      <td>1</td>
      <td>4</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>

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

<div id="df-53b88a1c-7008-4291-b143-61a54c5f3e9f" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>customer_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>e481f51cbdc54678b7cc49136f2d6af7</td>
      <td>9ef432eb6251297304e76186b10a928d</td>
    </tr>
  </tbody>
</table>
</div>

<div id="df-27eec79b-e9e1-46a7-8d1f-ba16088dd259" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Column</th>
      <th>Type</th>
      <th>Null</th>
      <th>Unique</th>
      <th>Unique Values</th>
      <th>Min</th>
      <th>Q1</th>
      <th>Median</th>
      <th>Q3</th>
      <th>Max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>order_id</td>
      <td>object</td>
      <td>0</td>
      <td>99441</td>
      <td>[e481f51cbdc54678b7cc49136f2d6af7, 53cdb2fc8bc...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>customer_id</td>
      <td>object</td>
      <td>0</td>
      <td>99441</td>
      <td>[9ef432eb6251297304e76186b10a928d, b0830fb4747...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

**Orders Table Information**
- The `orders_df` table consists of **99,441 records** and provides information about customer orders.  
- It includes two features:  
  1. **`order_id`**: A unique identifier for each order. There are **99,441 unique values**, indicating that each order is distinct.  
  2. **`customer_id`**: A unique identifier for each customer associated with an order, used to link with the `customers_df` table. There are **99,441 unique values**, ensuring that every order has a corresponding customer.  
- The table contains **no missing values**, making it reliable for linking orders to customers and other related data.  

**Relationship in the Schema**  
- The `order_id` column connects the `orders_df` table to the `order_items_df` and `order_reviews_df` tables, enabling the linkage of orders to specific items and customer reviews.  
- The `customer_id` column connects the `orders_df` table to the `customers_df` table, linking each order to the customer who placed it.  

#### **3.1.5. Products**

<div id="df-bfe66082-18dc-4af7-bae2-b10fcdf0bfdc" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_id</th>
      <th>product_category_name</th>
      <th>product_name_lenght</th>
      <th>product_description_lenght</th>
      <th>product_photos_qty</th>
      <th>product_weight_g</th>
      <th>product_length_cm</th>
      <th>product_height_cm</th>
      <th>product_width_cm</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1e9e8ef04dbcff4541ed26657ea517e5</td>
      <td>perfumaria</td>
      <td>40.0</td>
      <td>287.0</td>
      <td>1.0</td>
      <td>225.0</td>
      <td>16.0</td>
      <td>10.0</td>
      <td>14.0</td>
    </tr>
  </tbody>
</table>
</div>

<div id="df-3ec438e2-a435-409a-93fb-e6de9031288f" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Column</th>
      <th>Type</th>
      <th>Null</th>
      <th>Unique</th>
      <th>Unique Values</th>
      <th>Min</th>
      <th>Q1</th>
      <th>Median</th>
      <th>Q3</th>
      <th>Max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>product_id</td>
      <td>object</td>
      <td>0</td>
      <td>32951</td>
      <td>[1e9e8ef04dbcff4541ed26657ea517e5, 3aa071139cb...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>product_category_name</td>
      <td>object</td>
      <td>610</td>
      <td>73</td>
      <td>[perfumaria, artes, esporte_lazer, bebes, util...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>product_name_lenght</td>
      <td>float64</td>
      <td>610</td>
      <td>66</td>
      <td>[40.0, 44.0, 46.0, 27.0, 37.0, 60.0, 56.0, 57....</td>
      <td>5.0</td>
      <td>42.0</td>
      <td>51.0</td>
      <td>57.0</td>
      <td>76.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>product_description_lenght</td>
      <td>float64</td>
      <td>610</td>
      <td>2960</td>
      <td>[287.0, 276.0, 250.0, 261.0, 402.0, 745.0, 127...</td>
      <td>4.0</td>
      <td>339.0</td>
      <td>595.0</td>
      <td>972.0</td>
      <td>3992.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>product_photos_qty</td>
      <td>float64</td>
      <td>610</td>
      <td>19</td>
      <td>[1.0, 4.0, 2.0, 3.0, 5.0, 9.0, 6.0, nan, 7.0, ...</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>product_weight_g</td>
      <td>float64</td>
      <td>2</td>
      <td>2204</td>
      <td>[225.0, 1000.0, 154.0, 371.0, 625.0, 200.0, 18...</td>
      <td>0.0</td>
      <td>300.0</td>
      <td>700.0</td>
      <td>1900.0</td>
      <td>40425.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>product_length_cm</td>
      <td>float64</td>
      <td>2</td>
      <td>99</td>
      <td>[16.0, 30.0, 18.0, 26.0, 20.0, 38.0, 70.0, 40....</td>
      <td>7.0</td>
      <td>18.0</td>
      <td>25.0</td>
      <td>38.0</td>
      <td>105.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>product_height_cm</td>
      <td>float64</td>
      <td>2</td>
      <td>102</td>
      <td>[10.0, 18.0, 9.0, 4.0, 17.0, 5.0, 24.0, 8.0, 1...</td>
      <td>2.0</td>
      <td>8.0</td>
      <td>13.0</td>
      <td>21.0</td>
      <td>105.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>product_width_cm</td>
      <td>float64</td>
      <td>2</td>
      <td>95</td>
      <td>[14.0, 20.0, 15.0, 26.0, 13.0, 11.0, 44.0, 40....</td>
      <td>6.0</td>
      <td>15.0</td>
      <td>20.0</td>
      <td>30.0</td>
      <td>118.0</td>
    </tr>
  </tbody>
</table>
</div>

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

<div id="df-97662afe-7ac9-417a-91e5-151dd9eeb9c1" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_category_name</th>
      <th>product_category_name_english</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>beleza_saude</td>
      <td>health_beauty</td>
    </tr>
  </tbody>
</table>
</div>

<div id="df-11f8fffe-ba5b-4afd-939b-242252247aae" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Column</th>
      <th>Type</th>
      <th>Null</th>
      <th>Unique</th>
      <th>Unique Values</th>
      <th>Min</th>
      <th>Q1</th>
      <th>Median</th>
      <th>Q3</th>
      <th>Max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>product_category_name</td>
      <td>object</td>
      <td>0</td>
      <td>71</td>
      <td>[beleza_saude, informatica_acessorios, automot...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>product_category_name_english</td>
      <td>object</td>
      <td>0</td>
      <td>71</td>
      <td>[health_beauty, computers_accessories, auto, b...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

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

The analysis of product prices reveals a highly skewed distribution, as shown in the histogram. The majority of product prices are concentrated below **500 BRL**, with only a small number of items priced above **1,000 BRL**. It's suggests that most products are relatively affordable, catering to a broader customer base. The boxplot further highlights the presence of significant outliers, with prices extending up to **6,735 BRL**, representing luxury or specialty items. The interquartile range (IQR) indicates that most product prices fall between approximately **39.9 BRL** and **134.9 BRL**. These insights suggest that while the marketplace primarily targets affordability, the inclusion of high-value products adds diversity to the catalog. These outliers may require careful consideration during the development of the recommendation system to ensure they do not overly influence results.

#### **3.2.2. Review Score**

![Review Scores Distribution](images/image-2.png)

The distribution of review scores provides insights into customer satisfaction levels. The bar chart shows that the majority of reviews, accounting for **57.78% (57,328 reviews)**, have a perfect score of 5, indicating highly satisfied customers. It's is followed by a score of 4, with **19.29% (19,142 reviews)**, also reflecting positive feedback. On the lower end, **11.51% (11,424 reviews)** of reviews have a score of 1, representing dissatisfied customers, while scores of 2 and 3 account for **3.18% (3,151 reviews)** and **8.24% (8,179 reviews)**, respectively.

Review scores distribution demonstrates that the platform generally maintains a high level of customer satisfaction, as more than **77% of reviews** are positive (scores of 4 and 5). However, the presence of a notable proportion of low ratings (1 and 2) suggests areas for improvement in the customer experience, which can be targeted in recommendation strategies.

#### **3.2.3. Products Category**

![Product Category Distribution](images/image-3.png)

The analysis of product category distribution highlights the top 10 most popular categories in the dataset. The **bed, bath, and table** category leads with **3,029 products (9.19%)**, followed by **sports leisure** at **2,867 products (8.70%)**, and **furniture decor** with **2,657 products (8.06%)**. Other notable categories include **health beauty (7.42%)**, **housewares (7.09%)**, and **auto (5.77%)**. The categories **computers accessories**, **toys**, **watches gifts**, and **telephony** each make up between **3.44% and 4.97%** of the total products.

The distribution shows that the marketplace offers a wide range of categories, with household and lifestyle-related items dominating the catalog. These insights are crucial for developing the recommendation system, as they help in identifying key product categories that contribute significantly to customer engagement. Tailoring recommendations based on these popular categories could enhance customer satisfaction and increase sales.

<div id="df-afd54ada-2b52-4086-b316-d7253881c032" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Value</th>
      <th>Potential_Match</th>
      <th>Similarity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>eletrodomesticos</td>
      <td>eletrodomesticos_2</td>
      <td>94.12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>casa_conforto</td>
      <td>casa_conforto_2</td>
      <td>92.86</td>
    </tr>
  </tbody>
</table>
</div>

The analysis of the `product_category_name` in `products_df` and `product_category_df` column identified 2 potential typos: "eletrodomesticos" and "eletrodomesticos_2" (94.12% similarity) and "casa_conforto" and "casa_conforto_2" (92.86% similarity). These findings suggest minor inconsistencies in category naming that may require standardization to ensure data quality and consistency in analysis.

<div id="df-4bb00bde-023b-4888-bf01-1889f8f945d4" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Value</th>
      <th>Potential_Match</th>
      <th>Similarity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>fashion_male_clothing</td>
      <td>fashio_female_clothing</td>
      <td>93.02</td>
    </tr>
    <tr>
      <th>1</th>
      <td>home_appliances</td>
      <td>home_appliances_2</td>
      <td>93.75</td>
    </tr>
  </tbody>
</table>
</div>

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

```python
# List of numerical columns to handle missing values.
numerical_columns = [
    'product_name_lenght',
    'product_description_lenght',
    'product_photos_qty',
]

# Fill missing values in numerical columns with 0.
for col in numerical_columns:
    products_df[col].fillna(0, inplace=True)

# List of measurement columns to check for missing or zero values.
measurement_columns = [
    'product_weight_g',
    'product_length_cm',
    'product_height_cm',
    'product_width_cm'
]

# Drop rows with missing values in measurement columns.
products_df = products_df.dropna(subset=measurement_columns)

# Filter out rows with zero values in measurement columns.
products_df = products_df[(products_df[measurement_columns] != 0).all(axis=1)]
```

Null values in `product_name_lenght`, `product_description_lenght`, and `product_photos_qty` were filled with **0** to retain all products in the dataset. Rows with null or zero values in product measurement columns (`product_weight_g`, `product_length_cm`, `product_height_cm`, `product_width_cm`) were removed to ensure the accuracy of physical product attributes. These steps are necessary to maintain data consistency and reliability for analysis and modeling.

```python
# Convert numerical and measurement columns to integer
for col in numerical_columns + measurement_columns:
    products_df[col] = products_df[col].astype(int)
```

The numerical columns in `products_df`, including `product_name_length`, `product_description_length`, `product_photos_qty`, `product_weight_g`, `product_length_cm`, `product_height_cm`, and `product_width_cm`, were converted to integer format. The transformation optimizes memory usage and ensures consistency, as the data contains only integer-like values.

```python
# Calculate product volume in cubic centimeters.
products_df['volume_cm3'] = (
    products_df['product_length_cm']
    * products_df['product_height_cm']
    * products_df['product_width_cm']
)

# Drop unnecessary columns after volume calculation.
products_df = products_df.drop([
    'product_name_lenght',
    'product_length_cm',
    'product_height_cm',
    'product_width_cm'
], axis=1)
```

A new feature, `volume_cm3`, was created by calculating the product of `product_length_cm`, `product_height_cm`, and `product_width_cm`, representing the product's volume in cubic centimeters. The original columns used for the calculation (`product_length_cm`, `product_height_cm`, `product_width_cm`) were dropped to simplify the dataset and avoid redundancy. The dataset enhances by consolidating dimensional attributes into a single, more useful feature for analysis.

```python
correct_typos(products_df['product_category_name'])
```

```python
Iteration 1: Corrections: 2
No more potential typos found in product_category_name.
```

The `product_category_name` column in `products_df` was cleaned through typo correction. In a single iteration, 2 corrections were made, ensuring consistency and standardization of category names. The cleaning process confirmed that no further typos remain, resulting in a fully standardized `product_category_name` column.

#### **4.2.2. Product Category**

```python
correct_typos(product_category_df['product_category_name'])

product_category_df = product_category_df.drop_duplicates(
    subset='product_category_name'
)
```

```python
Iteration 1: Corrections: 2
No more potential typos found in product_category_name.
```

The `product_category_name` column in `product_category_df` was cleaned through typo correction. In a single iteration, 2 corrections were made, ensuring the consistency and standardization of category names. The cleaning process confirmed that no further typos remain, resulting in a fully standardized `product_category_name` column.

```python
# Correct typo in the product category name for female clothing.
product_category_df['product_category_name_english'] = product_category_df[
    'product_category_name_english'
].replace('fashio_female_clothing', 'fashion_female_clothing')

# Correct typo in the product category name for home appliances.
product_category_df['product_category_name_english'] = product_category_df[
    'product_category_name_english'
].replace('home_appliances_2', 'home_appliances')
```

The `product_category_name_english` column in `product_category_df` was cleaned by replacing "fashio_female_clothing" with "fashion_female_clothing" and "home_appliances_2" with "home_appliances." These corrections ensure consistency and accuracy in the English category names, aligning them with the intended standard naming conventions.

```python
# New categories to add to 'product_category_df'.
new_categories = [
    {'product_category_name': 'portateis_cozinha_e_preparadores_de_alimentos',
     'product_category_name_english': 'portable_kitchen_food_preparers'},
    {'product_category_name': 'pc_gamer',
     'product_category_name_english': 'pc_gamer'},
    {'product_category_name': np.nan,
     'product_category_name_english': 'others'},
]

# Add new categories to the DataFrame.
product_category_df = pd.concat(
    [product_category_df, pd.DataFrame(new_categories)],
    ignore_index=True
)
```

Three new categories were successfully added to `product_category_df` to address previously identified missing entries. These include "portateis_cozinha_e_preparadores_de_alimentos" with its English equivalent "portable_kitchen_food_preparers," "pc_gamer," and a placeholder category for missing or undefined values with the English equivalent "others." The update ensures complete mapping between Portuguese and English category names and resolves gaps in the dataset.

```python
# Merge 'products_df' with 'product_category_df' to get English category names.
products_df = pd.merge(
    product_category_df,
    products_df,
    on='product_category_name',
    how='left'
)

# Drop the original Portuguese category name column.
products_df = products_df.drop('product_category_name', axis=1)

# Rename the English category name column for consistency.
products_df = products_df.rename(
    columns={'product_category_name_english': 'product_category_name'}
)
```

The `products_df` dataset was enriched with English category names by merging it with `product_category_df` using the `product_category_name` column. The original Portuguese category name column was dropped, and the English category name column was renamed to `product_category_name` for consistency. The transformation ensures the dataset is standardized and easier to interpret for further analysis.

### **4.3. Data Merging**

<div id="df-bcf42771-ea7c-4bcd-b37b-4af290e5197e" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>customer_unique_id</th>
      <th>product_id</th>
      <th>price</th>
      <th>review_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>e481f51cbdc54678b7cc49136f2d6af7</td>
      <td>7c396fd4830fd04220f754e42b4e5bff</td>
      <td>87285b34884572647811a353c7ac498a</td>
      <td>29.99</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>

```python
(112372, 5)
```

<div id="df-9af9934d-8af1-4dcf-b3c1-08d58562b702" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Column</th>
      <th>Type</th>
      <th>Null</th>
      <th>Unique</th>
      <th>Unique Values</th>
      <th>Min</th>
      <th>Q1</th>
      <th>Median</th>
      <th>Q3</th>
      <th>Max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>order_id</td>
      <td>object</td>
      <td>0</td>
      <td>97917</td>
      <td>[e481f51cbdc54678b7cc49136f2d6af7, 53cdb2fc8bc...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>customer_unique_id</td>
      <td>object</td>
      <td>0</td>
      <td>94721</td>
      <td>[7c396fd4830fd04220f754e42b4e5bff, af07308b275...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>product_id</td>
      <td>object</td>
      <td>0</td>
      <td>32789</td>
      <td>[87285b34884572647811a353c7ac498a, 595fac2a385...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>price</td>
      <td>float64</td>
      <td>0</td>
      <td>5948</td>
      <td>[29.99, 118.7, 159.9, 45.0, 19.9, 147.9, 49.9,...</td>
      <td>0.85</td>
      <td>39.9</td>
      <td>74.9</td>
      <td>134.9</td>
      <td>6735.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>review_score</td>
      <td>int64</td>
      <td>0</td>
      <td>5</td>
      <td>[4, 5, 2, 1, 3]</td>
      <td>1</td>
      <td>4</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>

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
- **`price`**: Price of each purchased item.  
- **`review_score`**: Customer feedback score for each order.  

Consolidated dataset is essential for connecting customer, product, and review data, forming the foundation for building the recommendation system.

```python
# Count unique products purchased by each customer.
customer_product_counts = orders_df.groupby(
    'customer_unique_id'
)['product_id'].nunique()

# Filter customers who purchased more than one unique product.
filtered_customers = customer_product_counts[customer_product_counts > 1]

# Keep only orders from filtered customers.
orders_df = orders_df[
    orders_df['customer_unique_id'].isin(filtered_customers.index)
]
```

<div id="df-6b0b8278-63e1-4d85-96e5-45693a45169e" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>customer_unique_id</th>
      <th>product_id</th>
      <th>price</th>
      <th>review_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>e481f51cbdc54678b7cc49136f2d6af7</td>
      <td>7c396fd4830fd04220f754e42b4e5bff</td>
      <td>87285b34884572647811a353c7ac498a</td>
      <td>29.99</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>

```python
(13803, 5)
```

<div id="df-ce7e96e8-d499-4a74-b8f2-5caf39cccc32" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Column</th>
      <th>Type</th>
      <th>Null</th>
      <th>Unique</th>
      <th>Unique Values</th>
      <th>Min</th>
      <th>Q1</th>
      <th>Median</th>
      <th>Q3</th>
      <th>Max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>order_id</td>
      <td>object</td>
      <td>0</td>
      <td>8239</td>
      <td>[e481f51cbdc54678b7cc49136f2d6af7, dcb36b511fc...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>customer_unique_id</td>
      <td>object</td>
      <td>0</td>
      <td>5384</td>
      <td>[7c396fd4830fd04220f754e42b4e5bff, ccafc1c3f27...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>product_id</td>
      <td>object</td>
      <td>0</td>
      <td>7796</td>
      <td>[87285b34884572647811a353c7ac498a, 009c09f4399...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>price</td>
      <td>float64</td>
      <td>0</td>
      <td>1919</td>
      <td>[29.99, 132.4, 90.9, 39.5, 14.49, 36.9, 84.9, ...</td>
      <td>0.85</td>
      <td>35.0</td>
      <td>63.3</td>
      <td>109.99</td>
      <td>4799.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>review_score</td>
      <td>int64</td>
      <td>0</td>
      <td>5</td>
      <td>[4, 5, 1, 3, 2]</td>
      <td>1</td>
      <td>2</td>
      <td>4</td>
      <td>5</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>

The dataset was filtered to include only customers who have purchased more than one unique product. The filtered dataset now contains **13,803 records** and **5,384 unique customers**. Noise reduced by focusing on customers with diverse purchasing behaviors, which are more informative for building recommendation models.

```python
# Calculate the average price for each product and round to 2 decimal places.
product_price = orders_df.groupby('product_id').agg(
    price=('price', 'mean'),
).reset_index().round(2)
```

<div id="df-64b38ccc-1c8a-4395-a648-3bce59db3a62" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_id</th>
      <th>price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0011c512eb256aa0dbbb544d8dffcf6e</td>
      <td>52.0</td>
    </tr>
  </tbody>
</table>
</div>

```python
(7796, 2)
```

<div id="df-81df7ffa-bd16-4d4a-9ce2-4f40cdb8a494" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Column</th>
      <th>Type</th>
      <th>Null</th>
      <th>Unique</th>
      <th>Unique Values</th>
      <th>Min</th>
      <th>Q1</th>
      <th>Median</th>
      <th>Q3</th>
      <th>Max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>product_id</td>
      <td>object</td>
      <td>0</td>
      <td>7796</td>
      <td>[0011c512eb256aa0dbbb544d8dffcf6e, 001b72dfd63...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>price</td>
      <td>float64</td>
      <td>0</td>
      <td>2120</td>
      <td>[52.0, 34.99, 32.98, 79.99, 129.9, 39.9, 13.99...</td>
      <td>0.85</td>
      <td>34.9</td>
      <td>64.9</td>
      <td>119.9</td>
      <td>4799.0</td>
    </tr>
  </tbody>
</table>
</div>

- The average price for each product was calculated by grouping the data by `product_id`. Products were grouped by `product_id`, and the mean price for each product was computed.  
- The resulting dataset contains **7,796 unique products** with their average prices.  
- Prices range from **0.85 BRL** to **4,799 BRL**, with a median price of **64.9 BRL**.  

```python
products_df = pd.merge(products_df, product_price, on='product_id')
```

<div id="df-b7a2cadb-4a3c-4662-95a9-608392887502" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_category_name</th>
      <th>product_id</th>
      <th>product_description_lenght</th>
      <th>product_photos_qty</th>
      <th>product_weight_g</th>
      <th>volume_cm3</th>
      <th>price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>health_beauty</td>
      <td>50556c630443502c11acde1c320fe278</td>
      <td>257</td>
      <td>1</td>
      <td>250</td>
      <td>640</td>
      <td>129.9</td>
    </tr>
  </tbody>
</table>
</div>

```python
(7795, 7)
```

<div id="df-aee5a552-ee1a-445b-be7c-f4811ea717b7" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Column</th>
      <th>Type</th>
      <th>Null</th>
      <th>Unique</th>
      <th>Unique Values</th>
      <th>Min</th>
      <th>Q1</th>
      <th>Median</th>
      <th>Q3</th>
      <th>Max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>product_category_name</td>
      <td>object</td>
      <td>0</td>
      <td>69</td>
      <td>[health_beauty, computers_accessories, auto, b...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>product_id</td>
      <td>object</td>
      <td>0</td>
      <td>7795</td>
      <td>[50556c630443502c11acde1c320fe278, 12827caf723...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>product_description_lenght</td>
      <td>int64</td>
      <td>0</td>
      <td>1802</td>
      <td>[257, 3939, 677, 1935, 763, 991, 572, 803, 180...</td>
      <td>0</td>
      <td>296</td>
      <td>528</td>
      <td>878</td>
      <td>3976</td>
    </tr>
    <tr>
      <th>3</th>
      <td>product_photos_qty</td>
      <td>int64</td>
      <td>0</td>
      <td>17</td>
      <td>[1, 6, 3, 2, 5, 4, 7, 10, 9, 8, 11, 13, 12, 15...</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>17</td>
    </tr>
    <tr>
      <th>4</th>
      <td>product_weight_g</td>
      <td>int64</td>
      <td>0</td>
      <td>955</td>
      <td>[250, 150, 750, 300, 217, 350, 2550, 100, 975,...</td>
      <td>50</td>
      <td>300</td>
      <td>650</td>
      <td>1647</td>
      <td>30000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>volume_cm3</td>
      <td>int64</td>
      <td>0</td>
      <td>1932</td>
      <td>[640, 4368, 4096, 4455, 4000, 3315, 3332, 990,...</td>
      <td>352</td>
      <td>2576</td>
      <td>6300</td>
      <td>16500</td>
      <td>288000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>price</td>
      <td>float64</td>
      <td>0</td>
      <td>2120</td>
      <td>[129.9, 57.5, 35.0, 139.56, 55.5, 35.79, 37.9,...</td>
      <td>0.85</td>
      <td>34.9</td>
      <td>64.9</td>
      <td>119.9</td>
      <td>4799.0</td>
    </tr>
  </tbody>
</table>
</div>

The `products_df` dataset was enriched by merging it with the product price dataset. The merge linked `product_id` column in both datasets , adding the average price of each product to the `products_df`.

- The merged dataset contains **7,795 records** and **7 columns**, including product attributes such as category, weight, volume, and now the average price (`price`).  
- No missing values were introduced during the merge, ensuring data completeness.

### **4.4. Content-Based Filtering**

```python
# Categorize product descriptions into short, medium, and long categories.
products_df['description_category'] = pd.qcut(
    products_df['product_description_lenght'],
    q=3,
    labels=["short_description", "medium_description", "long_description"]
)

# Categorize product photos into few, moderate, and many photos.
products_df['photos_category'] = pd.qcut(
    products_df['product_photos_qty'],
    q=3,
    labels=["few_photos", "moderate_photos", "many_photos"]
)

# Categorize product weights into light, medium, and heavy categories.
products_df['weight_category'] = pd.qcut(
    products_df['product_weight_g'],
    q=3,
    labels=["light_weight", "medium_weight", "heavy_weight"]
)

# Categorize product volumes into small, medium, and large categories.
products_df['volume_category'] = pd.qcut(
    products_df['volume_cm3'],
    q=3,
    labels=["small_volume", "medium_volume", "large_volume"]
)

# Categorize product prices into low, medium, and high price categories.
products_df['price_category'] = pd.qcut(
    products_df['price'],
    q=3,
    labels=["low_price", "medium_price", "high_price"]
)
```

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

<div id="df-927272fc-99b3-4635-b509-abbd67edf3a4" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>column</th>
      <th>Q1</th>
      <th>Q2</th>
      <th>Q3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>product_description_lenght</td>
      <td>365</td>
      <td>713</td>
      <td>3976</td>
    </tr>
    <tr>
      <th>1</th>
      <td>product_photos_qty</td>
      <td>1</td>
      <td>2</td>
      <td>17</td>
    </tr>
    <tr>
      <th>2</th>
      <td>product_weight_g</td>
      <td>375</td>
      <td>1150</td>
      <td>30000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>volume_cm3</td>
      <td>3564</td>
      <td>11352</td>
      <td>288000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>price</td>
      <td>42</td>
      <td>94</td>
      <td>4799</td>
    </tr>
  </tbody>
</table>
</div>

The quantiles for key numerical variables were calculated to establish thresholds for categorizing them into groups for content-based filtering. The results for each variable are as follows above. These thresholds define the boundaries for the categories created in the previous step, ensuring that the grouping is based on the natural distribution of each variable. Data-driven segmentation improves the precision and relevance of the content-based filtering approach.

```python
# Drop numeric columns and store the result in cb_data.
cb_data = products_df.drop(
    products_df.select_dtypes(include=np.number).columns,
    axis=1
)

# Drop categorical columns from products_df.
products_df = products_df.drop(
    products_df.select_dtypes(include='category').columns,
    axis=1
)
```

<div id="df-5fb0d542-2fe8-4036-885d-b2417d7b6824" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_category_name</th>
      <th>product_id</th>
      <th>description_category</th>
      <th>photos_category</th>
      <th>weight_category</th>
      <th>volume_category</th>
      <th>price_category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>health_beauty</td>
      <td>50556c630443502c11acde1c320fe278</td>
      <td>short_description</td>
      <td>few_photos</td>
      <td>light_weight</td>
      <td>small_volume</td>
      <td>high_price</td>
    </tr>
    <tr>
      <th>1</th>
      <td>health_beauty</td>
      <td>12827caf723c4ff8030d64dcffe3cdab</td>
      <td>long_description</td>
      <td>many_photos</td>
      <td>light_weight</td>
      <td>medium_volume</td>
      <td>medium_price</td>
    </tr>
    <tr>
      <th>2</th>
      <td>health_beauty</td>
      <td>4a37bc772078a013df64fd661ae5ee6c</td>
      <td>medium_description</td>
      <td>few_photos</td>
      <td>medium_weight</td>
      <td>medium_volume</td>
      <td>low_price</td>
    </tr>
    <tr>
      <th>3</th>
      <td>health_beauty</td>
      <td>dbbda25f6b7c74669cf2203b2e3139a0</td>
      <td>long_description</td>
      <td>many_photos</td>
      <td>light_weight</td>
      <td>medium_volume</td>
      <td>high_price</td>
    </tr>
    <tr>
      <th>4</th>
      <td>health_beauty</td>
      <td>2fffbf105cbbc6e86144f00ecf571635</td>
      <td>long_description</td>
      <td>few_photos</td>
      <td>light_weight</td>
      <td>medium_volume</td>
      <td>medium_price</td>
    </tr>
  </tbody>
</table>
</div>

The dataset was prepared for content-based filtering by separating categorical and numerical data.
- The `cb_data` dataset contains categorical features for modeling in content-based filtering.  
- The `products_df` remains as a separate dataset, focusing on numerical attributes for other analyses.  

```python
# Combine multiple categorical features into a single feature.
cb_data['combined_features'] = (
    cb_data['product_category_name'].astype(str) + ' ' +
    cb_data['description_category'].astype(str) + ' ' +
    cb_data['photos_category'].astype(str) + ' ' +
    cb_data['weight_category'].astype(str) + ' ' +
    cb_data['volume_category'].astype(str) + ' ' +
    cb_data['price_category'].astype(str)
)

# Keep only product_id and the combined_features column.
cb_data = cb_data[['product_id', 'combined_features']]
```

<div id="df-222258c6-4b82-4f75-8fc2-8a1a549b0828" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_id</th>
      <th>combined_features</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>50556c630443502c11acde1c320fe278</td>
      <td>health_beauty short_description few_photos lig...</td>
    </tr>
  </tbody>
</table>
</div>

```python
(7795, 2)
```

<div id="df-f267617c-a62d-4f1e-80dd-9c70bcb13e4c" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Column</th>
      <th>Type</th>
      <th>Null</th>
      <th>Unique</th>
      <th>Unique Values</th>
      <th>Min</th>
      <th>Q1</th>
      <th>Median</th>
      <th>Q3</th>
      <th>Max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>product_id</td>
      <td>object</td>
      <td>0</td>
      <td>7795</td>
      <td>[50556c630443502c11acde1c320fe278, 12827caf723...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>combined_features</td>
      <td>object</td>
      <td>0</td>
      <td>2254</td>
      <td>[health_beauty short_description few_photos li...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

A new column, `combined_features`, was created to consolidate all categorical attributes into a single textual representation for each product. The categorical attributes were concatenated into a single string per product. Textual representation captures all the relevant features for content-based filtering, allowing for easier similarity comparisons.  

- The dataset contains **7,795 records** with no missing values.  
- There are **2,254 unique combinations** of product features, reflecting the diversity of products in the dataset.  

Dataset is prepared and now ready for modeling in content-based filtering, where similarity between products will be calculated based on their combined textual features.

### **4.5. Collaborative Filtering**

```python
# Create a pivot table for collaborative filtering with customers as rows,
# products as columns, and mean review scores as values.
cf_data = orders_df.pivot_table(
    index='customer_unique_id',
    columns='product_id',
    values='review_score',
    aggfunc='mean',
)
```

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

The user-item matrix enables collaborative filtering models to predict ratings for products that customers have not interacted with by analyzing patterns in existing customer-product interactions.

```python
# Define the number of rows and columns to mask (25% of the dataframe).
rows = -int(0.5 * cf_data.shape[0])
cols = -int(0.5 * cf_data.shape[1])

# Create a copy of the dataframe and mask the first 50% of rows and columns.
training_set = cf_data.copy()
training_set.iloc[:rows, :cols] = np.nan

# Drop rows where all values are NaN.
indices = training_set[training_set.isna().all(axis=1)].index
training_set = training_set.drop(indices)

# Extract the dataframe values and create a mask for non-NaN values.
training_set_value = training_set.values
training_mask = ~np.isnan(training_set_value)
```

![Holdout Set](images/image-4.png)

The user-item matrix was split to create a training set for collaborative filtering modeling [[4]](https://app.datacamp.com/learn/courses/building-recommendation-engines-in-python).

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

```python
# Holdout set excluding rows with all NaN values after the split.
holdout_set = cf_data.copy()
holdout_set = holdout_set.drop(indices)

# Extract values and create a mask for the first 50% of rows and columns.
holdout_set_value = holdout_set.iloc[:rows, :cols].values
holdout_mask = ~np.isnan(holdout_set_value)
```

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
# Calculate average review scores per customer.
avg_review_scores = training_set.mean(axis=1)

# Center the training set by subtracting the average review scores.
training_set_centered = training_set.sub(avg_review_scores, axis=0)

# Replace NaN values with 0 in the centered training set.
training_set_centered = training_set_centered.fillna(0)
```

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
    
    $$
    TF(t, d) = \frac{\text{Number of times term } t \text{ appears in document } d}{\text{Total number of terms in document } d}
    $$

2. **Inverse Document Frequency (IDF)**
   - Weighs terms by their rarity across all documents in the corpus. Rare terms are given higher weights, while common terms receive lower weights.  
   - **Mathematical Formula**
     
     $$
     IDF(t) = \log \left( \frac{\text{Total number of documents}}{\text{Number of documents containing term } t} \right) + 1
     $$

3. **TF-IDF Score**
   - Combines TF and IDF to assign a final weight to each term.  
   - **Mathematical Formula**
     
     $$
     TF\text{-}IDF(t, d) = TF(t, d) \times IDF(t)
     $$

```python
# Instantiate the vectorizer object to the tf variable.
tf = TfidfVectorizer()

# Fit and transform the combined_features column.
vectorized_data = tf.fit_transform(cb_data['combined_features'])
```

The TF-IDF approach prioritizes meaningful and unique terms in the product descriptions while minimizing the influence of common terms. This representation is fundamental for calculating product similarities, enabling the recommendation system to identify and recommend similar products effectively.

1. **Vectorization**
   - The `TfidfVectorizer` object was instantiated and applied to the `combined_features` column of `cb_data`.  
   - Each term was assigned a weight based on its TF-IDF score, capturing its importance relative to the product it belongs to and its rarity in the entire dataset.  

2. **Feature Extraction**
   - Distinct terms (features) were extracted from the combined features text.  
   - Products were represented as sparse vectors in a high-dimensional space, where each dimension corresponds to a term's TF-IDF weight.

```python
# Create Dataframe from TF-IDF array.
tfidf_df = pd.DataFrame(
    vectorized_data.toarray(),
    columns=tf.get_feature_names_out()
)

# Assign the product_id to the index.
tfidf_df.index = cb_data['product_id']
```

<div id="df-ceb8f8d4-6192-4098-abfd-a8ddcab16b7a" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>short_description</th>
      <th>agro_industry_and_commerce</th>
      <th>market_place</th>
      <th>drinks</th>
      <th>cine_photo</th>
    </tr>
    <tr>
      <th>product_id</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>e799a2b8707a6a256fa3c040b75d2713</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1c6fb703c624b381a20f21f757694866</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6d0a373c460a041c86167a92a5d3383e</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>40678c9096047877fa74a25cc6f0a726</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>b8a0d73b2a06e7910d9864dccdb0cda2</th>
      <td>0.358</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>

The TF-IDF matrix was converted into a DataFrame for easier processing.

- The TF-IDF sparse matrix was transformed into a dense DataFrame with terms as columns and TF-IDF scores as values.
- The `product_id` was set as the index to link each row to a specific product.

The DataFrame represents products as numerical vectors based on TF-IDF scores, making it ready for similarity calculations and recommendations.

#### **5.1.2. Cosine Similarity**

Cosine similarity measures the cosine of the angle between two vectors in a multi-dimensional space. It determines how similar two vectors are, regardless of their magnitude, making it ideal for comparing textual data represented as TF-IDF vectors.  

**Mathematical Formula**

$$
\text{Cosine Similarity} = \frac{\mathbf{A} \cdot \mathbf{B}}{\|\mathbf{A}\| \|\mathbf{B}\|}
$$
Where:  
- $\mathbf{A}$ and $\mathbf{B}$ are the TF-IDF vectors of two products.  
- $\|\mathbf{A}\|$ and $\|\mathbf{B}\|$ are the magnitudes (norms) of the vectors.  
- $\mathbf{A} \cdot \mathbf{B}$ is the dot product of the vectors.  

The similarity ranges from **0** (completely dissimilar) to **1** (identical), making it easy to interpret.  

```python
# Create the array of cosine similarity values.
cosine_similarity_array = cosine_similarity(tfidf_df)

# Wrap the array in a DataFrame.
cosine_similarity_df = pd.DataFrame(
    cosine_similarity_array,
    index=tfidf_df.index,
    columns=tfidf_df.index
)
```

<div id="df-d7dc4a9e-65a4-4036-b512-8fb13907f166" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>e799a2b8707a6a256fa3c040b75d2713</th>
      <th>1c6fb703c624b381a20f21f757694866</th>
      <th>6d0a373c460a041c86167a92a5d3383e</th>
      <th>40678c9096047877fa74a25cc6f0a726</th>
      <th>b8a0d73b2a06e7910d9864dccdb0cda2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>e799a2b8707a6a256fa3c040b75d2713</th>
      <td>1.000</td>
      <td>0.231</td>
      <td>1.000</td>
      <td>0.113</td>
      <td>0.131</td>
    </tr>
    <tr>
      <th>1c6fb703c624b381a20f21f757694866</th>
      <td>0.231</td>
      <td>1.000</td>
      <td>0.231</td>
      <td>0.271</td>
      <td>0.114</td>
    </tr>
    <tr>
      <th>6d0a373c460a041c86167a92a5d3383e</th>
      <td>1.000</td>
      <td>0.231</td>
      <td>1.000</td>
      <td>0.113</td>
      <td>0.131</td>
    </tr>
    <tr>
      <th>40678c9096047877fa74a25cc6f0a726</th>
      <td>0.113</td>
      <td>0.271</td>
      <td>0.113</td>
      <td>1.000</td>
      <td>0.000</td>
    </tr>
    <tr>
      <th>b8a0d73b2a06e7910d9864dccdb0cda2</th>
      <td>0.131</td>
      <td>0.114</td>
      <td>0.131</td>
      <td>0.000</td>
      <td>1.000</td>
    </tr>
  </tbody>
</table>
</div>

The cosine similarity matrix was generated to compute the similarity between products based on their TF-IDF vectors.

- The `cosine_similarity` function was applied to the TF-IDF matrix, generating a square matrix where each entry represents the similarity between two products.
- The similarity matrix was converted into a DataFrame, with rows and columns indexed by `product_id`. This allows for intuitive lookup of similarity scores between specific products.

The `cosine_similarity_df` provides pairwise similarity scores between products. These scores enable the recommendation system to identify and rank products most similar to a given product based on their features, forming the foundation of the CBF approach.

#### **5.1.3. Product Recommendation**

```python
# Find the values for the product '4d5bb93bfa70f67cda10b1428f2a252c'
cosine_similarity_series = cosine_similarity_df.loc[random_product_id]
cosine_similarity_series = cosine_similarity_series.drop(random_product_id)

# Sort these values highest to lowest and keep top 5
ordered_similarities = cosine_similarity_series.sort_values(ascending=False)
ordered_similarities = ordered_similarities.head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>4d5bb93bfa70f67cda10b1428f2a252c</th>
    </tr>
    <tr>
      <th>product_id</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>e65ab426efaf65e083b917d2e9eac80d</th>
      <td>0.940</td>
    </tr>
    <tr>
      <th>1589d392201547c9b78a117c0e2191dd</th>
      <td>0.881</td>
    </tr>
    <tr>
      <th>44feb28a74abb0f2f303412d60160750</th>
      <td>0.881</td>
    </tr>
    <tr>
      <th>652683d85a89f3b134e44989a601bc58</th>
      <td>0.881</td>
    </tr>
    <tr>
      <th>1da502ed939a029f4bee6cdc8341b4ac</th>
      <td>0.821</td>
    </tr>
  </tbody>
</table>
</div><br>

The recommendation system generates product suggestions by identifying the most similar products to a given one based on cosine similarity scores.

- The cosine similarity scores for the product with ID `4d5bb93bfa70f67cda10b1428f2a252c` were retrieved from the `cosine_similarity_df`.
- The similarity scores were sorted in descending order, ranking products from most to least similar to the selected product.
- The top 5 most similar products were selected, representing the closest matches based on their features.

The system outputs a ranked list of the top 5 recommended products, based on their similarity scores to the selected product. These recommendations demonstrate the practical application of CBF for suggesting similar items to customers.

```python
products_df[products_df['product_id'] == random_product_id]
```

<div id="df-3da0cb4f-eeaf-48b1-91b1-43cbc69aedba" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_category_name</th>
      <th>product_id</th>
      <th>product_description_lenght</th>
      <th>product_photos_qty</th>
      <th>product_weight_g</th>
      <th>volume_cm3</th>
      <th>price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>7270</th>
      <td>books_technical</td>
      <td>4d5bb93bfa70f67cda10b1428f2a252c</td>
      <td>250</td>
      <td>1</td>
      <td>500</td>
      <td>5808</td>
      <td>130.8</td>
    </tr>
  </tbody>
</table>
</div>

```python
products_df[products_df['product_id'].isin(ordered_similarities.index)]
```

<div id="df-19c2ca12-4008-4d2e-999e-7dfb58be7705" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_category_name</th>
      <th>product_id</th>
      <th>product_description_lenght</th>
      <th>product_photos_qty</th>
      <th>product_weight_g</th>
      <th>volume_cm3</th>
      <th>price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>7259</th>
      <td>books_technical</td>
      <td>44feb28a74abb0f2f303412d60160750</td>
      <td>1120</td>
      <td>1</td>
      <td>500</td>
      <td>1350</td>
      <td>127.00</td>
    </tr>
    <tr>
      <th>7260</th>
      <td>books_technical</td>
      <td>1589d392201547c9b78a117c0e2191dd</td>
      <td>1499</td>
      <td>1</td>
      <td>450</td>
      <td>4356</td>
      <td>24.97</td>
    </tr>
    <tr>
      <th>7263</th>
      <td>books_technical</td>
      <td>e65ab426efaf65e083b917d2e9eac80d</td>
      <td>445</td>
      <td>1</td>
      <td>500</td>
      <td>6292</td>
      <td>147.99</td>
    </tr>
    <tr>
      <th>7265</th>
      <td>books_technical</td>
      <td>1da502ed939a029f4bee6cdc8341b4ac</td>
      <td>1345</td>
      <td>1</td>
      <td>600</td>
      <td>1104</td>
      <td>29.45</td>
    </tr>
    <tr>
      <th>7269</th>
      <td>books_technical</td>
      <td>652683d85a89f3b134e44989a601bc58</td>
      <td>246</td>
      <td>1</td>
      <td>1100</td>
      <td>1764</td>
      <td>34.30</td>
    </tr>
  </tbody>
</table>
</div>

The CBF system identified the top 5 most similar products to the product with ID `4d5bb93bfa70f67cda10b1428f2a252c`. All recommended products belong to the same category, **books_technical**, demonstrating the system's ability to effectively identify similar items.  

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

1. **E-Commerce**: Suggesting products based on purchase history or user ratings.  
2. **Streaming Services**: Recommending movies, music, or TV shows based on user preferences.  
3. **Social Media**: Suggesting friends, groups, or content based on shared interests or behaviors.  
4. **Online Education**: Proposing courses or study materials based on interactions and ratings.

CF is a widely used recommendation technique due to its flexibility and ability to leverage implicit patterns in user behavior, making it ideal for scenarios where explicit item features are limited.

#### **5.2.1. Singular Value Decomposition**

Singular Value Decomposition (SVD) is a mathematical technique used to factorize a matrix into three components: two orthogonal matrices and a diagonal matrix of singular values. In collaborative filtering, SVD is applied to the user-item interaction matrix to uncover latent factors that represent hidden relationships between users and items.

**Mathematical Formula**

For a given matrix $R$ of dimensions $m \times n$ (e.g., a user-item matrix):
$$
R = U \Sigma V^T
$$
Where:
- $U$ is an $m \times k$ matrix containing the left singular vectors, representing user latent factors.
- $\Sigma$ is a $k \times k$ diagonal matrix of singular values, representing the importance of each latent factor.
- $V^T$ is a $k \times n$ matrix containing the right singular vectors, representing item latent factors.
- $k$ is the rank of the matrix (or a reduced dimension for approximation).

**Core Concepts**

- **Latent Factor Discovery**

    SVD decomposes the interaction matrix into latent factors for users and items, which capture hidden patterns such as user preferences and item characteristics.

- **Dimensionality Reduction**

    The decomposition allows retaining only the top $k$ singular values and their associated vectors, effectively reducing noise and focusing on the most significant relationships.

- **Reconstruction for Prediction**

    An approximation of the original matrix can be reconstructed using a subset of the singular values.
    $$
    \hat{R} = U_k \Sigma_k V_k^T
    $$
    This reconstruction predicts missing values in the user-item matrix.

![SVD](images/image-5.png)

SVD is a foundational technique in recommendation systems, enabling robust prediction of missing values and enhancing the ability to personalize recommendations based on hidden user and item characteristics [[4]](https://app.datacamp.com/learn/courses/building-recommendation-engines-in-python).

```python
# Decompose the matrix.
U, sigma, Vt = svds(training_set_centered.values)

# Convert sigma into a diagonal matrix.
sigma = np.diag(sigma)
```

```python
[[3.464 0.    0.    0.    0.    0.   ]
 [0.    3.464 0.    0.    0.    0.   ]
 [0.    0.    3.476 0.    0.    0.   ]
 [0.    0.    0.    4.085 0.    0.   ]
 [0.    0.    0.    0.    4.102 0.   ]
 [0.    0.    0.    0.    0.    4.155]]
 ```

The Singular Value Decomposition (SVD) process was applied to decompose the user-item interaction matrix into three components:

1. **Decomposing the Matrix**
   - The `training_set_centered` matrix was decomposed using the `svds` function, resulting in three matrices.
     - $U$: Contains user latent factors.
     - $\Sigma$: A diagonal matrix of singular values representing the importance of each latent factor.
     - $V^T$: Contains item latent factors.

2. **Converting $\Sigma$ into a Diagonal Matrix**
   - The singular values extracted by `svds` were converted into a diagonal matrix using `np.diag`, making them ready for reconstruction or dimensionality reduction.

The diagonal matrix $\Sigma$ indicates the significance of the corresponding latent factors in explaining variations in the data. Larger singular values (e.g., 4.155) signify more influential latent factors, which are critical for reconstructing the user-item matrix and predicting missing interactions.
This decomposition lays the foundation for generating recommendations by reconstructing the interaction matrix with the dominant latent factors.

#### **5.2.2. Interaction Matrix Reconstruction**

```python
# Dot product of U and sigma.
U_sigma = np.dot(U, sigma)

# Dot product of result and Vt.
U_sigma_Vt = np.dot(U_sigma, Vt)

# Add back on the row means contained in avg_ratings.
predicted_review_scores = U_sigma_Vt + avg_review_scores.values.reshape(-1, 1)

# Create DataFrame of the recalculated_review_scores.
predictions_df = pd.DataFrame(
    predicted_review_scores,
    index=training_set.index,
    columns=training_set.columns
)
```

<div id="df-c3a9332c-5037-4387-97e4-753e232702db" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>product_id</th>
      <th>609b1b50202d276e56b2468006fa82aa</th>
      <th>b57469faf40b556e16b9a49308953700</th>
      <th>5c973b4202aac5c372a483e712306f4f</th>
      <th>21fb5057dd6a737df6851a7ab7a130da</th>
      <th>0449db5eede617c5fd413071d582f038</th>
    </tr>
    <tr>
      <th>customer_unique_id</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>93affc403753dca0735740ee2458860a</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1610d8d6bc3013aaf6e558ec18cc8350</th>
      <td>5.0</td>
      <td>5.0</td>
      <td>5.0</td>
      <td>5.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>5a85f706b2059c3cd55287e105d8d5ea</th>
      <td>3.5</td>
      <td>3.5</td>
      <td>3.5</td>
      <td>3.5</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>fcda364be74555da695b4afe5166c2b1</th>
      <td>4.0</td>
      <td>4.0</td>
      <td>4.0</td>
      <td>4.0</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>d92462d83762d5e5c112bccc344119e6</th>
      <td>5.0</td>
      <td>5.0</td>
      <td>5.0</td>
      <td>5.0</td>
      <td>5.0</td>
    </tr>
  </tbody>
</table>
</div>

The interaction matrix was reconstructed using the components obtained from Singular Value Decomposition (SVD). This process predicts the missing values in the user-item interaction matrix, enabling recommendations for unseen user-item interactions.

1. **Matrix Reconstruction**
   - The dot product of $U$ (user latent factors) and $\Sigma$ (singular values) was calculated to obtain $U \Sigma$.
   - The result was further multiplied with $V^T$ (item latent factors), reconstructing the interaction matrix in its centered form $( U \Sigma V^T)$.

2. **Adding Row Means**
   - The row-wise mean ratings (`avg_ratings`) were added back to the reconstructed matrix to restore the original scale of review scores. This adjustment ensures that the predicted values align with the actual rating scale.

3. **Saving Reconstructed Matrix**
   - The final reconstructed matrix, containing predicted review scores for all user-item pairs, was converted into a DataFrame (`predictions_df`) with users as rows and items as columns.

The `predictions_df` now contains predicted review scores for all users and items, filling in the missing values in the original matrix. These predictions form the basis for recommending items to users by identifying those with the highest predicted scores. This reconstructed matrix captures the latent relationships between users and items, enabling the system to predict user preferences for items they have not interacted with.

#### **5.2.3. Product Recommendation**

```python
# Get sorted predictions for customer '175b8225bdd99e95a64e07a6c06e8746.
recommendations = predictions_df.loc[
    random_customer_id
].sort_values(ascending=False)

# Select the top recommendations.
recommendations = recommendations.head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>175b8225bdd99e95a64e07a6c06e8746</th>
    </tr>
    <tr>
      <th>product_id</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0011c512eb256aa0dbbb544d8dffcf6e</th>
      <td>5.0</td>
    </tr>
    <tr>
      <th>aacee6f1f7be193e891a8cca7a3a849a</th>
      <td>5.0</td>
    </tr>
    <tr>
      <th>abe171a94bee936786955f928bd764ab</th>
      <td>5.0</td>
    </tr>
    <tr>
      <th>abda62f854cffdc94184e6bccadc2286</th>
      <td>5.0</td>
    </tr>
    <tr>
      <th>abd7f7a1bd327a8f1f363abfc0f87391</th>
      <td>5.0</td>
    </tr>
  </tbody>
</table>
</div><br>

The CF model generates product recommendations for a specific customer based on predicted review scores from the reconstructed interaction matrix.

- A customer ID was randomly selected from those who have provided a review score of 5, ensuring that the recommendations target a highly engaged user.
- Predicted review scores for all products were retrieved from the `predictions_df` for the selected customer.
- The predicted review scores were sorted in descending order, ranking products from most to least preferred.
- The top 5 products with the highest predicted review scores were selected as recommendations.

CF model’s predict user preferences based on latent factors derived from the interaction matrix. The selected items are highly tailored to the customer’s inferred interests, enabling a personalized recommendation experience.  

```python
# Get a list of products bought by the random customer.
list_of_products_bought = orders_df[
    orders_df['customer_unique_id'] == random_customer_id
]['product_id'].tolist()

# Filter and display product details for the products bought.
products_df[products_df['product_id'].isin(list_of_products_bought)]
```

<div id="df-381e4dcd-4836-41df-ad7d-99fba6531bdc" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_category_name</th>
      <th>product_id</th>
      <th>product_description_lenght</th>
      <th>product_photos_qty</th>
      <th>product_weight_g</th>
      <th>volume_cm3</th>
      <th>price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1824</th>
      <td>bed_bath_table</td>
      <td>8d944f9367ba7f153e0ab5b6dc7d063b</td>
      <td>237</td>
      <td>1</td>
      <td>1500</td>
      <td>13690</td>
      <td>114.9</td>
    </tr>
    <tr>
      <th>2208</th>
      <td>bed_bath_table</td>
      <td>151d7733b44e0c7b292d7e2efb5424a2</td>
      <td>305</td>
      <td>1</td>
      <td>3950</td>
      <td>19200</td>
      <td>279.9</td>
    </tr>
  </tbody>
</table>
</div>

```python
products_df[products_df['product_id'].isin(recommendations.index)]
```

<div id="df-2227fb43-045a-45d0-ad0d-955a08eee582" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_category_name</th>
      <th>product_id</th>
      <th>product_description_lenght</th>
      <th>product_photos_qty</th>
      <th>product_weight_g</th>
      <th>volume_cm3</th>
      <th>price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>795</th>
      <td>auto</td>
      <td>0011c512eb256aa0dbbb544d8dffcf6e</td>
      <td>177</td>
      <td>1</td>
      <td>100</td>
      <td>3840</td>
      <td>52.00</td>
    </tr>
    <tr>
      <th>1284</th>
      <td>bed_bath_table</td>
      <td>abe171a94bee936786955f928bd764ab</td>
      <td>553</td>
      <td>1</td>
      <td>1300</td>
      <td>7875</td>
      <td>81.80</td>
    </tr>
    <tr>
      <th>4487</th>
      <td>housewares</td>
      <td>abda62f854cffdc94184e6bccadc2286</td>
      <td>810</td>
      <td>4</td>
      <td>800</td>
      <td>16038</td>
      <td>68.00</td>
    </tr>
    <tr>
      <th>5990</th>
      <td>fashion_bags_accessories</td>
      <td>aacee6f1f7be193e891a8cca7a3a849a</td>
      <td>726</td>
      <td>6</td>
      <td>100</td>
      <td>1584</td>
      <td>79.90</td>
    </tr>
    <tr>
      <th>6217</th>
      <td>fashion_bags_accessories</td>
      <td>abd7f7a1bd327a8f1f363abfc0f87391</td>
      <td>367</td>
      <td>1</td>
      <td>100</td>
      <td>612</td>
      <td>39.99</td>
    </tr>
  </tbody>
</table>
</div>

The CF system identified the top 5 recommended products for the customer with ID `175b8225bdd99e95a64e07a6c06e8746`. The recommendations span diverse categories, including **auto**, **housewares**, **fashion_bags_accessories**, and **bed_bath_table**, showcasing the system's ability to balance relevance and exploration.

Features such as **description length**, **photos quantity**, **weight**, **volume**, and **price** vary across the recommended products, introducing diversity while partially aligning with the customer’s previous preferences in the **bed_bath_table** category. The results demonstrate the effectiveness of the CF model in generating personalized recommendations that combine familiar and novel options.

## **6. Evaluation**

### **6.1. Content-Based Filtering**

#### **6.1.1. Customer Profile Recommendation**

The effectiveness of the CBF system was evaluated by analyzing the alignment of recommended products with the customer’s preferences, using both qualitative insights and quantitative metrics.

```python
# Get a list of products bought by customer '048df8b25dc48e1554eccde119d6cecd.
list_of_products_bought = orders_df[
    orders_df['customer_unique_id'] == random_customer_id
]['product_id'].tolist()

# Reindex TF-IDF dataframe to include only products bought by the customer.
products_bought_df = tfidf_df.reindex(list_of_products_bought)

# Calculate the customer's profile by averaging TF-IDF scores of bought products.
customer_prof = products_bought_df.mean()

# Display the top features in the customer's profile with positive scores.
customer_prof[customer_prof > 0].sort_values(ascending=False).head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>baby</th>
      <td>0.696268</td>
    </tr>
    <tr>
      <th>many_photos</th>
      <td>0.352389</td>
    </tr>
    <tr>
      <th>medium_volume</th>
      <td>0.317158</td>
    </tr>
    <tr>
      <th>long_description</th>
      <td>0.316169</td>
    </tr>
    <tr>
      <th>light_weight</th>
      <td>0.300955</td>
    </tr>
  </tbody>
</table>
</div><br>

A customer profile was generated based on the products they have purchased. The profile represents the average attributes of the items, highlighting the customer's preferences.  

- A subset of the TF-IDF DataFrame (`tfidf_df`) was created, containing only the rows corresponding to the products purchased by the customer.  
- The average scores of the features for the purchased products were calculated, forming the customer's profile.  
- Features with the highest average scores indicate the customer's most significant preferences.  

The customer's profile reveals preferences for the following top features.
- **baby**: Indicates interest in baby-related products.  
- **many_photos**: Suggests a preference for products with more images.  
- **medium_volume**: Highlights a tendency toward medium-sized products.  
- **long_description**: Shows a preference for products with detailed descriptions.  
- **light_weight**: Indicates favorability toward lighter products.  

```python
# Find subset of tfidf_df that does not include products in list_of_products_bought
tfidf_subset_df = tfidf_df.drop(list_of_products_bought, axis=0)

# Calculate the cosine_similarity and wrap it in a DataFrame
similarity_array = cosine_similarity(
    customer_prof.values.reshape(1, -1),
    tfidf_subset_df
)
similarity_df = pd.DataFrame(
    similarity_array.T,
    index=tfidf_subset_df.index,
    columns=["similarity_score"]
)

# Sort the values from high to low by the values in the similarity_score
sorted_similarity_df = similarity_df.sort_values(
    "similarity_score",
    ascending=False
)

# Inspect the most similar to the customer preferences
sorted_similarity_df = sorted_similarity_df.head()
```

<div id="df-44accdcf-8c72-4625-9072-765131f4c0c9" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>similarity_score</th>
    </tr>
    <tr>
      <th>product_id</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>103aed057190376889bc9e796affa24e</th>
      <td>0.974</td>
    </tr>
    <tr>
      <th>c8078bb7927143cdb008e3563230ca61</th>
      <td>0.923</td>
    </tr>
    <tr>
      <th>3b213ba02fcd0799a85c9c3580a3033d</th>
      <td>0.877</td>
    </tr>
    <tr>
      <th>14ad6805c263d8d758d648f46a06570e</th>
      <td>0.877</td>
    </tr>
    <tr>
      <th>cac9e5692471a0700418aa3400b9b2b1</th>
      <td>0.877</td>
    </tr>
  </tbody>
</table>
</div>

The recommendation system generates product suggestions for a customer by identifying items most similar to their profile based on cosine similarity.  

- The `tfidf_df` was filtered to exclude products the customer has already purchased (`list_of_products_bought`). This ensures recommendations include only new products.  
- The customer's profile vector was compared with the remaining products in the `tfidf_subset_df` using cosine similarity, generating a similarity score for each product.  
- The resulting similarity scores were wrapped in `similarity_df` for easier analysis.  
- The products were sorted in descending order by their similarity scores, ranking the most relevant items at the top.  

The top 5 product recommendations for the customer are identified based on their similarity to the customer’s preferences. These recommendations are tailored to the customer's profile, suggesting items most aligned with their past purchasing behavior and interests.

```python
products_df[products_df['product_id'].isin(list_of_products_bought)]
```

<div id="df-45b55e20-5f4c-49b1-82a8-9779b488776f" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_category_name</th>
      <th>product_id</th>
      <th>product_description_lenght</th>
      <th>product_photos_qty</th>
      <th>product_weight_g</th>
      <th>volume_cm3</th>
      <th>price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5273</th>
      <td>baby</td>
      <td>ac946196783ec18b207ead8a00d3f5c4</td>
      <td>740</td>
      <td>3</td>
      <td>167</td>
      <td>4725</td>
      <td>54.9</td>
    </tr>
    <tr>
      <th>5280</th>
      <td>baby</td>
      <td>b865aecbf934fe78d172e5a0f7a73ec0</td>
      <td>1344</td>
      <td>3</td>
      <td>400</td>
      <td>5120</td>
      <td>149.9</td>
    </tr>
  </tbody>
</table>
</div>

```python
products_df[products_df['product_id'].isin(sorted_similarity_df.index)]
```

<div id="df-fbca9e11-d7da-492f-9e1c-b5869753bebd" class="colab-df-container">
    <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_category_name</th>
      <th>product_id</th>
      <th>product_description_lenght</th>
      <th>product_photos_qty</th>
      <th>product_weight_g</th>
      <th>volume_cm3</th>
      <th>price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5240</th>
      <td>baby</td>
      <td>103aed057190376889bc9e796affa24e</td>
      <td>1355</td>
      <td>4</td>
      <td>332</td>
      <td>3840</td>
      <td>149.33</td>
    </tr>
    <tr>
      <th>5259</th>
      <td>baby</td>
      <td>3b213ba02fcd0799a85c9c3580a3033d</td>
      <td>1640</td>
      <td>1</td>
      <td>350</td>
      <td>8000</td>
      <td>54.90</td>
    </tr>
    <tr>
      <th>5260</th>
      <td>baby</td>
      <td>c8078bb7927143cdb008e3563230ca61</td>
      <td>1978</td>
      <td>4</td>
      <td>250</td>
      <td>7865</td>
      <td>19.90</td>
    </tr>
    <tr>
      <th>5269</th>
      <td>baby</td>
      <td>14ad6805c263d8d758d648f46a06570e</td>
      <td>782</td>
      <td>1</td>
      <td>350</td>
      <td>4536</td>
      <td>60.66</td>
    </tr>
    <tr>
      <th>5286</th>
      <td>baby</td>
      <td>cac9e5692471a0700418aa3400b9b2b1</td>
      <td>2440</td>
      <td>1</td>
      <td>375</td>
      <td>8120</td>
      <td>119.93</td>
    </tr>
  </tbody>
</table>
</div>

The recommendation system generates product suggestions for a customer based on their profile. Below are insights into the purchased and recommended products.

**Customer's Purchased Products**

The customer has previously purchased two products in the **baby** category.
- Products with moderate description lengths (**740** and **1344 characters**).
- Consistent photo count (**3 photos** for both products).
- Lightweight items (**167 g** and **400 g**) with moderate volumes.

**Recommended Products**

The system recommended five new products in the **baby** category, which align with the customer’s preferences.
- Products with detailed descriptions (ranging from **782 to 2440 characters**).
- Varied photo counts (**1 to 4 photos**), including higher photo counts than previously purchased items.
- Lightweight items (weights between **250 g and 375 g**) and moderate to large volumes.

The recommended products closely match the customer’s preference for the **baby** category, lightweight products, and moderate-to-detailed descriptions. The recommendation system effectively suggests items that complement the customer’s profile while introducing new options, such as varying photo counts and slightly different volumes.

#### **6.1.2. Quantitative Evaluation Metrics**

1. **Precision@5**

   Measures the proportion of top-5 recommended products that match the customer's preferences.  

   **Calculation**

   Out of the 5 recommendations, all products matched the customer’s preference for the **baby** category.
   $$
   \text{Precision@5} = \frac{\text{Relevant Recommendations}}{\text{Total Recommendations}} = \frac{5}{5} = 1.0 \, (100\%)
   $$  

2. **Diversity Score**

   Assesses variation in product features among recommendations.  
   - The recommendations showed variation in **description lengths** (782–2440 characters), **photo counts** (1–4 photos), and **volume** (4536–8120 cm³).  
   - The diversity in features ensures the recommendations cater to the customer’s general preferences while introducing variety.  

3. **Hit Rate**

   Evaluates whether at least one recommended product matches the customer’s true preferences.  
   - All 5 recommendations matched the customer’s interest in the **baby** category, yielding a **Hit Rate = 1.0 (100%)**.  

4. **Mean Reciprocal Rank (MRR)**

   Determines the rank of the first relevant product in the recommendation list. Since all recommendations are relevant.
   $$
   \text{MRR} = \frac{1}{\text{Rank of First Relevant Recommendation}} = 1.0
   $$  

### **6.2. Collaborative Filtering**

#### **6.2.1. Root Mean Squared Error**

Root Mean Squared Error (RMSE) is a standard metric for evaluating the accuracy of predicted values in regression-based systems, including collaborative filtering using SVD. RMSE provides a measure of the average difference between predicted ratings (from the reconstructed user-item matrix) and actual ratings, making it an essential evaluation method for recommendation systems.

**Mathematical Formula**

$$
RMSE = \sqrt{\frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2}
$$
Where:
- $y_i$: Actual user rating for an item.
- $\hat{y}_i$: Predicted rating for the same item (from the reconstructed matrix).
- $n$: Total number of predicted ratings being evaluated.

RMSE outputs a single value that quantifies the prediction error, expressed in the same unit as the ratings (e.g., on a scale of 1 to 5).

```python
# Extract prediction values for the first 50% of rows and columns.
predictions_value = predictions_df.iloc[:rows, :cols].values

# The first 20 prediction values that match the holdout mask.
[5. 4. 5. 3. 5. 2. 1. 1. 1. 5. 1. 1. 5. 3. 4. 4. 5. 4. 5. 5.]

# The first 20 actual values from the holdout set that match the holdout mask.
[5. 4. 5. 3. 5. 2. 1. 1. 1. 5. 1. 1. 5. 3. 3. 5. 4. 4. 5. 5.]
```

The comparison between predicted and actual review scores highlights the collaborative filtering model’s accuracy in approximating user preferences. Predicted ratings from the reconstructed interaction matrix closely align with actual ratings from the holdout set, with minimal deviations observed. The analysis demonstrates strong performance and forms the basis for calculating evaluation metrics such as RMSE, which quantifies the model's prediction error.

```python
# Calculate the RMSE between actual values and predictions in the holdout set.
rmse = mean_squared_error(
    holdout_set_value[holdout_mask],
    predictions_value[holdout_mask],
    squared=False
).round(2)
```

```python
RMSE of SVD Predictions: 0.86
```

The performance of the collaborative filtering model using SVD was evaluated by calculating the Root Mean Squared Error (RMSE) between predicted and actual review scores. The RMSE value of **0.86** indicates the average deviation of the predicted ratings from the actual ratings. This low RMSE demonstrates that the model effectively captures user preferences and provides accurate predictions, confirming its reliability for generating recommendations.

![RMSE](images/image-6.png)

The RMSE analysis across review scores reveals that the collaborative filtering model performs best for higher ratings. The RMSE for **review score 4** is the lowest at **0.70**, followed by **0.82** for score 5. Prediction errors are higher for lower scores, with RMSE reaching **1.07** for score 1. This indicates the model predicts higher ratings more accurately while struggling with lower ratings.

### **6.3. Insight**

Recommendation system successfully developed using **content-based filtering** and **collaborative filtering** approaches, delivering personalized product suggestions to improve customer satisfaction and merchant success on the Olist platform.  

1. **Content-Based Filtering Results**:  
   - Products were recommended based on shared attributes such as **category**, **description length**, **number of photos**, **weight**, **volume**, and **price**.  
   - The system demonstrated strong relevance by recommending products closely aligned with the user’s purchasing history while maintaining diversity in features to enhance product discovery.  

2. **Collaborative Filtering Results**:  
   - Collaborative filtering using **Singular Value Decomposition (SVD)** predicted user review scores for products, enabling recommendations based on latent patterns in user behavior.  
   - The model achieved an overall **RMSE of 0.86**, indicating accurate prediction performance. Analysis across review scores revealed the model performed best for higher ratings, with the lowest RMSE of **0.70** for review score 4.  

The system effectively balances relevance and diversity, ensuring that users receive accurate, tailored recommendations while discovering new products. Improved recommendation accuracy supports Olist’s goals of increasing customer engagement, boosting sales, and empowering small businesses across its platform.

## **7. References**

[1] Stalidis, G., Karaveli, I., Diamantaras, K., Delianidi, M., Christantonis, K., Tektonidis, D., Katsalis, A., & Salampasis, M. (2023). Recommendation Systems for e-Shopping: Review of Techniques for Retail and Sustainable Marketing. Sustainability, 15(23), 16151. https://doi.org/10.3390/su152316151

[2] Abdul Hussien, F. T., Rahma, A. M. S., & Abdulwahab, H. B. (2021). An E-Commerce Recommendation System Based on Dynamic Analysis of Customer Behavior. Sustainability, 13(19), 10786. https://doi.org/10.3390/su131910786

[3] kaggle. 2018. https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

[4] datacamp. 20204. https://app.datacamp.com/learn/courses/building-recommendation-engines-in-python
