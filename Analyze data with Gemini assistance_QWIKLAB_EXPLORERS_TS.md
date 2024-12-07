# || [Analyze data with Gemini assistance](https://www.cloudskillsboost.google/games/5700/labs/36430) ||

## # Like, comment, share & Don't forget to subscribe [Qwiklab_Explorers_ts](https://youtube.com/@titashshil?si=RgamNu1dc9jVIbJN) 👍😄🤝

---
## ⚠️ **Disclaimer:**
#### This script and guide are provided for educational purposes to help you understand the lab process. Please ensure you understand the steps before using any scripts. Before using the script, I encourage you to open and review it to understand each step.The goal is to help you learn how to complete the labs effectively while following Qwiklabs' terms of service and YouTube's community guidelines.
---

## Copy and run :

```
# Step 1: Set region from project metadata
echo "${CYAN}Fetching default region...${RESET}"
export REGION=$(gcloud compute project-info describe \
--format="value(commonInstanceMetadata.items[google-compute-default-region])")

# Step 2: Get project ID
echo "${CYAN}Fetching project ID...${RESET}"
export PROJECT_ID=$(gcloud config get-value project)

# Step 3: Get user email
echo "${CYAN}Fetching authenticated user email...${RESET}"
export USER=$(gcloud config get-value account 2> /dev/null)

# Step 4: Enable required Google Cloud services
echo "${CYAN}Enabling BigQuery API...${RESET}"
gcloud services enable cloudaicompanion.googleapis.com

# Step 5: Assign IAM roles to the user
echo "${CYAN}Assigning IAM roles to the user...${RESET}"
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member user:${USER} --role=roles/cloudaicompanion.user
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member user:${USER} --role=roles/serviceusage.serviceUsageViewer

# Step 6: Create BigQuery dataset
echo "${CYAN}Creating BigQuery dataset 'bqml_tutorial'...${RESET}"
bq mk bqml_tutorial

# Step 7: Run a query to fetch top 10 products by total orders
echo "${CYAN}Running query to fetch top 10 products by total orders...${RESET}"
bq query --use_legacy_sql=false \
"
SELECT u.id as user_id, u.first_name, u.last_name, avg(oi.sale_price) as avg_sale_price   
FROM \`bigquery-public-data.thelook_ecommerce.users\` as u   
JOIN \`bigquery-public-data.thelook_ecommerce.order_items\` as oi   
ON u.id = oi.user_id   
GROUP BY 1,2,3   
ORDER BY avg_sale_price DESC   
LIMIT 10
"

# Step 8: Query for sales grouped by date and product ID
echo "${CYAN}Running query for sales grouped by date and product ID...${RESET}"
bq query --use_legacy_sql=false \
"
# select the sum of sale_price by Date(created_at) and product_id casted to day from bigquery-public-data.thelook_ecommerce.order_id as t1 joined this with products table in the same dataset as t2
SELECT
  SUM(sale_price),
  DATE(created_at) AS created_at_day,
  CAST(product_id as INT64)
FROM
  \`bigquery-public-data.thelook_ecommerce.order_items\` AS t1
JOIN
  \`bigquery-public-data.thelook_ecommerce.products\` AS t2 ON t1.product_id = t2.id
GROUP BY
  created_at_day,
  product_id
"

# Step 9: Fetch top 10 users by average sale price
echo "${CYAN}Fetching top 10 users by average sale price...${RESET}"
bq query --use_legacy_sql=false '
SELECT 
    u.id AS user_id, 
    u.first_name, 
    u.last_name, 
    AVG(oi.sale_price) AS avg_sale_price
FROM 
    `bigquery-public-data.thelook_ecommerce.users` AS u
JOIN 
    `bigquery-public-data.thelook_ecommerce.order_items` AS oi
ON 
    u.id = oi.user_id
GROUP BY 
    u.id, u.first_name, u.last_name
ORDER BY 
    avg_sale_price DESC
LIMIT 10
'

# Step 10: Fetch total sales by date and product with product name
echo "${CYAN}Fetching total sales by date and product with product name...${RESET}"
bq query --use_legacy_sql=false '
SELECT
    DATE(order_items.created_at) AS order_date,
    order_items.product_id,
    products.name AS product_name,
    ROUND(SUM(order_items.sale_price), 2) AS total_sales
FROM
    `bigquery-public-data.thelook_ecommerce.order_items` AS order_items
LEFT JOIN
    `bigquery-public-data.thelook_ecommerce.products` AS products
ON
    order_items.product_id = products.id
GROUP BY
    order_date,
    order_items.product_id,
    product_name
ORDER BY
    total_sales DESC
'

# Step 11: Create a forecasting model
echo "${CYAN}Creating sales forecasting model...${RESET}"
bq query --use_legacy_sql=false \
"
CREATE MODEL bqml_tutorial.sales_forecasting_model
OPTIONS(MODEL_TYPE='ARIMA_PLUS',
time_series_timestamp_col='date_col',
time_series_data_col='total_sales',
time_series_id_col='product_id') AS
SELECT sum(sale_price) as total_sales,
DATE(created_at) as date_col,
product_id
FROM \`bigquery-public-data.thelook_ecommerce.order_items\`
AS t1
INNER JOIN \`bigquery-public-data.thelook_ecommerce.products\`
AS t2
ON t1.product_id = t2.id
GROUP BY 2, 3;
"

# Step 12: Use the forecasting model
echo "${CYAN}Running ML.FORECAST query to use the forecasting model...${RESET}"
bq query --use_legacy_sql=false "
SELECT
  *
FROM
  ML.FORECAST(MODEL \`$DEVSHELL_PROJECT_ID.bqml_tutorial.sales_forecasting_model\`,
    STRUCT(
      7 AS horizon,
      0.95 AS confidence_level
    )
  )
"

echo

cd

remove_files() {
    # Loop through all files in the current directory
    for file in *; do
        # Check if the file name starts with "gsp", "arc", or "shell"
        if [[ "$file" == gsp* || "$file" == arc* || "$file" == shell* ]]; then
            # Check if it's a regular file (not a directory)
            if [[ -f "$file" ]]; then
                # Remove the file and echo the file name
                rm "$file"
                echo "File removed: $file"
            fi
        fi
    done
}

remove_files
```
---

## Congratulations ..!!🎉  You completed the lab shortly..😃💯

## *Well done..!* 👏

## Thank you for visiting.... :) 🗯️

## [Qwiklab_Explorers_ts](https://youtube.com/@titashshil?si=RgamNu1dc9jVIbJN)

## Join to our community [Digital Dominators](https://chat.whatsapp.com/J0o1beFGCHfJ8ZHGKjcqkd)
