# azure-ml-example
A how-to guide to creating and configuring a ml model and it's endpoints.

In this example we're following the guidelines from Microsoft Learn. The original guidelines can be found [here](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/01-machine-learning.html)
This example creates a regressive model to predict bike rentals using data from a web file found in this [link](https://aka.ms/bike-rentals)

Steps:
- 1 Subscribe to Microsoft Azure and create an account.
- 2 From Azure portal, click on "+ Create a resource" and search for Machine Learning and create a new Azure Machine Learning workspace.
Here you have to select a valid azure subscription and select a resource group or create a new one. From the resource name you enter,
the storage account, key vault and application insights names will be created by default; Leave Container Registry as none.
- 3 After creating, select launch studio or access this [link](https://ml.azure.com). It should lead you to your newly created workspace. If not, click on select all workspaces in the left pannel.
- 4 In the left pannel, click Automated ML
- 5 Create new Automated ML job with:
  - In basic settings select name: mslearn-bike-automl, experiment name: mslearn-bike-rentals, description: Automated machine learning for bike rental prediction
  - In task type & data select task type: Regression and create a new data type:
    - name: bike-rentals, description: Historic bike rental data, Type: Tabular. Select 'From web files' in data source with this [web url](https://aka.ms/bike-rentals)
    - in settings, change column headers to 'Only first file has headers'
    - in Schema, all columns but path are selected by default.
    - Review and create the data type.
  - Select the newly created bike-rentals dataset.
  - In Task Settings:
    - Select task-type: Regression, dataset: bike-rentals, target-column: rentals(Integer)
    - Click on additional configuration settings and unselect everything; In allowed models select RandomForest and LightGBM only.
    - Expand Limits session and select max trials, max concurrent trials, max nodes: 3, Metric score threshold: 0.085, timeout and iteration timeout: 15 and select 'Enable early termination'
    - In validation and test, change the validation type to 'Train-validation split' and set the percentage of validation data: 10. Leave test dataset: None
  - In Compute:
    - Leave select compute type as serverless, leave the rest as default, changing the virtual machine size if needed.
  - Submit the training job. This takes a few minutes.
- 6 After the training job is complete, click on 'Overview' tab, and click the name under 'Algorithm name', at the 'Best model summary' pannel in the right. Make sure residuals and predicted_true charts are included.
- 7 On the model tab select deploy and click use the Web service to open the right pannel; set name: predict-rentals, description: Predict cycle rentals and select enable authentication. Click deploy.
- 8 Test your deployed service. On the left pannel, click Endpoints and open the predict-rentals real-time endpoint; click on Test tab.
  - Replace the template JSON in "Input data to test endpoint" with the following JSON code and click on 'Test':
```JSON
 {
   "Inputs": { 
     "data": [
       {
         "day": 1,
         "mnth": 1,   
         "year": 2022,
         "season": 2,
         "holiday": 0,
         "weekday": 1,
         "workingday": 1,
         "weathersit": 2, 
         "temp": 0.3, 
         "atemp": 0.3,
         "hum": 0.3,
         "windspeed": 0.3 
       }
     ]    
   },   
   "GlobalParameters": 1.0
 }
```
  - Review the test results. It includes a code snippet like this:
```JSON
{
"Results":[
0:float353.0146117637014
]
}
```
- 9 After finishing, delete all created resources, as you are billed for their usage.
