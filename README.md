
# RoleMapper Custom NER

### Dataset used:
Dataset is the combination of 5 different data sources from kaggle:
- [Dataset 1](https://www.kaggle.com/datasets/cedricaubin/linkedin-data-analyst-jobs-listings)
- [Dataset 2](https://www.kaggle.com/datasets/JobsPikrHQ/usa-based-job-data-set-from-300-companies)
- [Dataset 3](https://www.kaggle.com/datasets/shivamb/real-or-fake-fake-jobposting-prediction)
- [Dataset 4](https://www.kaggle.com/code/rashikrahmanpritom/explanatory-data-analysis-on-glassdoor-jobs)
- [Dataset 5](https://www.kaggle.com/datasets/shivamb/real-or-fake-fake-jobposting-prediction)

Note: none of these datasets have any kind of NER tagging, I will use a python script to extract all such data out of these
All data processing is in `NER_Data_Gathering_and_Processing.ipynb` file

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

