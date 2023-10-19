
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
 1) [Transformers HuggingFace NER Model](https://huggingface.co/MehdiHosseiniMoghadam/skill-role-mapper?text=Searching+for+new+opportunities+as+Junior+Node.js+JavaScript+backend+developer.+Over+15+years+of+experience+in+different+IT+areas.+Experience+with%3A+Node.js+JavaScript+MongoDB++HTML+CSS+Java+Lotus+Script+websocket+socket.io+Docker+babel+Webpack+MySQL+JSON+React+and+project+management+legal) (click on the [link](https://huggingface.co/MehdiHosseiniMoghadam/skill-role-mapper?text=Searching+for+new+opportunities+as+Junior+Node.js+JavaScript+backend+developer.+Over+15+years+of+experience+in+different+IT+areas.+Experience+with%3A+Node.js+JavaScript+MongoDB++HTML+CSS+Java+Lotus+Script+websocket+socket.io+Docker+babel+Webpack+MySQL+JSON+React+and+project+management+legal) to get the live interactive demo on HiggingFace website) 
 
 Trainin script for Flair can be found in:  `Custom_Named_Entity_Recognition_with_Flair.ipynb`

Training script for transformer huggingface model can be found in:
 `Custom_Named_Entity_Recognition_with_distillbert.ipynb`
 
 To test NER with both models look at this file:
 `HuggingFace_&_Flair_Test.ipynb`
 
