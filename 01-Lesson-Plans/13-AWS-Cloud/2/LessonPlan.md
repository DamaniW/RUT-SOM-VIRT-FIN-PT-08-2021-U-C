## 13.2 Lesson Plan: Deploying ML Models with SageMaker

### Overview

In Today's class, students will be introduced to some Amazon Web Services (AWS) tools and will learn how to leverage some cloud computing services for machine learning using Amazon SageMaker.

### Class Objectives

By the end of class, students will be able to:

* Describe the pros and cons of using cloud services to deploy machine learning models.

* Orchestrate a cloud solution by combining different AWS services.

* Understand how Amazon SageMaker works and how it can be used to deploy machine learning models.

### Instructor Notes

* This lesson introduces new content rapidly. Students may express frustration at learning new cloud technologies. Remind students that while the learning curve may be steep at first, AWS and Cloud experience is highly sought-after and well worth the effort required to become comfortable with it.

* **Important!** Slack out the disclaimer for [AWS Free Tier](../Supplemental/AWS-Free-Tier.md) services before class. Take some time at the beginning of class to explain that while we are only using free tier services in this class, students should review this documentation to avoid accidentally incurring charges.

* Note that in the past, AWS content has appeared differently for some instructional teams. It seems that AWS does A/B testing on their UI. If your AWS views _don't_ match up with the views in the lesson plan, check that you've pulled the latest updates from GitHub or look in the Slack Instructional Team channel for announcements regarding this.

* Check before class that all students have an AWS account and can log in.

* Today's class should be a fun one. Students will put together many different technologies covered so far and learn how they can interact with cloud services.

* There are a few activities that require setup. Have the class follow along and ask questions as you go.

### Sample Class Video (Highly Recommended)
* To watch an example class lecture, go here: [13.2 Class Video.](https://codingbootcamp.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=88dce2d4-e8b1-4a5d-a7fc-abd40178e01f) Note that this video may not reflect the most recent lesson plan.

### Slideshow and Time Tracker

* The slides for this lesson can be viewed on Google Drive here: [Lesson 13.2 Slides](https://docs.google.com/presentation/d/1Y-DPsCtOWFLDa2yCpwK77kqOlvUfrv33NRUxt7on0ic/edit?usp=sharing).

* To add the slides to the student-facing repository, download the slides as a PDF by navigating to File, selecting "Download as," and then choosing "PDF document." Then, add the PDF file to your class repository along with other necessary files. You can view instructions for this [here](https://docs.google.com/document/d/1XM90c4s9XjwZHjdUlwEMcv2iXcO_yRGx5p2iLZ3BGNI/edit?usp=sharing).

* **Note:** Editing access is not available for this document. If you wish to modify the slides, create a copy by navigating to File and selecting "Make a copy...".

* The time tracker for this lesson can be found here: [Time Tracker](TimeTracker.xlsx).

---

### 1. Instructor Do: Welcome Class (5 min)

Welcome class to day 2 of Unit 13, this should be a fun one since students will go more in-depth on using AWS to deploy machine learning models.

Explain to students that we will use the free tier of AWS and the trial period for some of the services. Slack out the following resources to be used as a reference to understand how the free tier of AWS works.

* [AWS Free Tier Supplemental](../Supplemental/AWS-Free-Tier.md)

* [AWS Free Tier Documentation](https://aws.amazon.com/free)

Open the [AWS Free Tier Supplemental](../Supplemental/AWS-Free-Tier.md) and briefly explain to students the limits we will have for the services they will learn in this unit.

| Amazon SageMaker (Two Months Free Trial)                                         | Amazon Lex (12 Months Free)       | AWS Lambda (Always Free)                              | Amazon S3 (12 Months Free) |
| -------------------------------------------------------------------------------- | --------------------------------- | ----------------------------------------------------- | -------------------------- |
| `250` hours per month of `t2.medium` notebook usage for the first **two months** | `10,000` text requests per month  | `1,000,000` free requests per month                   | `5` GB of Standard Storage |
| `50` hours per month of `m4.xlarge` for training for the first **two months**    | `5,000` speech requests per month | Up to `3.2` million seconds of compute time per month | `20,000` Get Requests      |
| `125` hours per month of `m4.xlarge` for hosting for the first **two months**    |                                   |                                                       | `2,000` Put Requests       |

Explain to students that the quotas presented in this table are subject to change without notice, so double-checking the [AWS Free Tier Documentation](https://aws.amazon.com/free) is vital before start using the free offer from AWS.

Answer any questions before moving on.

### 2. Instructor Do: Creating an Administrator user on IAM (10 min)

In this activity, students will learn how to create an `administrator` user using AWS Identity and Access Management (IAM) service. This user will add some extra security when working on AWS.

Explain to students that a standard best practice is to avoid using the principal or _root_ user to manage their AWS account. This principal user is the one they used to create their AWS account. Instead, a new user for each person that requires administrator access should be created using the AWS Identity and Access Management (IAM) service.

Open the [AWS Management Console](https://console.aws.amazon.com) using your _root_ user, and show students how to create a new user on IAM as follows.

* Look for the IAM service on the _Find Services_ search box, type `iam` and click on `IAM` service.

  ![Create an administrator IAM user - Step 1](Images/iam-user-1.png)

* In the left pane menu, choose the _Users_ option and click on the _Add user_ button.

  ![Create an administrator IAM user - Step 2](Images/iam-user-2.png)

* On the _Add user_ page, provide your new user name in the _User name_ input box, then fill out the details of the new `administrator` by filling in the selections as seen below.  Afterword, click on the _Next: Permissions_ button to continue.

  * **User name:** `administrator`
  * **Access type:** Select the _Programmatic access_ and _AWS Management Console access_ boxes.
  * **Console password:** Choose _Custom password_ and type your password.
  * **Require password reset:** Unselect this box.

  ![Create an administrator IAM user - Step 3](Images/iam-user-3.png)

* On the _Set permissions_ page, choose _Add user to group_ and click on the _Create group_ button.

  ![Create an administrator IAM user - Step 4](Images/iam-user-4.png)

* In the _Create group_ dialog box, type `Administrators` on the _Group name_ textbox.

* Choose _Filter policies_, and then choose _AWS managed - job function_ to filter the table contents.

  ![Create an administrator IAM user - Step 5](Images/iam-user-5.png)

* In the policy list, select the checkbox for _AdministratorAccess_. Then choose the _Create group_ button.

  ![Create an administrator IAM user - Step 6](Images/iam-user-6.png)

* After creating the group, select the checkbox for your new group. Choose Refresh if necessary to see the group on the list.

  ![Create an administrator IAM user - Step 7](Images/iam-user-7.png)

* Click on the _Next: Tags_ button to continue.

* On the _Next: Tags_ page, leave the defaults and click on the _Next: Review_ button to continue.

  ![Create an administrator IAM user - Step 8](Images/iam-user-8.png)

* Review the list of group memberships to be added to the new user. When you are ready to proceed, click on the _Create user_ button.

  ![Create an administrator IAM user - Step 9](Images/iam-user-9.png)

* Once the user is created, download the user's credentials by clicking on the _Download .csv_ button. Keep those credentials safe.

  ![Create an administrator IAM user - Step 10](Images/iam-user-10.png)

Enable access to billing data for the IAM admin user as follows:

* On the navigation bar, choose your account name, and then select My Account.

  ![Create an administrator IAM user - Step 11](Images/iam-user-11.png)

* Scroll down to the _IAM User and Role Access to Billing Information_ section, and click on the _Edit_ option.

  ![Create an administrator IAM user - Step 12](Images/iam-user-12.png)

* Select the checkbox to _Activate IAM Access_ and choose _Update_.

  ![Create an administrator IAM user - Step 13](Images/iam-user-13.png)

Sign-out from your session, open the `CSV` file with the new `administrator` user credentials and login into the AWS Management Console using the user's URL and password. Tell students that from now on, they should avoid using their _root user_ and work with this new admin user instead.

Answer any questions before moving on.

---

### 3. Students Do: Creating an Admin user on IAM (10 min)

**Corresponding Activity:** [01-Stu_IAM_User](Activities/01-Stu_IAM_User)

In this activity, students will create an administrator user to manage their AWS account.

**Instructions:**

* [README.md](Activities/01-Stu_IAM_User/README.md)

---

### 4. Instructor Do: Review Creating an Admin user on IAM (10 min)

This review activity is intended to verify that all students have successfully created their admin user using IAM.

Make sure all students have their AWS account working correctly, also, ask TAs to assist students who could have an issue before moving forward, forthcoming activities will use the `administrator` IAM user by default.

Answer any questions before moving on.

---

### 5. Everyone Do: Create an Amazon SageMaker Notebook Instance (20 min)

**Corresponding Activity:** [02-Evr_SageMaker](Activities/02-Evr_SageMaker)

In this activity, students will learn how to create an instance of Amazon SageMaker, and how to use Jupyter notebooks on the AWS cloud. This activity will utilize the Alpaca API. If issues are encountered in doing so, please check the [Alpaca Installation Guide](../Supplemental/AlpacaMarkets_Installation-Guide.md) for assistance. Students will need to have their Alpaca keys handy, as they will be saving them into a `.env` file.

**Files:**

* [monte_carlo.ipynb](Activities/02-Evr_SageMaker/Solved/monte_carlo.ipynb)

Comment to students that you will demo how to create an Amazon SageMaker Notebook Instance, ask them to follow your steps as you move along the demo. Ask TAs to assist students if they get stuck along the process.

Login into your _AWS Management Console_ using your admin user, tell students that the first component that Amazon SageMaker requires is an [Amazon S3](https://aws.amazon.com/s3) bucket to store data to feed machine learning models, or to save prediction results.

To create an Amazon S3 bucket, follow the next steps:

* On the _Find Services_ search box, type `S3` and select the _S3_ service from the list.

  ![Creating an Amazon SageMaker instance - step 1](Images/sagemaker-1.png)

* On the Amazon S3 console, click on the _Create bucket_ button.

  ![Creating an Amazon SageMaker instance - step 2](Images/sagemaker-2.png)

* Fill the following details on the _Create button_ window.

  * **Section: Name and region**
    * _Bucket name:_ `sagemaker-<CURRENT-DATE+TIME>` (for example: `sagemaker-20190903-1026`)
    * _Region:_ `US West (Oregon)` (S3 and SageMaker instance regions should be the same)
    * Click: _Next_
    ![Creating an Amazon SageMaker instance - step 3](Images/sagemaker-3.png)
  * **Section: Configure options**
    * Click: _Next_ (leave defaults)
    ![Creating an Amazon SageMaker instance - step 4](Images/sagemaker-4.png)
  * **Section: Set permissions**
    * Click: _Next_ (leave defaults)
    ![Creating an Amazon SageMaker instance - step 5](Images/sagemaker-5.png)
  * **Section: Review**
    * Click: _Create bucket_
    ![Creating an Amazon SageMaker instance - step 6](Images/sagemaker-6.png)

* Note down (copy/paste/save) the name of the bucket for use in the following section.

Explain to students that the next step is to create a Jupyter notebook instance on Amazon SageMaker. Follow the next steps.

* Navigate to the AWS Management Console homepage, on the _Find Services_ search box type _sagemaker_, and select Amazon SageMaker from the list.

  ![Creating an Amazon SageMaker instance - step 7](Images/sagemaker-7.png)

* On the Amazon SageMaker console, be sure that `Oregon` is the selected region, on the left pane menu, under _Notebook_ section choose _Notebook instances_.

  ![Creating an Amazon SageMaker instance - step 8](Images/sagemaker-8.png)

* On the _Notebook instances_ page, click on the _Create notebook instance_ button.

  ![Creating an Amazon SageMaker instance - step 9](Images/sagemaker-9.png)

* Fill in the following values on the _Create notebook instance_ page:

  * **Section: Notebook instance settings**
    * _Notebook instance name:_ `sm-test`
    * _Notebook instance type:_ `ml.t2.medium`
    * _Elastic Inference:_ `none`
    ![Creating an Amazon SageMaker instance - step 10](Images/sagemaker-10.png)
  * **Section: Permissions and encryption**
    * _IAM role:_ On the dropdown list, select the `Create a new role` option.
    ![Creating an Amazon SageMaker instance - step 11](Images/sagemaker-11.png)
    * Under the _S3 buckets you specify - optional_ section, choose _Specific S3 buckets_ and type the name of the Amazon S3 bucket you created in the preceding section (e.g. `sagemaker-20190903-1026`), click on _Create role_ to continue.
    ![Creating an Amazon SageMaker instance - step 12](Images/sagemaker-12.png)
    * _Root access:_ Be sure that the `Enable - Give users root access to the notebook` option is selected. Tell students that this option is less safe but allows more control over the instance.
    ![Creating an Amazon SageMaker instance - step 13](Images/sagemaker-13.png)

* Scroll down and click on the _Create notebook instance_ button to continue. Comment to students that the creation process takes up to five minutes to finish.

  ![Creating an Amazon SageMaker instance - step 14](Images/sagemaker-14.png)

While the notebook instance is being created, explain to students that AWS charges for these and most resources as they are created, even when not in use, this instance is billed for by the second until it's turned off and deleted. Students will learn how to eliminate these resources later in Today's class.

Explain to students that, as long as the free tier is used, there are no charges associated with Today's activities, however, if any of the students use an account that is more than two months old, the tasks performed Today using Amazon SageMaker may have an associated cost.

* Once the notebook instance status is _InService_, it's ready to be used; on the _Actions_ column, click on `Open JupyterLab` to continue.

  ![Creating an Amazon SageMaker instance - step 15](Images/sagemaker-15.png)

* After a few seconds, you will see the Jupyter Lab UI, comment to students that this notebook is running on the AWS Cloud.

* On the _Notebook_ section in the JupyterLab _Launcher_, select the `conda_python3` environment to create a new notebook.

  ![Creating an Amazon SageMaker instance - step 16](Images/sagemaker-16.png)

* On the new notebook, code a `hello world` statement in Python in the first cell as a test. Be sure that all of the class has reached this point before moving forward.

  ![Creating an Amazon SageMaker instance - step 17](Images/sagemaker-17.png)

Explain to students that it's possible to code a Jupyter notebook from scratch on this Amazon SageMaker's notebook instance, but also, you can open an existing Jupyter notebook. Slack out to students the `monte_carlo.ipynb` starter file, and continue the demo as follows.

* This demo code runs a Monte Carlo simulation that uses the Alpaca API, so ask students to have their API keys at hand. 

* In your Amazon SageMaker notebook instance, in left icon menu, click on the _Upload_ icon (arrow up) and select the `monte_carlo.ipynb` and `sample.env` files to upload.

  ![Creating an Amazon SageMaker instance - step 18](Images/sagemaker-18.png)

* Direct students to open the `sample.env` file inside Jupyter Lab, add their Alpaca keys, save the file, then rename it as `.env`.    

* Open the `monte_carlo.ipynb` notebook. You'll probably see the message: `Select Kernel` or `Kernel not found`, select `conda_python3` and click on `Select` or `Set Kernel` to continue.

  ![Creating an Amazon SageMaker instance - step 19](Images/sagemaker-19.png)

* Run all the cells in the notebook, comment to students that now this notebook is going to run on the AWS cloud using Amazon SageMaker.

  ![Creating an Amazon SageMaker instance - step 20](Images/sagemaker-20.gif)

End the demo and answer any questions before moving on.

---

### 6. Everyone Do: Create and Deploy a Machine Learning Model in Amazon SageMaker (30 min)

**Corresponding Activity:** [03-Evr_SageMaker_Deployment](Activities/03-Evr_SageMaker_Deployment)

In this activity, students will learn how to create, train, deploy, and evaluate a machine learning model in Amazon SageMaker.

This is a collaborative activity where you will lead the class through all the process, be sure to keep the pace allowing the students to follow you. Ask TAs to assist any student that may be stuck during the activity.

**Files:**

* [rainfall_forecast.ipynb](Activities/03-Evr_SageMaker_Deployment/Unsolved/rainfall_forecast.ipynb)

* [x_austin_final.csv](Activities/03-Evr_SageMaker_Deployment/Resources/x_austin_final.csv)

* [y_austin_final.csv](Activities/03-Evr_SageMaker_Deployment/Resources/y_austin_final.csv)

Explain to students that Amazon has created an extensive library of machine learning models that are optimized for the cloud. This demo will show how to use one of those models.

Tell students that in this demo, you are going to demo one of Amazon's linear regression models to predict the amount of rain that will fall in Austin, given the average temperature in Fahrenheit degrees.

Slack out the unsolved version of the Jupyter notebook and the two data files to the students, ask the class to follow you on this demo.

Start the demo by opening the Jupyter lab UI at your Amazon SageMaker instance and create a new folder called `Data`.

![Deploy SageMaker Model - step 1](Images/deploy-sagemaker-1.png)

Open the `Data` folder, upload the following `CSV` files and highlight the following:

* `x_austin_final.csv`, this file contains historical weather conditions in Austin, Texas, along 1319 days.

* `y_austin_final.cvs`, this file contains the precipitations sum in inches in Austin, Texas, along 1319 days.

![Deploy SageMaker Model - step 2](Images/deploy-sagemaker-2.png)

Navigate to the main folder on Jupyter lab, and import the unsolved version of the Jupyter notebook to your Amazon SageMaker notebook instance, live code the demo by highlighting the following:

* In the `Initial imports` section, some well-known libraries are imported. The `sklearn` library will be used to split the dataset in training and testing sets, as well as to evaluate the model.

  ```python
  import os
  import io
  import json
  import numpy as np
  import pandas as pd
  from path import Path
  import matplotlib.pyplot as plt
  from sklearn.model_selection import train_test_split
  from sklearn.metrics import mean_squared_error, r2_score
  ```

* To use Amazon SageMaker, the following libraries are needed.

  ```python
  import sagemaker
  import sagemaker.amazon.common as smac
  from sagemaker.predictor import csv_serializer, json_deserializer
  from sagemaker import get_execution_role
  from sagemaker.amazon.amazon_estimator import get_image_uri
  import boto3
  ```

* Along with the `sagemaker` modules, the `boto3` library is imported; `boto3` is the [AWS SDK for Python](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html).

* The data to train and test the model, is loaded into two Pandas DataFrames, next, the data is transformed into a vector as follows:

  * The `x_austin_final.csv` data is loaded into the `features` DataFrame, the `TempAvgF` column that denotes the average temperature in Austin in Fahrenheit degrees is taken as an input variable (predictor) for the linear model.

    ```python
    # Read the weather features data
    file_path = Path("Data/x_austin_final.csv")
    features = pd.read_csv(file_path)

    # Transforming the "TempAvgF" column to a vector
    X = features["TempAvgF"].values.reshape(-1, 1)
    ```

  * The `y_austin_final.csv` data is initially loaded into the `y` DataFrame; this data represents the target variable in the linear model.

    ```python
    # Read the target data (precipitation sum inches)
    file_path = Path("Data/y_austin_final.csv")
    y = pd.read_csv(file_path, names=["PrecipitationSumInches"], header=None)

    # Transforming y into a vector
    y = y.iloc[:, 0].values
    ```

* The data is split into a training and testing DataSets using the `train_test_split()` function from `sklearn`.

  ```python
  X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
  ```

Comment to students that once the data is loaded, the next step is to create the linear regression model. The process starts with some initial configurations as follows:

* The training and testing data should be stored in an Amazon S3 bucket, so a variable to store the name of the bucket we created before is defined.

  ```python
  bucket = "sagemaker-bucket-name-here"
  ```

* To identify the data files stored in Amazon S3, a prefix is defined.

  ```python
  prefix = "austin-rainfall-regression"
  ```

* The current role of the IAM user running the notebook is stored in the `role` variable.

  ```python
  role = get_execution_role()
  ```

Now it's time to upload the data to Amazon S3. Explain to students that, to train the machine learning model using Amazon SageMaker, the training and testing data should pass through an Amazon S3 Bucket formatted using the [protobuf recordIO format](https://docs.aws.amazon.com/sagemaker/latest/dg/cdf-training.html#td-serialization).

* The profobuf recordIO format, is a method to serialize structured data (similar to `JSON`), to allow different applications to communicate with each other or for storing data.

Explain to students that, using the profobuf recordIO format, allows you to take advantage of _Pipe mode_ when training the algorithms that support it. In _Pipe mode_, your training job streams data directly from Amazon S3. Streaming can provide faster start times for training jobs and better throughput.

Continue with the following code, to format the training data as a protobuf recordIO, and upload it to the Amazon S3 bucket.

```python
# Encode the training data as Protocol Buffer
buf = io.BytesIO()
vectors = np.array(X_train).astype("float32")
labels = np.array(y_train).astype("float32")
smac.write_numpy_to_dense_tensor(buf, vectors, labels)
buf.seek(0)

# Upload encoded training data to Amazon S3
key = 'linear_train.data'
boto3.resource("s3").Bucket(bucket).Object(os.path.join(prefix, "train", key)).upload_fileobj(buf)
s3_train_data = "s3://{}/{}/train/{}".format(bucket, prefix, key)
print("Training data uploaded to: {}".format(s3_train_data))
```

Tell students that, if you provide test data, the algorithm logs include the test score for the final model. Live code the following to upload the testing data.

```python
# Encode the testing data as Protocol Buffer
buf = io.BytesIO()
vectors = np.array(X_test).astype("float32")
labels = np.array(y_test).astype("float32")
smac.write_numpy_to_dense_tensor(buf, vectors, labels)
buf.seek(0)

# Upload encoded testing data to Amazon S3
key = "linear_test.data"
boto3.resource("s3").Bucket(bucket).Object(os.path.join(prefix, "test", key)).upload_fileobj(buf)
s3_test_data = "s3://{}/{}/test/{}".format(bucket, prefix, key)
print("Testing data uploaded to: {}".format(s3_test_data))
```

Once you have uploaded your data to Amazon S3, it's time to train the machine learning model. Comment to students that in this demo, you will use the Amazon SageMaker's [_linear learner algorithm_](https://docs.aws.amazon.com/sagemaker/latest/dg/linear-learner.html) to run a linear regression prediction model.

Create the instance of the linear learner algorithm and highlight the following:

* The instance of the `linear learner` is created using the `get_image_uri()` method from the `sagemaker` library.

  ```python
  container = get_image_uri(boto3.Session().region_name, "linear-learner")
  ```

* Before creating the estimator container, an Amazon SageMaker's session should be started.

  ```python
  sess = sagemaker.Session()
  ```

* The estimator container is an AWS EC2 instance that will store and run the model. The estimator container is created using a `ml.m4.xlarge` instance type for training the model.

  ```python
  linear = sagemaker.estimator.Estimator(
    container,
    role,
    train_instance_count=1,
    train_instance_type="ml.m4.xlarge",
    output_path="s3://{}/{}/output".format(bucket, prefix),
    sagemaker_session=sess
    )
  ```

* The linear learner hyperparameters are defined next, it's important to highlight that the `feature_dim` parameter should match with the number of predictors in `X`, in this case since we only have one predictor its value is `1`.

  ```python
  # Define linear learner hyperparameters
  linear.set_hyperparameters(
    feature_dim=1,
    mini_batch_size=100,
    predictor_type="regressor",
    epochs=10,
    num_models=32,
    loss="absolute_loss"
    )
  ```

* The model is trained using the `fit` method of the Amazon SageMaker estimator. **Note:** This step might take a few minutes.

  ```python
  linear.fit({'train': s3_train_data, 'test': s3_test_data})
  ```

Explain to students that this step might take a few minutes, and it will use resources from the AWS account. Typically, this time is not billed in the two months trial period. However, clarify to students that policies of the AWS free and trial offer changes regularly, so they should always check the pricing pages for any service that they want to use. Below, a sample output is shown, you will notice that the output text is in blue.

**Important Note:** Explain to students that this step may take up to 15 minutes since Amazon SageMaker is provisioning not only a Jupyter notebook but also a series of virtual machines (EC2 instances) to compute the model. If you are running out of time in this activity, open the solved version of the notebook and continue the demo by dry-walking through the code.

![Deploy SageMaker Model - step 3](Images/deploy-sagemaker-3.gif)

Once the `linear-learner` model was trained, tell students that it can be deployed to make predictions of the rainfall in Austin. Continue the demo and highlight the following:

* In order to make predictions, the model should be deployed; a `ml.t2.medium` instance type is defined since this is the instance type we selected when we created the notebook that is part of the free tier offer. This step will take about ten minutes.

  ```python
  linear_predictor = linear.deploy(initial_instance_count=1, instance_type="ml.t2.medium")
  ```

* Some configurations should be made to specify the type of data files that are going to be used and to define how the data is going to be serialized and deserialized.

  ```python
  linear_predictor.serializer = csv_serializer
  linear_predictor.deserializer = json_deserializer
  ```

* To make predictions, we use the `predict()` method of the model. We will make predictions using the testing data; results are stored in the `y_predictions` array.

  ```python
  result = linear_predictor.predict(X_test)
  y_predictions = np.array([r["score"] for r in result["predictions"]])
  ```

Explain to students that once you have the predictions, the model can be evaluated using the techniques they already know. First, a plot to contrast the predicted rainfall values versus the real values is created.

![Deploy SageMaker Model - step 4](Images/deploy-sagemaker-4.png)

Additionally, the `RMSE` and `R2` scores are calculated.

![Deploy SageMaker Model - step 5](Images/deploy-sagemaker-5.png)

Finally, after reviewing the model evaluation's results, explain to students that the endpoint needs to be deleted to avoid additional AWS resources usage and extra billing.

```python
sagemaker.Session().delete_endpoint(linear_predictor.endpoint)
```

Slack out the following page to students, where they can learn more about the different Amazon SageMaker built-in algorithms.

* [Use Amazon SageMaker Built-in Algorithms](https://docs.aws.amazon.com/sagemaker/latest/dg/algos.html).

Answer any questions before moving on.

---

### 7. Break (15 min)

---

### 8. Instructor Do: Pros and Cons of Deploying Machine Learning Models with Amazon SageMaker (10 min)

Lead and facilitate a discussion around deploying models in Amazon SageMaker and why a RESTful ML API is useful.

Have students share their opinions with the class and bring up the following points:

**Pros:**

* Data storage capacity: By using an Amazon S3 bucket to store the data, we could have trained a model on multiple terabytes of data, or a lot more space than would otherwise have fit in our personal computer.

* Hardware / GPU: By using different Amazon SageMaker instances to train our model, we can access compute power, including GPU capabilities, making powerful hardware available to us as required.

* Cost: Using AWS resources, we only pay for what we use, we'll turn off everything before ending the class and not incur further charges.

* Availability: By deploying our model to another Amazon SageMaker instance, we have made the prediction functionality available 24/7 through a secured endpoint to an application or to be consumed by others without having to make our computer available.

* RESTful API: As learned in previous units, APIs provide a standard mechanism to access data; our ML API can be consumed through apps and other channels in a simple form while remaining secure and allowing other constraints (for example authentication, authorization, rate limiting, etc.).

**Cons:**

* Data privacy/security: By uploading data to a third party, you are trusting your data to them. Certain kinds of data are subject to compliance and regulatory constraints.

* Visibility: You won't have oversight on AWS internal handling of your data and infrastructure.

* Availability: Although there are SLAs in place, AWS (and any other cloud providers) can and have suffered outages at times, causing data unavailability.

Answer any questions before moving on.

---

### 9. Students Do: Credit Risk Evaluation with Amazon SageMaker (30 min)

**Corresponding Activity:** [04-Stu_Credit_Risk_Classification](Activities/04-Stu_Credit_Risk_Classification)

In this activity, students will train and deploy a binary classification model to predict the *credit risk* of a person using the [German Credit Risk dataset](https://archive.ics.uci.edu/ml/datasets/statlog+(german+credit+data)) and the SageMaker built-in `Linear Learner` algorithm.

This activity will require the use of an AWS SageMaker notebook instance; the unsolved notebook will guide students through the process and indicate what the missing code snippets are.

**Note:** Remember that the time needed to deploy the model is about 10 minutes.

**Instructions:**

* [README.md](Activities/04-Stu_Credit_Risk_Classification/README.md)

**Files**:

* [credit-risk-classification.ipynb](Activities/04-Stu_Credit_Risk_Classification/Unsolved/credit-risk-classification.ipynb)

* [german_credit_data.csv](Activities/04-Stu_Credit_Risk_Classification/Resources/german_credit_data.csv)

---

### 10. Instructor Do: Review Credit Risk Evaluation with Amazon SageMaker (15 min)

* Open JupyterLab in your AWS SageMaker notebook instance.

* Upload the dataset: [german_credit_data.csv](Activities/04-Stu_Credit_Risk_Classification/Resources/german_credit_data.csv)

* Upload the solved notebook: [credit-risk-classification.ipynb](Activities/04-Stu_Credit_Risk_Classification/Solved/credit-risk-classification.ipynb)

* Walk-through the solved notebook, cell by cell, highlighting the following points:

  * The output of the model prediction is a binary label (0, 1): "Good" or "Bad" Credit Risk.

  * Despite using a "curated" dataset, we still need to perform some data preparation tasks: *split*, *hot-encode* and *scale* the input features.
  
  * We use the AWS SageMaker built-in `linear-lerner` algorithm by setting the hyper-parameter `predictor_type` to "binary_classifier".

  * The predictions are in the `predicted_label` field in the prediction result.

  * Lastly, for our model evaluation, besides the accuracy score, we use a confusion matrix to get a quick sense of the model's true-positive/negative and false-positive/negative prediction combinations.

  * Remember that you should delete the endpoint you created when the SageMaker model was deployed in order to avoid additional charges. The endpoint is deleted by executing the code in the last cell of the notebook.

    ```python
    # Delete Amazon SageMaker end-point
    sagemaker.Session().delete_endpoint(linear_predictor.endpoint)
    ```

Answer any questions before moving on.

---

### 11. Instructor Do: Delete AWS Resources (10 min)

In this activity, students will learn how to delete the AWS resources they created so that no billing charges are incurred for them after class.

#### Deleting the Amazon SageMaker Notebook Instance

Open the Amazon SageMaker console then, on the left pane menu, under the _Notebook_ section, click on _Notebook instances_.

![Deleting Amazon SageMaker instance - 1](Images/deleting-sm-1.png)

Select the `sm-test` notebook instance on the left by clicking the circular dot, once selected, click on the right _Actions_ menu and select _Stop_.

![Deleting Amazon SageMaker instance - 2](Images/deleting-sm-2.png)

Refresh the page and wait for the instance `Status` to change to `Stopped`. **Note:** This process will take few minutes.

![Deleting Amazon SageMaker instance - 3](Images/deleting-sm-3.png)

Select the instance again on the left circular dot, click on _Actions_ and select _Delete_, then confirm the delete.

![Notebook Instance delete](Images/notebook-confirm-delete.png)

* At the end of Today's lesson, the notebook instances list should be empty and state: "There are currently no resources.", otherwise charges will be incurred for any remaining active instances.

#### Manually Deleting Amazon SageMaker Endpoints

Explain to students that, despite we have code in our notebooks to explicitly delete the Amazon SageMaker endpoints, we may omit to run that cell, and the endpoints will keep alive and running. If this happens, additional charges may incur.

Highlight to students that a common practice is to verify if all the endpoints were deleted. To do so, follow the following steps.

* On the Amazon SageMake console, open the "Inference" menu on the left. Next, choose the "Endpoints" option.

  ![Checking for running endpoints in the Amazon SageMaker console](Images/13-2-checking-endpoints.png)

If you see a page like the one shown in the previous image, explain to students that there is one running endpoint. If we already finish working with SageMaker, it's recommendable to delete this endpoint to avoid additional charges.

If you see an empty list like the image below, there are no running endpoints.

![The Amazon SageMaker console showing that there are no running endpoints](Images/13-2-no-running-endpoints.png)

To manually delete an endpoint, proceed as follows:

* Select the endpoint you want to delete from the list.

  ![Selecting an endpoint to delete](Images/13-2-select-endpoint.png)

* Next, on the top menu at the right, select "Delete" under the "Actions" option.

  ![Choosing the Delete action to remove an endpoint](Images/13-2-delete-endpoint.png)

* A confirmation message will appear next. Click on the "Delete" button to permanently delete the endpoint and avoid additional charges.

  ![Delete endpoint confirmation message](Images/13-2-delete-endpoint-confirmation.png)

* After clicking on the "Delete" button of the confirmation message, you will see that the "Deleting" status is shown in the endpoint you chose to delete. After a few minutes, the endpoint will be removed.

  ![Deleting Amazon SageMaker endpoint](Images/13-2-deleting-endpoint.png)

#### Deleting Amazon S3 Buckets

Go to the Amazon S3 console, and remove the buckets created for the activity as follows.

* Choose the checkbox next to the bucket name, next click on the _Delete_ button.

![Deleting Amazon SageMaker instance - 4](Images/deleting-sm-4.png)

* On the _Delete bucket_ window, type the name of the bucket to confirm that you want to delete it, next click on the _Confirm_ button to finish.

![Deleting Amazon SageMaker instance - 5](Images/deleting-sm-5.png)

Answer any questions before moving on.

---

### 12. Everyone Do: Delete AWS resources (15 mins)

In this activity, students will delete all the AWS resources created in Today's class to avoid additional charges.

Explain to students, as it was mentioned before, the policies for the AWS free tier and trials continually change. Hence, it's essential to remove any unnecessary resources created on AWS to avoid additional charges, especially Amazon SageMaker instances, since regardless when they are stopped, AWS bills you for hosting the instances.

Collaborate with TAs, on assisting students in deleting all the AWS resources that students need to remove. Remind students, that they can save a local copy of the Jupyter notebooks, by right-clicking on the notebook name and selecting the _Download_ option.

![Downloading a Jupyter notebook](Images/download-notebook-sm.png)

Answer any questions before finishing class.

---

### End Class

---

© 2021 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.
