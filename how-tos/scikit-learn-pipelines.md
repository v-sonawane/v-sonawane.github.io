---
description: Are you using them correctly?
---

# 🛠 scikit-learn pipelines

Data preprocessing is something you could already be familiar with. Usually, here is how we go about it:

* **Data Quality Assessment**\
  \- Looking for missing values\
  \- Looking for outliers\
  \- Looking for data type discrepancy in features
* **Data Transformation**\
  \- Feature Selection/Sampling\
  \-Data Aggregation\
  \-Data Normalization
* **Feature Encoding**\
  \-Label Encoding for ordinal variables\
  \-One Hot Encoding for nominal variables
* **Dimensionality Reduction** (PCA, SVD)
* **Feature Scaling**\
  \-Standardization\
  \-Normalization

Although this approach is absolutely correct, if your code looks something like this, give this blog a read till end?

```python
import numpy as np
import sklearn
from sklearn.preprocessing import OneHotEncoder,StandardScaler,SimpleImputer

categorical=list()
numerical=list()

for column in train_data.columns:
  if train_data[column].dtype=='object':
    categorical.append(column)
  elif train_data[column].dtype==np.number:
    numerical.append(column)

imputer=SimpleImputer(strategy='mean')
transformed_data=imputer.fit_transform(train_data[numerical])
transformed_numerical=scaler.fit_transform(train_data[numerical])
encoder=OneHotEncoder()
transformed_categorical=encoder.fit(train_data[categorical])
scaler=StandardScaler()


final_train_data=transformed_categorical+transformed_numericalt
```

Although this approach would work, it is not optimal when dealing with massive datasets or working in a production environment.

You need an orchestrated workflow to go about this. Enter, scikit-learn pipelines. The purpose of a sklearn pipeline is to assemble several steps together.

Imagine a pipeline which finds and separates the numerical & categorical variables , then performs the transformations meant for each data types respectively and then feeds the model the required data for training, validation & testing.

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

**Compose** in scikit-learn (sklearn.compose) helps you design one.

Let’s try it hands-on. Shall we? Consider this [dataset](https://www.kaggle.com/competitions/tabular-playground-series-aug-2022/data) for playing it out.

```python
#Here are all the libraries you will be needing to play this project out

import numpy as np 
import pandas as pd 
import os
import sklearn
from sklearn import preprocessing
from sklearn.pipeline import make_pipeline
from sklearn.impute import SimpleImputer
from sklearn.compose import make_column_selector
from sklearn.compose import make_column_transformer
from sklearn.preprocessing import OrdinalEncoder
from sklearn.preprocessing import OneHotEncoder
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import train_test_split
from sklearn.model_selection import cross_val_score
from sklearn.model_selection import KFold
import time
from sklearn.ensemble import RandomForestClassifier
from sklearn.ensemble import StackingClassifier
from sklearn.svm import LinearSVC
from sklearn.model_selection import GridSearchCV
import warnings
warnings.filterwarnings("ignore")
```

I have kept the code in relevance with kaggle for ease of use

```python
input_paths=list() 
for dirname, _, filenames in os.walk('/kaggle/input'): 
    for filename in filenames: input_paths+=[os.path.join(dirname, filename)] 
        print(os.path.join(dirname, filename))
```

Let's import the train and test data now.

```python
train_data=pd.DataFrame(pd.read_csv(input_paths[1]))
test_data=pd.DataFrame(pd.read_csv(input_paths[2]))
```

We need to observe the type and distribution of our target feature to determine what kind of problem we are dealing with.

```python
target=train_data['failure']
print(target.unique()) 
train_data=train_data.drop(columns=['failure','id'])
```

The output for above code block is ‘0’ & ‘1’. Hence, the target feature has **discrete** values & thus we will be performing **classification**.

After deciding what problem we are working on i.e classification, we begin exploratory data analysis (EDA) by checking for missing values now.

```python
train_data.isna().sum()
```

You will observe that we have a bunch of missing values in the dataset.

(EDA entails observation of features closely with various statistical & visualization methods, but I will be referring to the most known techniques & keep the focus of the blog on designing the pipeline)

Running the below code block will also reveal that we have both numerical (int, float) & categorical (object) features in our dataset.

```python
train_data.info()
```

So, let’s kick-off designing our pipeline, shall we?

The **make\_column\_selector** in sklearn.compose helps in fetching the required columns based on their **data type** or by matching a **regex pattern** with the column names. We will use it to implicitly select numerical & categorical features.

```python
categorical_data = make_column_selector(dtype_include=object)
numerical_data=make_column_selector(dtype_include=np.number)
```

Now, let’s create a transformer pipeline. Remember, our goal is to automate the whole process from pre-processing to prediction. Once you have a pipeline setup ready, you can customize this very setup for all similar requirements. Optimal and efficient, right?

```python
#for categorical transformations
categorical_processor=OneHotEncoder(handle_unknown="ignore") 

#for numerical transformations
numerical_processor=make_pipeline(StandardScaler(), SimpleImputer(strategy="mean", add_indicator=True)) 

#combining the transformations into one common column transformer
transformer=make_column_transformer((numerical_processor,numerical_data),(categorical_processor,categorical_data))
```

Now, let’s add the models to our pipeline. We will be working with Random Forest and Support Vector classifiers.

To include everything in one pipeline at the end, we have to keep including the previously done work to each step. Hence, we add our column transformer first and then the classifier.

```python
rf_pipeline = make_pipeline(transformer, RandomForestClassifier(n_estimators=10, random_state=42))
svc_pipeline = make_pipeline(transformer,LinearSVC(random_state=42))

#Then we group the pipelines in a list

estimators = [
     ('rf', rf_pipeline),
     ('svc', svc_pipeline) 
]
```

We will now add the estimators to a **StackingClassifier.** A stacking classifier, very literally, is just a stack of classifiers. We do this because stacked generalization stacks the output of individual estimator and uses a classifier to compute the final prediction.

```python
sclf = StackingClassifier(estimators=estimators)b
```

Now, let’s do hyperparameter tuning. To get information about what parameters are actually you dealing with in your selected estimators, run this line of code.

```python
sclf.get_params()
```

After going through all the parameters and selecting which ones we want to tune, now we have to figure out the best values for these parameters. We will be using **GridSearchCV** for that. We have to create a parameter grid of selected paramters using a dictionary. Then, after feeding it to the function, the parameters of the estimatos will be optimized by cross-validated grid-search based on the provided parameter grid

```python
param_grid = { 
    'svc__linearsvc__penalty': ['l1','l2'],
    'svc__linearsvc__loss': ['hinge', 'squared_hinge'],
    'svc__linearsvc__max_iter' : [10,100,1000],
    'rf__randomforestclassifier__max_depth' : [3,5,7,9],
}

clf = GridSearchCV(estimator=sclf, param_grid=param_grid, cv= 5)
clf.fit(X_train, Y_train)
print(clf.best_params_)
```

Now, we have the best parameter values. We can hypertune our estimators and get on with the model training.

```python
rf_pipeline = make_pipeline(transformer, RandomForestClassifier(max_depth=3, random_state=42))
svc_pipeline = make_pipeline(transformer,LinearSVC(loss='hinge',max_iter=10,penalty='l2'))
estimators = [
     ('rf', rf_pipeline),
     ('svc', svc_pipeline) 
]

sclf=StackingClassifier(estimators=estimators)
sclf.fit_transform(X_train,Y_train)
```

Now take a second and think about which is our final pipeline. The answer is in the next line. Done?

Our final pipeline is sclf. If you got it right, give yourself a pat. If not, no worries. Just scan through the article once again. Feel free to comment any questions you might have.

So, that was all. Our pipeline is ready. To get predictions, you can just run sclf.predict & also compare the output with evaluation metrics of your liking.

## You are awesome & You got this! <a href="#c3eb" id="c3eb"></a>
