# Lab 1 - Customer Churn (Marketing)



## Agenda

1. [Overview](#overview)
2. [Upload the dataset to S3 Bucket](#upload-the-dataset-to-s3-bucket)
3. [Import the dataset in Canvas](#import-the-dataset-in-canvas)
4. [Building and Training a ML model](#building-and-training-a-ml-model)
5. [Using the model to generate predictions](#using-the-model-to-generate-predictions)

 

## Overview

In this lab, you will assume the role of a business analyst working for a bank. Your marketing department has asked you to assist in helping identify target customers for an upcoming marketing campaign focused on enrolling customers for new certificate of deposits (CDs). They have provided you with a dataset that contains customer demographic and bank history. Since resources and budget are limited for the marketing campaign, they have asked you to use the data to predict which customers are most likely to enroll in a new CD. The data schema is as follows:

| Column Name    | Data type       |
| -------------- | --------------- |
| age            | INT             |
| job            | STRING          |
| marital        | STRING          |
| education      | STRING          |
| default        | STRING (yes/no) |
| housing        | STRING (yes/no) |
| loan           | STRING (yes/no) |
| contact        | STRING          |
| month          | STRING          |
| day_of_week    | STRING          |
| duration       | INT             |
| campaign       | INT             |
| pdays          | DECIMAL         |
| previous       | INT             |
| poutcome       | STRING          |
| emp.var.rate   | DECIMAL         |
| cons.price.idx | DECIMAL         |
| cons.conf.idx  | DECIMAL         |
| euribor3m      | DECIMAL         |
| nr.employed    | INT             |
| y (target)     | STRING (yes/no) |



## Upload the dataset to S3 Bucket

The first step is to download the dataset that we will use. You can download it [here](./Data/bank-marketing.csv).

Go to the AWS Management Console, search **S3** in the searchbox on top of your console, then go to **S3** service console.

![](./Images/search_s3.png)

![](./Images/studio-bucket.png)

> The **sagemaker-studio-\*** bucket was created automatically when you created the SageMaker Studio domain in the **Prerequisites** section. If you follow the **Event Engine** track, the bucket was pre-provisioned by you instructor.

Click **Upload**.

![](./Images/s3_upload.png)

On the Upload page, drag and drop the `bank-marketing.csv` file you've just downloaded, then click **Upload** at the bottom of the page. Once the upload is complete, you can now click the top-right **Close** button. You should now see the file uploaded in your bucket.

![](./Images/s3-uploaded-banking.png)



## Import the dataset in Canvas

Go back to the SageMaker Canvas tab created in the **Prerequisites** section. On the left menu, you can click the second icon to head to the Datasets section, then click the **Import** button.

![](./Images/import-data.png)

Now, select the bucket where we've previously uploaded our dataset, the **sagemaker-studio-\*** bucket.

![](./Images/import-from-s3-studio.png)

You can now select the `bank-marketing.csv` file uploaded previously by selecting the checkbox at its left. Two new buttons will pop-up at the bottom of your page: **Preview all** and **Import Data**. Let's choose the first one.

![](./Images/canvas-select-preview.png)

You now see a 100-rows preview of the dataset you're looking to import. Once you're done checking that it's indeed the right one, you can click on **Import Data**.



## Building and Training a ML model

Now, let's head back to the **Models** section of the web page, by clicking the second button on the left menu.

![](./Images/canvas-models.png)

Click on **+ New model**, and provide a name to your model.

![](./Images/new-marketing-model.png)

If this is the first time creating a Canvas model, you will be welcomed by an informative pop-up about how to build your first model in 4 simple steps. You can read this through, then come back to this guide.

![](./Images/canvas-first-model-popup.png)

In the Model view, you will see four tabs, which correspond to the four steps to create a model and use it to generate predictions: **Select**, **Build**, **Analyze**, **Predict**. In the first tab, **Select**, click the radio button to select the `bank-marketing.csv` dataset we've uploaded previously. This dataset includes 21 columns and 41K rows. Click the bottom button **Select dataset**.

![](./Images/banking-build.png)

Canvas will automatically move to the **Build** phase. In this tab, choose the target column, in our case `y`. Your marketing team has informed you that this column indicates if the existing customer has or does not have an existing Certificate of Deposit with the bank. This is what you want to train your model to predict. Canvas will automatically detect that this is a **2 Category** problem (also known as binary classification). If the wrong model type is detected, you can change it manually with the **Change type** link at the center of the screen.

![](./Images/banking-build2.png)

In the bottom half of the screen, you can take a look at some of the statistics of the dataset, including missing and mismatched values, unique vales, mean and median values. This can also be used to drop some of the columns, if we don't want to use them for the prediction, by simply un-checking them with the left checkbox.

The dataset includes customer data, previous marketing campaign and contact data, and economic indicators. A description of each column is provided below.

### Customer Data

| Customer Data | Description                                  |
| ------------- | -------------------------------------------- |
| age           | customer age                                 |
| job           | Type of job summarized into 12 categories    |
| marital       | marital status                               |
| education     | education level summarized into 8 categories |
| default       | has customer defaulted on credit (yes/no)    |
| housing       | does customer have a mortgage loan (yes/no)  |
| loan          | does customer have a personal loan (yes/no)  |

### Customer data related to last marketing campaign

| Campaign History | Description                                                  |
| ---------------- | ------------------------------------------------------------ |
| previous         | how many times customer was contacted prior to last marketing campaign |
| campaign         | how many times customer was contacted in last marketing campaign |
| pdays            | number of days since customer was last contacted in previous marketing campaign |
| poutcome         | outcome of previous marketing campaign                       |

### Customer data related to last contact

| Contact History | Description                                      |
| --------------- | ------------------------------------------------ |
| contact         | how was customer last contacted (telephone/cell) |
| month           | last contact month                               |
| day_of_week     | last contact day of the week                     |
| duration        | last contact duration (seconds)                  |

### MacroEconomic Indicators

| Contact History | Description               |
| --------------- | ------------------------- |
| cons.price.idx  | consumer price index      |
| cons.conf.idx   | consumer confidence index |
| euribor3m       | euribor 3 month rate      |
| emp.var.rate    | employment variation rate |
| nr.employed     | employment indicator      |

For this Lab, we're planning on using all of the available features. You can come back to this step later and try to change some of the features to see the impact on the model training.

Once you've explored this section, it's time to finally train the model! Before building a complete model, it is a good practice to have a general idea about the performances that our model will have by training a **Quick Model**. A quick model trains fewer combinations of models and hyper-parameters in order to prioritize speed over accuracy, especially in cases like ours where we want to prove the value of training an ML model for our use case. Note that quick build is not available for models bigger than 50k rows. Let's go ahead and click **Quick build**.

![](./Images/canvas-quick-vs-standard.png)

Now, we wait anywhere from 2 to 15 minutes. Since the dataset is small, this will take probably even less than 2 minutes. Once done, Canvas will automatically move to the **Analyze** tab, to show us the results of our quick training. The analysis performed using quick build estimates that our model is able to predict the right outcome 91.5% of the time.

Let's focus on the first tab, **Overview**. This is the tab that shows us the **Column impact**, or the estimated importance of each column in predicting the target column. In this example, the duration column has the most significant impact in predicting if a customer will open a new CD with the bank.

![](./Images/banking-analyze.png)

> Don't worry if the numbers in the below images differ from yours. Machine Learning introduces some stochasticity in the process of training models, which can lead to different results to different builds.

When we move to the **Scoring** portion of our analysis, we can see a plot representing the distribution of our predicted values with respect to the actual values. Notice that most customers are unlikely to enroll in a new certificate of deposit. If you want to learn more about how Canvas uses SHAP baselines to bring explainability to Machine Learning, you can check the ["Evaluating Your Model's Performance in Amazon SageMaker Canvas" section ](https://docs.aws.amazon.com/sagemaker/latest/dg/canvas-evaluate-model.html) of the Canvas documentation, as well as the page for [SHAP Baselines for Explainability ](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-feature-attribute-shap-baselines.html).

![](./Images/banking-quick-scoring.png)

You are most interested in the customers who are expected to enroll and how well the model predicts this subset of customers. To take a closer look at the results, you can click on the **Advanced metrics** link on the right.

This will display a matrix that allows you to more closely examine the results. In machine learning, this is referred to as a confusion matrix.

For positive class on the top left, click yes since for your use case attaining a new customer is a positive result.

In machine learning, the accuracy of the model is defined as the number of correct predictions divided over the total number of predictions. The blue boxes represent correct predictions that the model made against a subset of test data where there was a known outcome. In this case there were 7537 correct predictions out of 8238 overall, which resulted in 91% accuracy.

However, you are more interested in measuring how well the model predicted new customers. The model correctly predicted 502 customers would enroll in a CD (True Positive - TP). However, it incorrectly predicted that 426 customers would not enroll in a CD, when in fact they would (False negatives - FN). In machine learning, a ratio used to measure this is TP / (TP + FN). This is referred to Recall. The advanced metrics page calculates and displays a recall score of 54% for this model.

![](./Images/banking-advanced-metrics.png)

Now, you have two options:

1. you can use this model to run some predictions, by clicking on the button **Predict** at the bottom of the page;
2. you can create a new Version of this model to train with the **Standard Build** process. This will take much longer - about 4-6 hours - but will be much more accurate.

For the sake of this lab, we will go forward with Option 1.

> Note that training a model with **Standard Build** is necessary to share the model with a Data Scientist with the SageMaker Studio integration. **Predictions** do not require the full build, however they can lack in performances with respect to a fully-trained model.



## Using the model to generate predictions

Now that the model is trained, let's use for some predictions. Select **Predict** at the bottom of the **Analyze** page, or choose the **Predict** tab.

![](./Images/banking-batch-predict-1.png)

Now, choose **Select dataset**, and choose the `bank-marketing.csv`. Next, choose **Generate predictions** at bottom of the page. Canvas will use this dataset to generate our predictions. Although it is generally a good idea not to use the same dataset for both training and testing, we're using the same dataset for the sake of simplicity. After a few seconds, the prediction is done. You can click on the eye icon to see a preview of the predictions by hovering over the, or click the download button to download a CSV file containing the full output. SageMaker Canvas will return a prediction for each row of data and the probability of the prediction being correct.

![](./Images/banking-batch-predict-2.png)

![](./Images/banking-batch-predict-3.png)

You can also choose to predict one by one values, by selecting **Single prediction** instead of batch prediction. Canvas will show you a view where you can provide manually the values for each feature, and generate a prediction. This is ideal for situations like **what-if scenarios**: e.g. how does the price change if the house is older? What if it's closer to the ocean? How about one with more rooms?

![](./Images/banking-single-predict.png)

**Congratulations!** You've now completed lab 1. As next steps you can:

1. run this lab again, but building a Standard Model to see its performances;
2. choose another lab to run

