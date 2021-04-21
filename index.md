## Introduction

Classification is a supervised machine learning technique used to predict categories or classes. We will introduce how to create classification models using Azure Machine Learning designer.

In this article we also should create the resource, the compute instances and targets. I have the articles that are very specifically talking about how to step by step create resources and machine learning instances. The respositories names are: AzureML-Startup and AzureML-AutoMLStartup, welcome to review them.

## 1. Setups to ML assignments

In Azure we can perform all of machine learning assignments. Before implementing the assignments, we should build up the platforms, which means that we need to create an Azure Machine Learnig workspace, create compute resources and compute targets. 

There are several configurations that should be set in advance.

To create an Azure machine learning workspace:

We need to sign into the Azure portal and create a resource, please configure for the following information:

![image](https://user-images.githubusercontent.com/71245576/115499085-c6ae9600-a23c-11eb-8d05-6bd4d886f925.png)

It could take a few minutes.

Then we need to create compute resources. To train and deploy models using Azure Machine Learning designer, you need compute on which to run the training process, and to test the trained model after deploying it.

There are several compute resource that you can create:

![image](https://user-images.githubusercontent.com/71245576/115499174-f8bff800-a23c-11eb-9553-8b97d1a0b458.png)

On the Compute Instances tab, add a new compute instance with the following settings, and you will use this as the workstation from which to test the models.

![image](https://user-images.githubusercontent.com/71245576/115499250-1beaa780-a23d-11eb-9307-3f3fed64764b.png)

While the compute instance is being created, switch to the Compute Clusters tab, and add a new compute cluster with the following settings. You'll use this to train a machine learning model:

![image](https://user-images.githubusercontent.com/71245576/115499291-33c22b80-a23d-11eb-8c52-1472bf1b7d73.png)

## 2. Loading the data

In Azure Machine Learning studio, view the Datasets page and create a dataset from web files. The data is from https://aka.ms/diabetes-data.

Fill basic info:

![image](https://user-images.githubusercontent.com/71245576/115627553-39ffe880-a2cd-11eb-9d81-a285898c412c.png)

Settings and preview:

![image](https://user-images.githubusercontent.com/71245576/115627875-b692c700-a2cd-11eb-9fb0-e16871d43568.png)

Scheme settings: include all other than Path.

![image](https://user-images.githubusercontent.com/71245576/115627929-cd391e00-a2cd-11eb-9e42-d6b648d3a3f0.png)

Confirm and create it.

## 3. transforming data

View the Designer page and select to create a new pipeline. Change its name to Diabetes Training.

Drag the diabetes data to the canvas:

![image](https://user-images.githubusercontent.com/71245576/115628299-7253f680-a2ce-11eb-95d1-884e5165243c.png)

You also can visualize the data output on the right pane of settings, noting that diabetic column is the label we need to predict, it contains two class, with a value of 0 meaning that the patient does not have diabetes, and a value of 1 meaning that the patient is diabetic.

![image](https://user-images.githubusercontent.com/71245576/115628742-150c7500-a2cf-11eb-95dc-88d163a65103.png)

You can check on other features. For example Age values range from 21 to 77 while DiabetesPedigree values range from 0.078 to 2.3016. There are several features that are not the same scale so let's normalize them:


Drag a Normalize Data module to the canvas and select columns by name:

![image](https://user-images.githubusercontent.com/71245576/115629014-8a784580-a2cf-11eb-9e60-d190ed66fb34.png)

Your canvas now could be like this:

![image](https://user-images.githubusercontent.com/71245576/115629087-a67be700-a2cf-11eb-8641-408ba6ca6e85.png)

Now try to submit to run it, afterit has completed, view the transformed data in its settings pane, visualize it:

![image](https://user-images.githubusercontent.com/71245576/115629753-f7400f80-a2d0-11eb-8f7e-e259683ef3e7.png)

## 4. Machine learning pipelines

Open the Diabetes Training pipeline you created in the previous unit if it's not already open.

Drag a Split Data module onto the canvas under the Normarlize Data module.Then connect the Transformed Dataset (left) output of the Normalize Data module to the input of the Split Data module.The splitting mode is Split Rows. Fraction of the rows in the first output dataset is 0.7 and random seed is 123.

Drag a Train Model module to the canvas, under the Split Data module. Then connect the Result dataset1 (left) output of the Split Data module to the Dataset (right) input of the Train Model module. Set the label column to Diabetic.

Because the Diabetic label the model will predict is a class (0 or 1), so we need to train the model using a two-class classification algorithm, drag a Two-Class Logistic Regression module to the canvas, to the left of the Split Data module and above the Train Model module. Then connect its output to the Untrained model (left) input of the Train Model module.

To test the trained model, we need to use it to score the validation dataset we held back when we split the original data. Drag a Score Model module to the canvas, below the Train Model module. Then connect the output of the Train Model module to the Trained model (left) input of the Score Model module; and connect the Results dataset2 (right) output of the Split Data module to the Dataset (right) input of the Score Model module.

Now our pipeline looks like this(I am running this pipeline):

![image](https://user-images.githubusercontent.com/71245576/115630806-aa5d3880-a2d2-11eb-9316-5b216d4fa2ac.png)

Now submit it to run, wait for running. Then you can select the Score Model module and in the settings pane, on the Outputs + Logs tab, under Data outputs in the Scored dataset section, use the Visualize icon to view the results.

![image](https://user-images.githubusercontent.com/71245576/115631680-48053780-a2d4-11eb-8ad2-d37b1e42217c.png)

## 5. Evaluation

We can calculate various metrics that describe how well the model performs. Now let's add Evaluate Model module:

In the pane on the left, in the Model Scoring & Evaluation section, drag an Evaluate Model module to the canvas, under the Score Model module, and connect the output of the Score Model module to the Scored dataset (left) input of the Evaluate Model module.

Our pipeline is now like this:

![image](https://user-images.githubusercontent.com/71245576/115631738-60755200-a2d4-11eb-9552-c3b5f3e07388.png)

We select Submit and wait for running.

When the experiment run has finished, select the Evaluate Model module and in the settings pane, on the Outputs + Logs tab, under Data outputs in the Evaluation results section, use the Visualize icon to view the performance metrics.

![image](https://user-images.githubusercontent.com/71245576/115632055-f7420e80-a2d4-11eb-80aa-dc7ee73ae76b.png)

View the confusion matrix for the model, which is a tabulation of the predicted and actual value counts for each possible class. 

![image](https://user-images.githubusercontent.com/71245576/115632186-2fe1e800-a2d5-11eb-93e6-5d2c6922e3a5.png)

## 6. Inference pipeline

In the Create inference pipeline drop-down list, click Real-time inference pipeline. After a few seconds, a new version of your pipeline named Diabetes Training-real time inference will be opened.

Rename the inference pipeline to Predict Diabetes, now step by step re-structure the pipeline:

a. Replace the diabetes-data dataset with an Enter Data Manually module that does not include the label column (Diabetic).

The rows of data are:

```excel
PatientID,Pregnancies,PlasmaGlucose,DiastolicBloodPressure,TricepsThickness,SerumInsulin,BMI,DiabetesPedigree,Age
1882185,9,104,51,7,24,27.36983156,1.350472047,43
1662484,6,73,61,35,24,18.74367404,1.074147566,75
1228510,4,115,50,29,243,34.69215364,0.741159926,59
```
b. Remove the Evaluate Model module that is not useful.

c. Insert an Execute Python Script module before the web service output to return only the patient ID, predicted label value, and probability.

This script is to select only the PatientID, Scored Labels and Scored Probabilities columns and renames them appropriately

```python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    scored_results = dataframe1[['PatientID', 'Scored Labels', 'Scored Probabilities']]
    scored_results.rename(columns={'Scored Labels':'DiabetesPrediction',
                                'Scored Probabilities':'Probability'},
                        inplace=True)
    return scored_results
```
Connect the output from the Score Model module to the Dataset1 (left-most) input of the Execute Python Script, and connect the output of the Execute Python Script module to the Web Service Output.

Now our inference pipeline looks like this:

![image](https://user-images.githubusercontent.com/71245576/115632905-9d424880-a2d6-11eb-812f-af2bccfbe672.png)

After it has completed, select the Execute Python Script module, and in the settings pane, on the Output + Logs tab, visualize the Result dataset to see the predicted labels and probabilities for the three patient observations in the input data.

![image](https://user-images.githubusercontent.com/71245576/115634981-afbd8180-a2d8-11eb-8821-992edfac770d.png)

## 7. Deploying a predictive service

After you've created and tested an inference pipeline for real-time inferencing, you can publish it as a service for client applications to use.

View the Predict Diabetes inference pipeline you created in the previous unit, select Deploy and deploy a new real-time endpoint, set the name, description and compute type:

![image](https://user-images.githubusercontent.com/71245576/115635031-c532ab80-a2d8-11eb-96de-591c909b3267.png)

It takes a few minutes. After it has completed, check on the primary key and compute servies. 

![image](https://user-images.githubusercontent.com/71245576/115635357-86e9bc00-a2d9-11eb-95f1-83e894207b1a.png)

Now create a new file and open a notebook, paste the code following(do not forget replace the endpoint and primary key information):

```python
endpoint = 'YOUR_ENDPOINT' #Replace with your endpoint
key = 'YOUR_KEY' #Replace with your key

import urllib.request
import json
import os

data = {
    "Inputs": {
        "WebServiceInput0":
        [
            {
                    'PatientID': 1882185,
                    'Pregnancies': 9,
                    'PlasmaGlucose': 104,
                    'DiastolicBloodPressure': 51,
                    'TricepsThickness': 7,
                    'SerumInsulin': 24,
                    'BMI': 27.36983156,
                    'DiabetesPedigree': 1.3504720469999998,
                    'Age': 43,
            },
        ],
    },
    "GlobalParameters":  {
    }
}

body = str.encode(json.dumps(data))


headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ key)}

req = urllib.request.Request(endpoint, body, headers)

try:
    response = urllib.request.urlopen(req)
    result = response.read()
    json_result = json.loads(result)
    output = json_result["Results"]["WebServiceOutput0"][0]
    print('Patient: {}\nPrediction: {}\nProbability: {:.2f}'.format(output["PatientID"],
                                                            output["DiabetesPrediction"],
                                                            output["Probability"]))
except urllib.error.HTTPError as error:
    print("The request failed with status code: " + str(error.code))

    # Print the headers to help debug
    print(error.info())
    print(json.loads(error.read().decode("utf8", 'ignore')))
```

## Reference

Create no-code predictive models with Azure Machine Learning, retrieved from https://docs.microsoft.com/en-us/learn/paths/create-no-code-predictive-models-azure-machine-learning/

