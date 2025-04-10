# # Azure AutoML Clustering Project- In ML Workspace - Designer
* datascience-dp-100
* ml-batch
* autoML
* mlflow-experiment
* Car-Project
* ai-ml-Microsoft
* ai-api-MLOPS-Duke
* data-e-203
* Assignment
  ## Cheatsheets check
  * Drive/github
  
![image](https://github.com/user-attachments/assets/b8c07f3d-d36b-456d-acc2-3fcc294a2b16)
![image](https://github.com/user-attachments/assets/5a4660ee-2987-4ea0-b444-8ee5f5d4d017)

# Azure AutoML Clustering Project- In ML Workspace - Designer
### Designer - Drag and drop interface from preparing and deploying models
## Deploying to Azure Container Instance (ACI) for Dev/Test and AKS for Production.
# Step-by-Step Process for Creating and Deploying a Clustering Predictive Service in Azure Machine Learning

<details>
<summary><b>Phase 1: Setting up the Azure Machine Learning Environment</b></summary>
<br>

1.  **Create a Resource Group:**
    ```markdown
    - In the Azure portal, navigate to "Resource groups."
    - Click "+ Create."
    - Select your subscription.
    - Enter a unique name for your resource group (e.g., `ml-clustering-rg`).
    - Choose a region that supports Azure Machine Learning services (e.g., East US 2, West Europe).
    - Click "Review + create" and then "Create."
    ```

2.  **Create an Azure Machine Learning Workspace:**
    ```markdown
    - In the Azure portal, navigate to "Azure Machine Learning."
    - Click "+ Create."
    - Select your subscription and the resource group you created in the previous step.
    - Enter a unique name for your Azure Machine Learning workspace (e.g., `ml-clustering-ws`).
    - Choose the same region as your resource group.
    - Select a storage account, container registry, and Azure Key Vault (you can let Azure create new ones or select existing ones).
    - Click "Review + create" and then "Create."
    ```

3.  **Create Compute Targets:**
    ```markdown
    - Open your Azure Machine Learning workspace in the Azure portal.
    - Navigate to the "Compute" section (under "Manage").
    - Click "+ New" under "Compute instances" (optional, for interactive development).
        - Select a virtual machine size.
        - Enter a name for your compute instance.
        - Click "Create."
    - Click "+ New" under "Compute clusters" (for scalable training).
        - Enter a name for your compute cluster.
        - Select a virtual machine size and tier.
        - Configure the scale settings (minimum and maximum nodes, idle time before scale down).
        - Click "Create."
    - **For Inference Testing (ACI):** You don't need to explicitly create an ACI compute target beforehand. The Designer will handle this during deployment.
    ```

</details>

<details>
<summary><b>Phase 2: Building and Training the Clustering Model in Azure Machine Learning Designer</b></summary>
<br>

4.  **Create a Dataset:**
    ```markdown
    - In your Azure Machine Learning workspace, navigate to the "Data" section (under "Assets").
    - Click "+ Create."
    - Choose the data source type (e.g., "From local file," "From Azure Blob storage," "**web files- like Github**".).
    - Follow the prompts to select your data file(s) and configure the dataset (e.g., data type, headers).
    - Click "Create."
    - In Explore tab - preview and profile
    - in Consume tab - import to juypter nb - https://www.coursera.org/learn/mlops-aws-azure-duke/lecture/6oqw4/exploring-open-datasets-sdk
    ```

5.  **Create a Pipeline in Azure Machine Learning Designer:**
    ```markdown
    - Navigate to the "Designer" section in your Azure Machine Learning workspace.
    - Click "+ New."
    - Give your pipeline a name (e.g., "clustering-training-pipeline").
    
    ```

6.  **Apply Data Transformations to Cluster Observations:**
    ```markdown
    In the Designer canvas, drag and drop your created dataset, right-click, and visualize output to see a distribution of columns by histograms.
    - Use the search bar to find and add data transformation modules (under "Data Transformation"). Common transformations for clustering might include:
        - "Select Columns in Dataset" to choose relevant features like edit columns **by name**/by rules.
        - "Clean Missing Data" to handle missing values.
        - "Normalize Data" to scale numerical features - edit columns and select the transformation method - **MinMax**, Z-score, Logistic, Tanh, LogNormal.
      - Right click on Normalize Data and visualize before and after experiment
    - Connect the output ports of the dataset to the input ports of the transformation modules, and chain the transformations as needed.
    ```

7.  **Add Training Modules and Apply a Clustering Algorithm:**
    ```markdown
    - Search for and add a clustering algorithm module (under "Machine Learning Algorithms" -> "Clustering"). A common choice is "K-Means Clustering."
    - Connect the output of your last data transformation module to the "Dataset" input of the clustering algorithm.
    - Configure the parameters of the clustering algorithm (e.g., "Number of centroids").
    ```

8.  **Run the Training Pipeline as new Experiment:**
    ```markdown
    - Click "Submit" at the top of the Designer canvas.
    - Choose an experiment name (e.g., "clustering-experiment").
    - Select a compute target you created in Step 3 (Compute cluster is recommended for training).
    - Review the pipeline settings and click "Submit."
    - Monitor the pipeline run in the "Pipelines" section.
    ```

9.  **Evaluate the Clustering Model:**
    ```markdown
    - Once the training pipeline run is complete, navigate to the output of the "K-Means Clustering" module. You might find metrics or visualizations related to the clusters.
    - To explicitly evaluate the model, you can add an "Evaluate Model" module (under "Model Evaluation") to your training pipeline (if applicable for your chosen clustering algorithm and evaluation metrics). Connect the "Trained model" output of the clustering module and the original dataset (with true labels if available for evaluation) to the "Evaluate Model" module.
    ```

</details>

<details>
<summary><b>Phase 3: Creating and Publishing the Predictive Service</b></summary>
<br>

10. **Create an Inference Pipeline:**
    ```markdown
    - Once your training pipeline is complete and you have a trained model, you can create an inference pipeline. There are a few ways to do this in the Designer:
        - **From the Training Pipeline:** Open the completed training pipeline, click "Create inference pipeline" at the top. This will often create a draft inference pipeline with the necessary input and output components.
        - **Create a New Pipeline:** Start a new pipeline in the Designer. Add your trained model (found under "Assets" -> "Models"). Add necessary input components (e.g., "Web Service Input"), any required data transformations (matching those in the training pipeline), the "Score Model" module (connecting the model and input data), and an output component (e.g., "Web Service Output"). For clustering, the "Score Model" module will assign new data points to the learned clusters.
    ```

11. **Deploy the Predictive Service to Azure Container Instance (ACI) for Testing:**
    ```markdown
    - Open your inference pipeline in the Designer.
    - Click "Deploy" at the top.
    - Give your endpoint a name (e.g., "clustering-aci-endpoint").
    - **Select "Azure Container Instance" as the compute target.**
    - Confirm the deployment settings and click "Deploy."
    - Wait for the deployment to complete. You can monitor the status in the "Endpoints" section.
    ```

12. **Test the Deployed Web Service (ACI) using a Notebook:**
    ```markdown
    - **Create a Notebook:** In your Azure Machine Learning workspace, navigate to the "Notebooks" section. Create a new notebook (you can choose a Python kernel).
    - **Get Endpoint and Key:**
        - Navigate to the "Endpoints" section in your Azure Machine Learning workspace.
        - Select your deployed ACI endpoint (e.g., "clustering-aci-endpoint").
        - Go to the "Consume" tab.
        - You will find the **"REST endpoint" URL**. Copy this URL.
        - You will also find the **"Primary key"** or **"Authorization header"**. Copy this key.
    - **Authenticate and Call the Service in the Notebook:**
        ```python
        import requests
        import json

        scoring_uri = "<YOUR_REST_ENDPOINT_URL>"
        api_key = "<YOUR_PRIMARY_KEY>"
        headers = {'Content-Type': 'application/json', 'Authorization': f'Bearer {api_key}'} # For key-based authentication
        # headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': api_key} # Alternative header if required

        # Sample input data (adjust based on your pipeline input)
        input_data = {
            "Inputs": {
                "data": [
                    {"feature1": 2.5, "feature2": 3.1},
                    {"feature1": 1.8, "feature2": 2.9}
                    # Add more data points as needed
                ]
            },
            "GlobalParameters": {}
        }

        input_json = json.dumps(input_data)

        try:
            response = requests.post(scoring_uri, data=input_json, headers=headers)
            response.raise_for_status()  # Raise an exception for bad status codes
            predictions = response.json()
            print("Cluster Predictions:")
            print(predictions)
            # You might find the cluster assignment in a specific part of the JSON response
            # For example, it might be under a "Results" key.
            if "Results" in predictions:
                for result in predictions["Results"]:
                    print(f"Predicted Cluster: {result}")
        except requests.exceptions.RequestException as e:
            print(f"Error calling endpoint: {e}")
            if response is not None:
                print(f"Response status code: {response.status_code}")
                print(f"Response text: {response.text}")
        ```
        ```markdown
        - **Run the Script:** Execute the notebook cells. You should see the predicted cluster assignments for your input data. In your example, you mentioned "cluster 1 is predicted," so you would look for that in the output.
        ```
    ```

12. **Publish the Predictive Service for Application Use (ACI Endpoint):**
    ```markdown
    - The ACI endpoint you deployed in Step 11 is already a published predictive service that client applications can use. You would share the endpoint URL and authentication key with the application developers.
    ```

</details>

**Important Considerations:**

```markdown
- **Input Data Schema:** Ensure that the input data you send to the endpoint in your notebook (and from client applications) matches the expected schema of your inference pipeline.
- **Authentication:** Securely manage and share the authentication key for your endpoint.
- **ACI Limitations:** Remember that ACI is best suited for development and testing or low-traffic production scenarios. For high-scale, production deployments with strict latency and security requirements, consider deploying to Azure Kubernetes Service (AKS) as you initially mentioned. The process for deploying to AKS from the Designer is similar to deploying to ACI, but you would select an AKS cluster as your compute target.

----------------------------------------------------------------------------------------------------------------------------------




![image](https://github.com/user-attachments/assets/b8c07f3d-d36b-456d-acc2-3fcc294a2b16)


