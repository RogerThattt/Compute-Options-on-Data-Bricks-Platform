# Compute-Options-on-Data-Bricks-Platform
Compute Options on Data Bricks Platform

1️⃣ Interactive Clusters (Notebook Execution)
Used for exploratory analysis and ad-hoc queries.

python
Copy
Edit
# Interactive analysis in a Databricks notebook
df = spark.read.format("delta").load("/mnt/delta/sample_data")
df.createOrReplaceTempView("sample_data")

# Run an SQL query interactively
result = spark.sql("SELECT category, COUNT(*) FROM sample_data GROUP BY category")
display(result)
2️⃣ Job Clusters (Scheduled ETL Job)
Job clusters are created for each execution and then terminated.

Databricks Job JSON Definition (via API)
json
Copy
Edit
{
  "name": "Daily ETL Job",
  "new_cluster": {
    "spark_version": "13.0.x-scala2.12",
    "num_workers": 4,
    "node_type_id": "i3.xlarge"
  },
  "notebook_task": {
    "notebook_path": "/Users/etl_notebook"
  }
}
Run the Job using Databricks API
bash
Copy
Edit
curl -X POST https://<databricks-instance>/api/2.0/jobs/runs/submit \
  -H "Authorization: Bearer <token>" \
  -d @job_payload.json
3️⃣ Serverless SQL Warehouses
Best for BI dashboards and running SQL queries without managing clusters.

sql
Copy
Edit
-- Running a SQL query on Serverless SQL Warehouse
SELECT product_id, SUM(sales) as total_sales
FROM sales_data
WHERE region = 'US'
GROUP BY product_id
ORDER BY total_sales DESC;
4️⃣ Photon Engine (High-Performance SQL Processing)
Photon engine can be enabled for high-speed queries on Delta Lake.

sql
Copy
Edit
-- Running an optimized SQL query using Photon Engine
SELECT * FROM sales_data WHERE revenue > 1000000;
To enable Photon on a cluster:

Go to Clusters > Configuration and check Enable Photon.

5️⃣ GPU Clusters (Deep Learning)
Best for machine learning training and large AI models.

python
Copy
Edit
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import VectorAssembler

# Preparing the dataset
vector_assembler = VectorAssembler(inputCols=["feature1", "feature2"], outputCol="features")
train_data = vector_assembler.transform(df).select("features", "label")

# Using a GPU-enabled cluster for training
lr = LogisticRegression(maxIter=10, regParam=0.3, elasticNetParam=0.8)
model = lr.fit(train_data)

# Predict
predictions = model.transform(train_data)
display(predictions)
To enable GPU clusters:

Use g4dn.xlarge, p3.2xlarge, or similar GPU instance types.

6️⃣ Spot & Reserved Instances (Cost Optimization)
Using spot instances reduces costs by leveraging spare capacity.

Enable Spot Instances in Cluster Configuration
json
Copy
Edit
{
  "cluster_name": "Spot Cluster",
  "spark_version": "13.0.x-scala2.12",
  "num_workers": 5,
  "node_type_id": "i3.xlarge",
  "aws_attributes": {
    "availability": "SPOT_WITH_FALLBACK"
  }
}
For Reserved Instances, contact AWS/Azure/GCP to reserve resources in advance.

7️⃣ Unity Catalog Compute (Secure Access)
Unity Catalog enforces governance policies across Databricks workspaces.

sql
Copy
Edit
-- Grant access to a user in Unity Catalog
GRANT SELECT ON TABLE main.sales_data TO 'user@example.com';

-- Restrict access to sensitive data
CREATE MASKING POLICY mask_ssn AS (val STRING) RETURNS STRING ->
    CASE WHEN is_account_admin() THEN val ELSE 'XXX-XX-XXXX' END;

ALTER TABLE customer_data
MODIFY COLUMN ssn SET MASKING POLICY mask_ssn;
Unity Catalog is enabled through Admin Console > Unity Catalog.
