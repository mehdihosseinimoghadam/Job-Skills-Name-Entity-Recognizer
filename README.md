
# RoleMapper Custom NER

### Dataset used:
Dataset is the combination of 5 different data sources from kaggle:
- [Dataset 1](https://www.kaggle.com/datasets/cedricaubin/linkedin-data-analyst-jobs-listings)
- [Dataset 2](https://www.kaggle.com/datasets/JobsPikrHQ/usa-based-job-data-set-from-300-companies)
- [Dataset 3](https://www.kaggle.com/datasets/shivamb/real-or-fake-fake-jobposting-prediction)
- [Dataset 4](https://www.kaggle.com/code/rashikrahmanpritom/explanatory-data-analysis-on-glassdoor-jobs)
- [Dataset 5](https://www.kaggle.com/datasets/shivamb/real-or-fake-fake-jobposting-prediction)

Note: none of these datasets have any kind of NER tagging, I will use a python script to extract all such data out of these. All data processing is in `NER_Data_Gathering_and_Processing.ipynb` file

### NER model training scripts:
 There are two models for NER in this repo. 
 1) [Flair NER](https://github.com/flairNLP/flair) 
 1) [Transformers HuggingFace NER Model](https://huggingface.co/MehdiHosseiniMoghadam/skill-role-mapper?text=Searching+for+new+opportunities+as+Junior+Node.js+JavaScript+backend+developer.+Over+15+years+of+experience+in+different+IT+areas.+Experience+with%3A+Node.js+JavaScript+MongoDB++HTML+CSS+Java+Lotus+Script+websocket+socket.io+Docker+babel+Webpack+MySQL+JSON+React+and+project+management+legal) (click on the 🤗 [link](https://huggingface.co/MehdiHosseiniMoghadam/skill-role-mapper?text=Searching+for+new+opportunities+as+Junior+Node.js+JavaScript+backend+developer.+Over+15+years+of+experience+in+different+IT+areas.+Experience+with%3A+Node.js+JavaScript+MongoDB++HTML+CSS+Java+Lotus+Script+websocket+socket.io+Docker+babel+Webpack+MySQL+JSON+React+and+project+management+legal) to get the live interactive demo on HiggingFace website) 
 
 Trainin script for Flair can be found in:  `Custom_Named_Entity_Recognition_with_Flair.ipynb`

Training script for transformer huggingface model can be found in:
 `Custom_Named_Entity_Recognition_with_distillbert.ipynb`
 
 To test NER with both models look at this file:
 `HuggingFace_&_Flair_Test.ipynb`



 

### Preprocessing steps:
1) For data Processing I will first make a list of all possible skills avalible (this can be done with prompt engineering with chatgpt or crawling job sites)

2) Then I need some job description text to label them with avalible list of skills (these job descriptions will be gathered from 5 datasets mentioned above)

3) Target is to turn this text `We need someone with statistical background and fluent in python` to 
`O,O,O,O,In-Sk,O,O,O,O,In-Sk`

4) First I break all extracted texts from above datasets to smaller sentences (split with '.' )
then I remove punctuation, \r, \n and some other words. I won't use stemming or lemetization
because I don't want to manipulate the nature of text too much.
Next, I will use regex to search in each sentence if there is any word from skills list in that sentence, if yes, label it as 'In-SK' otherwise label it as 'O'.(note some skills like project management will be counted twice, because it has two words it it.)
Finally turn all sentence/label pairs into dataframe, it is roughly about 125K pairs.



### Summary of model and training methods used:

Two main method used to train the skill NER model:

-

| Model | Embedding | lr | BatchSize | #Epoch | Precision | Recall | f1 | train size| GPU/hour |
| ------ | ------ |------ | ------ |------ | ------ |------ | ------ |------ | ------ |
 | Flair | distilbert | .04 | 16 | 4 | .98 | .98 | .98 | 40K pairs | T4/4h (colab)|
| Transformer | distilbert | 4e-06 | 16 | 3 | .69 | .81 | .75 | 100K pairs | T4/3h (colab) |


###  Analysis of model results along with chosen metrics for evaluation:
 
 - **Flair** : General accuracy for flair model on test set  is 98.36%, (precision, recall, and f-1) for `In-SK` are as follow (98.2%, 98.4%, and 98.3%)
 - Accuracy alone is not a good metric here because NER dataset is generally a kind of imbalanced data so having precision and recall as well as f-1 would give a better understanding of the model accuracy.
 
 - **Transformer HF🤗** : General accuracy for Transformer🤗 model on valid set  is 97.5%, (precision, recall, and f-1) for `In-SK` are as follow (69%, 81%, and 75%)


### Transformer VS Flair: For now between these to that I have trained Flair is Better :)
here are some screenshots of results:

- *Frontend Job Desc. with Transformer HF*:
 <img src="https://github.com/mehdihosseinimoghadam/RoleMapper/blob/main/assets/frontend.JPG" height="900" width="600" >

- *Same Frontend Job Desc. with Flair*:
 <img src="https://github.com/mehdihosseinimoghadam/RoleMapper/blob/main/assets/flair%20frontend1.JPG" height="900" width="800" >

### So it is clear that from only this example(there much more) that transformers have a hard time to detect some of the skills (specially those that are kind of short form of a longer name like SQL or Git), this can be because of several reasons: 
- Training duration (starting warm up)
- Flair uses bert as embedding so it is definately very strong, but it also uses conditional random fields (CRF) as an additional step as well
-  other reason could be data, I used 100K pairs to train transformer but only 40K to train flair (however I think using more data will make it even stronger).
-  Also Transformer has two more tokens `start` and `end` which can make difference



### Here are some more examples of NER with different real job posts from indeed

- Data Science
 <img src="https://github.com/mehdihosseinimoghadam/RoleMapper/blob/main/assets/data%20science.JPG" height="900" width="800" >



- Digital Marketing
 <img src="https://github.com/mehdihosseinimoghadam/RoleMapper/blob/main/assets/digital%20marketing.JPG" height="900" width="800" >



- Digital Marketing1
 <img src="https://github.com/mehdihosseinimoghadam/RoleMapper/blob/main/assets/digital%20marketing1.JPG" height="900" width="800" >



- FrontEnd
 <img src="https://github.com/mehdihosseinimoghadam/RoleMapper/blob/main/assets/frontend.JPG" height="900" width="800" >



- Legal
 <img src="https://github.com/mehdihosseinimoghadam/RoleMapper/blob/main/assets/legal.JPG" height="900" width="800" >



- Warehouse
 <img src="https://github.com/mehdihosseinimoghadam/RoleMapper/blob/main/assets/warehouse.JPG" height="900" width="800" >





### Instructions for reproducing results:
To reproduce the results, just open each of notebooks in google colab or local jupyter lab and start running cells one by one, top to bottom, all weights are in drive and will be downloaded with Gdown
(just in case of any problem let me know so I can fix any possible problem)

### Future work:
As explained [here](https://medium.com/product-ai/automatic-speech-recognition-for-specific-domains-28f400fb9000), for future work we need to have two kinds of model
- General Domain
- Specific Domain

Because there are lots of different job fields out there and some words might have different meanings in different contexts, we can have one general model to detect general skills like *communication, writing, presenting, ...*
and one model for each of the fields that we know for example Data, Legal, ...
You might ask how we can underestant which one to use when a new user comes and sends a CV or JD, it is simple: we can add a field in a form (like: *Please enter your field of interest*) and once done that field will invoke the related model from pool of models thathat we have or this also can be done with text classification module.

### How to serve in AWS:

To serve the model wecan use AWS Lambda function to make a simple serverless function from NER model and connecting that to AWS api gateway to make sure that we can handle huge requests and finally we can use dynamodb to log all data.
To better define and handle exceptions it is recomended to define a AWS step function machine.

Also for multi model case (case that we have several models we can use one lambda to detect the type of JD or CV (this also can be done with text classification) and then that middle lambda will envode the other lambda which the model lives)

 <img src="https://github.com/mehdihosseinimoghadam/RoleMapper/blob/main/assets/aws1.JPG" height="900" width="1000" >




Prediction results in a CSV files:

`flair_results_sample.csv` and `transformer_bert_results_sample.csv` contains sample predictions from each model



