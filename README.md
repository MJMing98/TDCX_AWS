# TDCX's Machine Learning Engineer Assessment
## <ins> Description </ins>
The main goal is to create an A.I. Model and have it deployed onto a cloud computing service where an inference score can then be requested through a RESTful endpoint. For the test, an Isolation Forest model from the Scikit-learn library was be used, and as for the cloud computing platform, AWS. For simplicity's sake, all of the code was written on Amazon SageMaker.

## <ins> Dataset </in>
The dataset used for this task is called the KDD Cup 99 dataset of the SF subset variant, which contained only connections where the **is_logged** feature from the original form of the KDD Cup 99 dataset is sampled as a positive, which implies that any abnormal connections that are labelled as a malicious connection are therefore intrusion attacks. The dataset has a total sample of 699691 recorded connections with a dimensionality of 4, and out of the 4 features only one of which is categorical.

| Feature name    | Type    |
| -------- | ------- |
| duration  | Float    |
| service | String     |
| src_bytes    | Float   |
| dst_bytes    | Float   |

## <ins> Preprocessing and Machine Learning training </in>
### Initialization of the data ###
With the way SageMaker works, 2 separate notebooks were created:
1. **init.ipynb**, which is responsible for the initial setting up of the infrastructure for both the AI model as well as the SageMaker session, 
2. **trainingScript.py**, which is the script responsible for the actual preprocessing, training, testing and inference scoring of the AI model.

The dataset was first imported into the init.ipynb and then split into a train/test dataset with a 70:30 ratio respectively. From there, the init.ipynb notebook then uploads both train/test datasets into an S3 bucket where the data would then be stored. It then creates an estimator object from SageMaker's Python SDK and tells the estimator to run the training script, after which the model is then persisted on SageMaker and ready for deployment.

When the training script gets the data from the S3 bucket, it first one-hot encodes the categorical feature **service** into separate columns as the isolation forest doesn't take object-type data as inputs. It then trains the isolation forest model with said data, saves the model, fits the testing data into the model, and would then output the classification report of the model. Since the main focus of the test is more on the actual deployment of the model and not so much on the actual performance of the model, not much attention was put into the preprocessing step of the model. This, however, is not much of an issue as the isolation forest model is natively based off the random forest model, making scaling and normalizing numerical features not that huge of a priority.

## <ins> AWS Services used </in>
A couple of AWS services were used during the entire duration of the project (apart from SageMaker):
- Amazon Lambda, an event-driven computing platform,
- Amazon S3, a simple cloud storage service,
- Amazon CloudWatch, a logger service for your AWS resources/services,
- Amazon API Gateway, a service for creating/managing/monitor real-time APIs

For the ML model to work through HTTP requests, an API gateway as well as a handler function is needed. A simple handler script was first deployed on Amazon Lambda, in which the handler parses the data within the requsts's body to the model's endpoint that was created by SageMaker. After the handler function is deployed, a REST API is then created through Amazon API Gateway so that users could query inference scores of a sample of the data through a HTTP request.
