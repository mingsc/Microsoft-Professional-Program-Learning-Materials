# DAT210x: Programming with Python for Data Science

## Course Map
* Big Picture
  * Data Science and Analysis
  * Machine Learning
  * The Possibilities
  * Dive Deeper
* Data and Features
  * Features Premiere
  * Determine Features
  * Manipulate Data
  * Feature Representation
  * Wrangling Data
  * Dive Deeper
*  Exploring Data
  * Visualizations
  * Basic Plots
  * Higher Dimensionality
  * Dive Deeper
* Transforming Data
  * PCA
  * Isomap
  * Pre-processing
  * Dive Deeper
* Data Modeling
  * Clustering
  * Spliting Data
  * K-nearest Neighbors
  * Regression
  * Dive Deeper
* Data Modeling II
  * SVC
  * Decision Trees
  * Random Forest
  * Dive Deeper
* Evaluating Data
  * Confusion
  * Cross Validation
  * Power Tuning
  * Dive Deeper



## Data and Features

### Features Premiere
There are many synonymous names for features. The background of the speaker, as well as the context of the conversation usually dictates which term is used:

* Attribute - Features are a quantitative attributes of the samples being observed
* Axis - Features are orthogonal axes of their feature space, if they are linearly independent
* Column - Features are represented as columns in your dataset
* Dimension - A dataset's features, grouped together can be treated as a n-dimensional coordinate space
* Input - Feature values are the input of data-driven, machine learning algorithms
* Predictor - Features used to predict other attributes are called predictors
* View - Each feature conveys a quantitative trait or perspective about the sample being observed
* Independent Variable - Autonomous features used to calculate others are like independent variables in algebraic equations

### Determine Features
### Manipulate Data
#### A Quick Peek
```python
df.head(10)
df.describe() # give statistical info
df.var() # variance
df.dtypes
```
* numeric-type: int32, float32, float64.
* date-type: datetime64, timedelta[ns].
* object-type: object (string), category

#### Column Indexing
One difference you'll notice is that some of the methods take in a list of parameters, e.g.: df[['recency']], df.loc[:, ['recency']], and df.iloc[:, [0]]. By passing in a list of parameters, you can select more than one column to slice. Please be aware that if you use this syntax, even if you only specify a single column, the data type that you'll get back is a dataframe as opposed to a series. This will be useful for you to know once you start machine learning, so be sure to take down that note.
```python

#
# Produces a series object:
>>> df.recency
>>> df['recency']
>>> df.loc[:, 'recency']
>>> df.iloc[:, 0]
>>> df.ix[:, 0]

0        10
1         6
2         7
3         9
4         2
5         6
Name: recency, dtype: int64

#
# Produces a dataframe object:
>>> df[['recency']]
>>> df.loc[:, ['recency']]
>>> df.iloc[:, [0]]

       recency
0           10
1            6
2            7
3            9
4            2
5            6

[64000 rows x 1 columns]

>>> df[0:2]
>>> df.iloc[0:2, :]
```
#### Boolean Indexing
```python
>>> df.recency < 7

0        False
1         True
2        False
3        False
4         True
5         True

Name: recency, dtype: bool

>>> df[ df.recency < 7 ]

       recency   history_segment  history  mens  womens   zip_code  newbie
1            6    3) $200 - $350   329.08     1       1      Rural       1
4            2      1) $0 - $100    45.34     1       0      Urban       0
5            6    2) $100 - $200   134.83     0       1  Surburban       0

>>> df[ (df.recency < 7) & (df.newbie == 0) ]

       recency history_segment  history  mens  womens   zip_code  newbie
4            2    1) $0 - $100    45.34     1       0      Urban       0
5            6  2) $100 - $200   134.83     0       1  Surburban       0
```

### Feature Representation

#### Textual Categorical-Features
If you have a categorical feature, the way to represent it in your dataset depends on if it's ordinal or nominal. For **ordinal features**, map the order as increasing integers in a single numeric feature. 
```python
>>> ordered_satisfaction = ['Very Unhappy', 'Unhappy', 'Neutral', 'Happy', 'Very Happy']
>>> df = pd.DataFrame({'satisfaction':['Mad', 'Happy', 'Unhappy', 'Neutral']})
>>> df.satisfaction = df.satisfaction.astype("category",
  ordered=True,
  categories=ordered_satisfaction
).cat.codes

>>> df
   satisfaction
0            -1
1             3
2             1
3             2
```
On the other hand, if your feature is **nominal** (and thus there is no obvious numeric ordering), then you have two options.
```python
# Data Source
>>> df = pd.DataFrame({'vertebrates':[
...  'Bird',
...  'Bird',
...  'Mammal',
...  'Fish',
...  'Amphibian',
...  'Reptile',
...  'Mammal',
... ]})


# Method 1)
>>> df['vertebrates'] = df.vertebrates.astype("category").cat.codes

>>> df
  vertebrates  vertebrates
0        Bird            1
1        Bird            1
2      Mammal            3
3        Fish            2
4   Amphibian            0
5     Reptile            4
6      Mammal            3

# Method 2)

>>> df = pd.get_dummies(df,columns=['vertebrates'])

>>> df
   vertebrates_Amphibian  vertebrates_Bird  vertebrates_Fish  \
0                    0.0               1.0               0.0   
1                    0.0               1.0               0.0   
2                    0.0               0.0               0.0   
3                    0.0               0.0               1.0   
4                    1.0               0.0               0.0   
5                    0.0               0.0               0.0   
6                    0.0               0.0               0.0   

   vertebrates_Mammal  vertebrates_Reptile  
0                 0.0                  0.0  
1                 0.0                  0.0  
2                 1.0                  0.0  
3                 0.0                  0.0  
4                 0.0                  0.0  
5                 0.0                  1.0  
6                 1.0                  0.0  
```
### NLP Tokenization
```python
>>> from sklearn.feature_extraction.text import CountVectorizer

>>> corpus = [
...  "Authman ran faster than Harry because he is an athlete.",
...  "Authman and Harry ran faster and faster.",
... ]

>>> bow = CountVectorizer()
>>> X = bow.fit_transform(corpus) # Sparse Matrix

>>> bow.get_feature_names()
['an', 'and', 'athlete', 'authman', 'because', 'faster', 'harry', 'he', 'is', 'ran', 'than']

>>> X.toarray()
[[1 0 1 1 1 1 1 1 1 1 1]
 [0 2 0 1 0 2 1 0 0 1 0]]
 ```
#### Graphical Features
 ```python
 # Uses the Image module (PIL)
from scipy import misc

# Load the image up
img = misc.imread('image.png')

# Is the image too big? Resample it down by an order of magnitude
img = img[::2, ::2]

# Scale colors from (0-255) to (0-1), then reshape to 1D array per pixel, e.g. grayscale
# If you had color images and wanted to preserve all color channels, use .reshape(-1,3)
X = (img / 255.0).reshape(-1)

# To-Do: Machine Learning with X!
#
```

#### Audio Features
```python
import scipy.io.wavfile as wavfile

sample_rate, audio_data = wavfile.read('sound.wav')
print audio_data

# To-Do: Machine Learning with audio_data!
#
```
### Wrangling Data

#### fill Nans
```pyhthon
df.my_feature.fillna( df.my_feature.mean() )
df.fillna(0)
df.fillna(method='ffill')  # fill the values forward
df.fillna(method='bfill')  # fill the values in reverse
df.fillna(limit=5)
df.interpolate(method='polynomial', order=2)
```
#### Drop Data
```python
# Dropna()
df = df.dropna(axis=0)  # remove any row with nans
df = df.dropna(axis=0, thresh=4) # Drop any row that has at least 4 NON-NaNs within it

# Drop row/column(s)
df = df.drop(labels=['Features', 'To', 'Delete'], axis=1) # Axis=1 for columns

# Drop Duplicates
df = df.drop_duplicates(subset=['Feature_1', 'Feature_2'])
df = df.reset_index(drop=True)

# Chain them together
df = df.dropna(axis=0, thresh=2).drop(labels=['ColA', axis=1]).drop_duplicates(subset=['ColB', 'ColC']).reset_index()
```
However there may be times where you want these operations to work in-place on the dataframe calling them, rather than returning a new dataframe. Pass **inplace=True** as a parameter to any of the above methods to get that working.

#### Change data types
If your data types don't look the way you expected them, explicitly convert them to the desired type using the .to_datetime(), .to_numeric(), and .to_timedelta() methods:
```python
>>> df.Date = pd.to_datetime(df.Date, errors='coerce')
array([7, 33, 27, 40, 22], dtype=int64)
>>> df.Height = pd.to_numeric(df.Height, errors='coerce')
7      1
22     5
27     1
33     2
40     2
dtype: int64

df[['height']] =df[['height']].astype("float64")
df[['height']] =df[['height']].astype("int64")
```
The **errors='coerce'** parameter instructs Pandas to enter a NaN at any field where the conversion fails.

#### Check data constitution
```python
>>> df.Age.unique()
>>> df.Age.value_counts()
```

### [Dive Deeper](https://courses.edx.org/courses/course-v1:Microsoft+DAT210x+6T2016/courseware/12621a4064aa4d92874a9d8a953734c5/e43e044ae9c045298766ece7d3881386/)



## Exploring Data
### Basic Plots
#### Histogram Plots
```python
import pandas as pd
import matplotlib
matplotlib.style.use('ggplot') # Look Pretty
# If the above line throws an error, use plt.style.use('ggplot') instead

student_dataset = pd.read_csv("/Datasets/students.data", index_col=0)

my_series = student_dataset.G3
my_dataframe = student_dataset[['G3', 'G2', 'G1']] 

my_series.plot.hist(alpha=0.5)
my_dataframe.plot.hist(alpha=0.5)
```

#### Scatter Plot
```python
import pandas as pd
import matplotlib

matplotlib.style.use('ggplot') # Look Pretty
# If the above line throws an error, use plt.style.use('ggplot') instead

student_dataset = pd.read_csv("/Datasets/students.data", index_col=0) 
student_dataset.plot.scatter(x='G1', y='G3')
```

#### 3D plot
```python
import matplotlib
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D

import pandas as pd
matplotlib.style.use('ggplot') # Look Pretty
# If the above line throws an error, use plt.style.use('ggplot') instead

student_dataset = pd.read_csv("/Datasets/students.data", index_col=0)

fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')
ax.set_xlabel('Final Grade')
ax.set_ylabel('First Grade')
ax.set_zlabel('Daily Alcohol')

ax.scatter(student_dataset.G1, student_dataset.G3, student_dataset['Dalc'], c='r', marker='.')
plt.show()
```


### Higher Dimensionality
#### Parallel Coordinates
Parallel coordinates are useful because polylines belonging to similar records tend to cluster together.
To graph them with Pandas and MatPlotLib, you have to specify a feature to group by (it can be non-numeric) - like legend
```python
from sklearn.datasets import load_iris
from pandas.tools.plotting import parallel_coordinates

import pandas as pd
import matplotlib.pyplot as plt
import matplotlib

# Look pretty...
matplotlib.style.use('ggplot')
# If the above line throws an error, use plt.style.use('ggplot') instead

# Load up SKLearn's Iris Dataset into a Pandas Dataframe
data = load_iris()
df = pd.DataFrame(data.data, columns=data.feature_names) 

df['target_names'] = [data.target_names[i] for i in data.target]

# Parallel Coordinates Start Here:
plt.figure()
parallel_coordinates(df, 'target_names')
plt.show()
```
Pandas' parallel coordinates interface is extremely easy to use, but use it with care. It only supports a **single scale** for all your axes. If you have some features that are on a small scale and others on a large scale, you'll have to deal with a compressed plot. For now, your only three options are to:
* **Normalize** your features before charting them
* Change the scale to a **log scale**
* Or create **separate, multiple parallel coordinate charts**. Each one only plotting features with similar domains scales plotted

#### Andrew's Curves
An Andrews plot, also known as Andrews curve, helps you visualize higher dimensionality, multivariate data by plotting each of your dataset's observations as a curve. The feature values of the observation act as the coefficients of the curve, so observations with similar characteristics tend to group closer to each other. Due to this, Andrews curves have some use in outlier detection.
```python
from sklearn.datasets import load_iris
from pandas.tools.plotting import andrews_curves

import pandas as pd
import matplotlib.pyplot as plt
import matplotlib

# Look pretty...
matplotlib.style.use('ggplot')
# If the above line throws an error, use plt.style.use('ggplot') instead

# Load up SKLearn's Iris Dataset into a Pandas Dataframe
data = load_iris()
df = pd.DataFrame(data.data, columns=data.feature_names)
df['target_names'] = [data.target_names[i] for i in data.target]

# Andrews Curves Start Here:
plt.figure()
andrews_curves(df, 'target_names')
plt.show()
```
One of the current weaknesses with the Pandas implementation (and this goes for Parallel Coordinates as well) is that every single observation is charted. In the MATLAB version, you can specify a quantile or probability distribution cutoff. 

#### Imshow and Corr
.imshow() generates an image based off of the normalized values stored in a matrix, or rectangular array of float64s. The properties of the generated image will depend on the dimensions and contents of the array passed in:
* An [X, Y] shaped array will result in a grayscale image being generated
* A [X, Y, 3] shaped array results in a full-color image: 1 channel for red, 1 for green, and 1 for blue
* A [X, Y, 4] shaped array results in a full-color image as before with an extra channel for alpha

Besides being a **straightforward way to display .PNG and other images**, the .imshow() method has quite a few other use cases. When you use the .corr() method on your dataset, Pandas calculates a correlation matrix for you that measures how close to being linear the relationship between any two features in your dataset are.
```python 
>>> df = pd.DataFrame(np.random.randn(1000, 5), columns=['a', 'b', 'c', 'd', 'e'])
>>> df.corr()

          a         b         c         d         e
a  1.000000  0.007568  0.014746  0.027275 -0.029043
b  0.007568  1.000000 -0.039130 -0.011612  0.082062
c  0.014746 -0.039130  1.000000  0.025330 -0.028471
d  0.027275 -0.011612  0.025330  1.000000 -0.002215
e -0.029043  0.082062 -0.028471 -0.002215  1.000000

import matplotlib.pyplot as plt

plt.imshow(df.corr(), cmap=plt.cm.Blues, interpolation='nearest')
plt.colorbar()
tick_marks = [i for i in range(len(df.columns))]
plt.xticks(tick_marks, df.columns, rotation='vertical')
plt.yticks(tick_marks, df.columns)

plt.show()
```

### [Dive Deeper](https://courses.edx.org/courses/course-v1:Microsoft+DAT210x+6T2016/courseware/dfb4bc13bab749ceb87191a9185cb111/94a6eca559df4cd482e379d5e9bcbad7/)


## Transforming Data

### Principal Component Analysis (PCA)
Principal Component Analysis (PCA), a transformation that attempts to convert your possibly correlated features into a set of linearly uncorrelated ones, is the first unsupervised learning algorithm you'll study.

PCA falls into the group of **dimensionality reduction algorithms**. In many real-world datasets and the problems they represent, you aren't aware of what specifically needs to be measured to succinctly address the issue driving your data collection.
If you have reason to believe your question has a simple answer, or that the features you've collected are actually many indirect observations of some inherent source you either cannot or do not know how to measure, then dimensionality reduction applies to your needs.
PCA's approach to dimensionality reduction is to derive a set of degrees of freedom that can then be used to reproduce most of the variability of your data. It accesses your dataset's **covariance** structure directly using matrix calculations and eigenvectors to compute **the best unique features** that describe your samples.

PCA ensures that each newly computed view (feature) is **orthogonal or linearly independent to all previously computed ones**, minimizing these overlaps. PCA also orders the features by importance, assuming that the **more variance expressed in a feature, the more important it is**.

![pic_example_PCA](http://courses.edx.org/asset-v1:Microsoft+DAT210x+4T2016+type@asset+block@PCA1.jpg)

```python
>>> from sklearn.decomposition import PCA
>>> pca = PCA(n_components=2)
>>> pca.fit(df)
PCA(copy=True, n_components=2, whiten=False)

>>> T = pca.transform(df)

>>> df.shape
(430, 6) # 430 Student survey responses, 6 questions..
>>> T.shape
(430, 2) # 430 Student survey responses, 2 principal components.
```
you can recover your original feature values using **.inverse_transform()** so long as you don't drop any components. 
a few other interesting model attribute with the **.fit()** method:
* components_ These are your principal component vectors and are linear combinations of your original features. As such, they exist within the feature space of your original dataset.
* explained_variance_ This is the calculated amount of variance which exists in the newly computed principal components.
* explained_variance_ratio_ Normalized version of explained_variance_ for when your interest is with probabilities.

Some other things to pay attention:
* PCA is sensitive to the scaling of features. Unless need to respect the specific scaling, should always **standardize** first.
* *RandomizedPCA* is a faster training process with accuracy a bit affected. Will be useful for very large dataset.
* PCA is a **linear transformation** only, therefore cannot discern any complex, nonlinear intricacies. For such cases, you will have to make use different dimensionality reduction algorithms, such as Isomap.

### Isomap
Its goal: to uncover the intrinsic, geometric-nature of your dataset, as opposed to simply capturing your datasets most variant directions.
Isomap operates by first computing each record's nearest neighbors. Only a sample's K-nearest samples qualify for being included in its nearest-neighborhood samples list. A neighborhood graph is then constructed by linking each sample to its K-nearest neighbors.
Just as you would drive waypoint to waypoint in order to navigate to a final destination, so too does Isomap travel from sample to sample, taking the shortest neighborhood paths between any two distant samples in your dataset. The straightforward, e.g. high-dimensional, direct Euclidean distance between any two records fails to properly account for any intrinsic, nonlinear geometry present within your dataset's features.

![example_pic_isomap](http://courses.edx.org/asset-v1:Microsoft+DAT210x+4T2016+type@asset+block@isomap_replace.png)


Isomap is better than linear methods when dealing with almost all types of real image and motion tracking. If you're dataset comes from images captured in a natural way, or your data is characterized by similar types of motions, consider using isomap. 
```python
>>> from sklearn import manifold
>>> iso = manifold.Isomap(n_neighbors=4, n_components=2)
>>> iso.fit(df)
Isomap(eigen_solver='auto', max_iter=None, n_components=2, n_neighbors=4,
    neighbors_algorithm='auto', path_method='auto', tol=0)
>>> manifold = iso.transform(df)

>>> df.shape
(430, 6)
>>> manifold.shape
(430, 2)
```
Isomap is also a bit more **sensitive to noise** than PCA. Noisy data can actually act as a conduit to short-circuit the nearest neighborhood map, cause isomap to prefer the 'noisy' but shorter path between samples that lie on the real geodesic surface of your data that would otherwise be well separated.

When using unsupervised dimensionality reduction techniques, be sure to use the feature scaling on all of your features because the nearest-neighbor search that Isomap bases your manifold on will do poorly if you don't, and PCA will prefer features with larger variances. As explained within the labs' source code, **SciKit-Learn's StandardScaler** is a good-fit for taking care of  scaling your data before performing dimensionality reduction.


### Pre-processing
For a great overview on a few of the normalization methods supported in SKLearn, please check out: [link]( https://stackoverflow.com/questions/30918781/right-function-for-normalizing-input-of-sklearn-svm)

#### StandardScaler
It assumes that your features are normally distributed (each feature with a different mean and standard deviation), and scales them such that each feature's Gaussian distribution is now centered around 0 and it's standard deviation is 1.
#### Normalizer
It looks at all the feature values for a given data point as a vector and normalizes that vector by dividing it by it's magnitude. For example, let's say you have 3 features. The values for a specific point are [x1, x2, x3]. If you're using the default 'l2' normalization, you divide each value by sqrt(x1^2 + x2^2 + x3^2). If you're using 'l1' normalization, you divide each by x1+x2+x3.
#### MinMaxScaler
For each feature, this looks at the minimum and maximum value. This is the range of this feature. Then it shrinks or stretches this to the same range for each feature (the default is 0 to 1).
```python
from sklearn import preprocessing

T = preprocessing.StandardScaler().fit_transform(df)
T = preprocessing.MinMaxScaler().fit_transform(df)
T = preprocessing.MaxAbsScaler().fit_transform(df)
T = preprocessing.Normalizer().fit_transform(df)
```


### [Dive Deeper](https://courses.edx.org/courses/course-v1:Microsoft+DAT210x+6T2016/courseware/1aabc1638cb64c699ddc447d16e3cfea/d2dd5efbb36c4632af42178081be1c71/?child=first)



## Data Modeling

### Clustering - KMeans
```python
>>> from sklearn.cluster import KMeans
>>> kmeans = KMeans(n_clusters=5)
>>> kmeans.fit(df)
KMeans(copy_x=True, init='k-means++', max_iter=300, n_clusters=5, n_init=10,
    n_jobs=1, precompute_distances='auto', random_state=None, tol=0.0001,
    verbose=0)

>>> labels = kmeans.predict(df)
>>> centroids = kmeans.cluster_centers_


fig = plt.figure()
ax = fig.add_subplot(111)
ax.scatter(df.Longitude, df.Latitude, marker='.', alpha=0.3)

kmeans_model = KMeans(n_clusters = 7)
kmeans_model.fit(df)

centroids = kmeans_model.cluster_centers_
ax.scatter(centroids[:,0], centroids[:,1], marker='x', c='red', alpha=0.5, linewidths=3, s=169)
print centroids
plt.show()
```
**Two** other key characteristics of K-Means are that it assumes your samples are **length normalized**, and as such, is sensitive to feature scaling. It also assumes that the cluster sizes are **roughly spherical and similar**; this way, the nearest centroid is always the correct assignment.

[An example for visualization of PCA in K-means](https://github.com/yang0339/Microsoft-Professional-Program-Learning-Materials/blob/master/DAT210x%20Programming%20with%20Python%20for%20Data%20Science/kmeans_PCA.py), and [the data source file](https://github.com/yang0339/Microsoft-Professional-Program-Learning-Materials/blob/master/DAT210x%20Programming%20with%20Python%20for%20Data%20Science/Wholesale%20customers%20data.csv)

![pic_demo](https://github.com/yang0339/Microsoft-Professional-Program-Learning-Materials/blob/master/DAT210x%20Programming%20with%20Python%20for%20Data%20Science/kmeans_PCA_demo.png)

### Spliting Data
A short summary and outlook:
Every single machine learning class, or estimator as SciKit-Learn call them, implements the .fit() method as you've seen. This will continue to hold true for the supervised one's as well. The unsupervised estimators also allowed you to make use of the following methods:
* .transform() : without changing the number of samples, alters the value of each existing feature by changing its units
* .predict() : only with clustering, you could predict the label of the specified sample.

What you'll now see is that supervised learning estimators implement a slightly different set of distinct methods:
* .predict() : After training your machine learning model, you can predict the labels of new and never seen samples
* .predict_proba() : For some estimators, you can further see what the probability of the new sample belonging to each label is
* .score(): The ability to score how well your model fit the training data

```python
>>> from sklearn.model_selection import train_test_split
>>> data_train, data_test, label_train, label_test = train_test_split(data, labels, test_size=0.5, random_state=7)

from sklearn.metrics import accuracy_score

# Returns an array of predictions:
>>> predictions = my_model.predict(data_test) 
>>> predictions
[0, 0, 0, 1, 0]

# The actual answers:
>>> label_train
[1, 1, 0, 1, 0]

>>> accuracy_score(label_train, predictions)
0.59999999999999998

>>> accuracy_score(label_train, predictions, normalize=False)
3
```


### Classification - K-nearest Neighbors
A basic streamline of any supervised learning:
```python
# Process:
# Load a dataset into a dataframe
X = pd.read_csv('data.set', index_col=0)

# Do basic wrangling, but no transformations
# ...

# Immediately copy out the classification / label / class / answer column
y = X['classification'].copy()
X.drop(labels=['classification'], inplace=True, axis=1)

# split data
# ex:
from sklearn import cross_validation
data_train, data_test, label_train, label_test = cross_validation.train_test_split(X, y, test_size=0.33, random_state=1)


# Feature scaling as necessary
# ex: 
from sklearn import preprocessing
preprocessing.Normalizer().fit(data_train)
data_train = preprocessing.Normalizer().transform(data_train)
data_test = preprocessing.Normalizer().transform(data_test)

# Transformation
# ex: 
from sklearn.decomposition import PCA
pca = PCA(n_components=2)
pca = pca.fit(data_train)
data_train = pca.transform(data_train)
data_test = pca.transform(data_test)


# Machine Learning
# ex:
from sklearn.neighbors import KNeighborsClassifier
knn = KNeighborsClassifier(n_neighbors=5)
knn.fit(data_train, label_train) 

# Evaluation
# ex:
knn.score(data_test, label_test)
```
K-nearest Neighbors code:
```python
>>> from sklearn.neighbors import KNeighborsClassifier
>>> model = KNeighborsClassifier(n_neighbors=3)
>>> model.fit(X_train, y_train) 
KNeighborsClassifier(...)

>>> # You can pass in a dframe or an ndarray
>>> model.predict([[1.1]])

>>> model.predict_proba([[0.9]])
[[ 0.66666667  0.33333333]
```
Something to note:
As with all algorithms dependent on distance measures, it is also sensitive to feature scaling. K-Neighbors is also sensitive to perturbations and the local structure of your dataset, particularly at lower "K" values.
with large "K" values, you have to be more cautious of the overall class distribution of your samples. If 30% of your dataset is labeled A and 70% of labeled B, with high enough "K" values, you might experience K-Neighbors unjustly giving preference to B labeling, even in those localities of your dataset that should be properly classified as A.

#### A demo:
[source code](https://github.com/yang0339/Microsoft-Professional-Program-Learning-Materials/blob/master/DAT210x%20Programming%20with%20Python%20for%20Data%20Science/knn.py) and [data file](https://github.com/yang0339/Microsoft-Professional-Program-Learning-Materials/blob/master/DAT210x%20Programming%20with%20Python%20for%20Data%20Science/wheat.data)
![knn_visualization](https://github.com/yang0339/Microsoft-Professional-Program-Learning-Materials/blob/master/DAT210x%20Programming%20with%20Python%20for%20Data%20Science/knn.png)


### Regression
```python
>>> from sklearn import linear_model
>>> model = linear_model.LinearRegression()
>>> model.fit(X_train, y_train)

LinearRegression(copy_X=True, fit_intercept=True, n_jobs=1, normalize=False)

>>> # R2 Score
>>> model.score(X_test, y_test)
153.244939109

>>> # Sum of Squared Distances
>>> np.sum(model.predict(X_test) - y_test) ** 2)
5465.15
```
As for its outputs, the attributes you're interested in are:
* intercept_ the scalar constant offset value
* coef_ an array of weights, one per input feature, which will act as a scaling factor

### [Dive Deeper](https://courses.edx.org/courses/course-v1:Microsoft+DAT210x+6T2016/courseware/57010625975a485ab1acb37592255b3f/83d48871f944443592c39dfb5326fc78/)



## Data Modeling II
### SVC
A visualization of SVM's kernel trick: what does higher-dimension mean?
![Animation on SVM kernel trick](https://courses.edx.org/asset-v1:Microsoft+DAT210x+4T2016+type@asset+block@svc_visualization.gif)

One of the advantages of SVC is that once you've done the hard work of finding the hyperplane and its supporting vectors, the real job of classifying your samples is as simple as answering what side of the line is the point on? This makes SVC a classifier of choice for problems where **classification speed is more critical than training speed**.

There may be cases where **number your dataset has more features than the number of samples**. Not all machine learning algorithms will be able to work with that, however such datasets aren't an issue for SVC. In fact, at least conceptually, if you use the kernel trick then at some point your data will almost assuredly be at a higher dimensionality than the number of features, depending on which kernel you use. And with the ability to use different kernel functions or even define your own, SVC will prove to be a very versatile classifier for you to have down in your machine learning arsenal.

Lastly, SVC is **non-probabilistic**. That means the resulting classification is calculated based off of the geometry of your dataset, as opposed to probabilities of occurrences. Once you get to decision trees, you'll see an example of a classifier that works using probabilities and not the geometric nature of your dataset.

```python
>>> from sklearn.svm import SVC
>>> model = SVC(kernel='linear')
>>> model.fit(X, y) 
SVC(C=1.0, cache_size=200, class_weight=None, coef0=0.0,
  decision_function_shape=None, degree=3, gamma='auto', kernel='linear',
  max_iter=-1, probability=False, random_state=None, shrinking=True,
  tol=0.001, verbose=False)
```
Of the many configurable parameters for SciKit-Learn's svm.SVC class, the most important three in order are:
* **kernel** Defines the type of kernel used with your classifier. The default is the radial basis function rbf, the most popular kernel used with support vector machines generally. SciKit-Learn also supports linear, poly, sigmoid, and precomputed kernels. You can also specify a user defined function to pre-compute the kernel matrix from your sample's feature space, which should be shaped [n_samples, n_samples].
* **C** This is the penalty parameter for the error term. Do you want your SVC to never miss a single classification? Or is having a more generalized solution important to you? The lower your C value, the smoother and more generalized your decision boundary is going to be. But if you have a large C value, the classifier will attempt to do whatever is in its power to squiggle and wiggle between each sample to correctly classify it.
* **gamma** This parameter's value is inversely proportional to the extent a single training sample's influence extends. Large gamma values result in each training sample having localized effects only. Smaller values result in each sample affecting a larger area. In essence, the gamma values dictate how pronounced your decision boundary is by varying the influence of your support vector samples.
* **random_state** SVC and support vector machines are theoretically a deterministic algorithm, meaning if you re-run it against the same input, it should produce identical output each time. However SKLearn's SVC via libsvc implementation randomly shuffles your data during its probability estimation step. So to truly get deterministic execution, set a state seed.

In terms of attributes, a few goodies are exposed here to:
* support_ Contains an array of the indices belonging to the selected support vectors
* support_vectors_ The actual samples chosen as the support vectors
* intercept_ The constants of the decision function
* dual_coef_ Each support vector's contribution to the decision function, on a per classification basis. This has similarities to the weights of linear regression

An [example](https://github.com/yang0339/Microsoft-Professional-Program-Learning-Materials/blob/master/DAT210x%20Programming%20with%20Python%20for%20Data%20Science/KNeightbor-SVC.py) on comparison of K-Neighbors and SVC, and data source file: [wheat.data](https://github.com/yang0339/Microsoft-Professional-Program-Learning-Materials/blob/master/DAT210x%20Programming%20with%20Python%20for%20Data%20Science/wheat.data)
![svc-kneightbor visualization](https://github.com/yang0339/Microsoft-Professional-Program-Learning-Materials/blob/master/DAT210x%20Programming%20with%20Python%20for%20Data%20Science/KNeightbor-SVC.jpg)

### Decision Trees
```python
>>> from sklearn import tree
>>> model = tree.DecisionTreeClassifier(max_depth=9, criterion="entropy")
>>> model.fit(X,y)
DecisionTreeClassifier(class_weight=None, criterion='entropy', max_depth=9,
            max_features=None, max_leaf_nodes=None, min_samples_leaf=1,
            min_samples_split=2, min_weight_fraction_leaf=0.0,
            presort=False, random_state=None, splitter='best')

# .DOT files can be rendered to .PNGs, if you've already `brew install graphviz`.
>>> tree.export_graphviz(model.tree_, out_file='tree.dot', feature_names=X.columns)

>>> from subprocess import call
>>> call(['dot', '-T', 'png', 'tree.dot', '-o', 'tree.png'])
```
* **criterion** By default, SciKit-Learn uses Gini, which is an impurity rating. Alternatively, you could also make use of information gain, or entropy instead.
* **splitter** Lets you control of the algorithm chooses the best split or not. We'll discuss why that's importance once you move to random forest classifier.
* **max_features** One of the possible splitter options for splitter above is called 'best'. SciKit-Learn runs a bunch of tests on your features to figure out which mechanism should be used when searching for the best split. This parameter limits the number of features to consider while doing this.
* get back a **feature_importances** vector that stores, in order of importance, the features that used to make the labeling decisions of your tree.

### Random Forest
Some of the advantages decision trees offer include:

* Unlike SVMs, the accuracy of a DTree doesn't decrease when you include irrelevant features
* Unlike KNeighbors, both training and predicting with a DTree are relatively fast operations
* Unlike PCA / IsoMap, DTrees are invariant to monotonic feature scaling and transformations
* Moreover, a trained DTree model is readily human inspectable

```python
>>> from sklearn.ensemble import RandomForestClassifier
>>> model = RandomForestClassifier(n_estimators=10, oob_score=True)
>>> model.fit(X, y)
RandomForestClassifier(bootstrap=True, class_weight=None, criterion='gini',
            max_depth=None, max_features='auto', max_leaf_nodes=None,
            min_samples_leaf=1, min_samples_split=2,
            min_weight_fraction_leaf=0.0, n_estimators=10, n_jobs=1,
            oob_score=True, random_state=None, verbose=0, warm_start=False)
            
>>> print model.oob_score_
0.789925345
```
Some of the new, optional parameters you can pass in while instantiating your model include:

* **n_estimators** Controls the density of the forest ensemble.
* **bootstrap** Also known as bagging. Every trained tree is grown using an independently drawn subset of your input data. As such, training samples not used for training an individual tree are considered out-of-bag for that one tree.
* **obb_score** Controls whether to use out-of-bag samples to estimate a generalization error. By default, this is turned off, with the assumption that you'll be using random forest just like any other SciKit-Learn estimator, and handling the splitting of your training/testing data manually, along with its scoring.


### [Dive Deeper](https://courses.edx.org/courses/course-v1:Microsoft+DAT210x+6T2016/courseware/a936135a7c484ad7a1e04da1280c13c1/1521c056ae8e4245866506e0b1dcad07/)



## Evaluating Data


### Three CheatSheets on Machine Learning
[microsoft-machine-learning-algorithm-cheat-sheet](https://github.com/yang0339/Microsoft-Professional-Program-Learning-Materials/blob/master/DAT210x%20Programming%20with%20Python%20for%20Data%20Science/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)
[SKLEARN cheatsheet](http://scikit-learn.org/stable/tutorial/machine_learning_map/index.html)
[Emanuel Ferm Cheat Sheet](http://eferm.com/wp-content/uploads/2011/05/cheat3.pdf)

### Confusion Matrix
```python
>>> import sklearn.metrics as metrics
>>> y_true = [1, 1, 2, 2, 3, 3]  # Actual, observed testing dataset values
>>> y_pred = [1, 1, 1, 3, 2, 3]  # Predicted values from your model

>>> metrics.confusion_matrix(y_true, y_pred)
array([[2, 0, 0],
       [1, 0, 1],
       [0, 1, 1]])

# One last way you can look at this data is through using an .imshow() visualization plot:
>>> import matplotlib.pyplot as plt

>>> columns = ['Cat', 'Dog', 'Monkey']
>>> confusion = metrics.confusion_matrix(y_true, y_pred)

>>> plt.imshow(confusion, cmap=plt.cm.Blues, interpolation='nearest')
>>> plt.xticks([0,1,2], columns, rotation='vertical')
>>> plt.yticks([0,1,2], columns)
>>> plt.colorbar()

>>> plt.show()
```
--- | Predicted Cat |	Predicted Dog |	Predicted Monkey
--- | --- | --- | --- 
Actual Cat |	2 |	0 |	0
Actual Dog	| 1 |	0 |	1
Actual Monkey |	0 |	1 |	1

![a demo on confusion matrix visualization](https://courses.edx.org/asset-v1:Microsoft+DAT210x+4T2016+type@asset+block@confusion.png)

### Cross Validation

#### Scoring Metrics
```python
>>> import sklearn.metrics as metrics
>>> y_true = [1, 1, 2, 2, 3, 3]  # Actual, observed testing datset values
>>> y_pred = [1, 1, 1, 3, 2, 3]  # Predicted values from your model

>>> metrics.accuracy_score(y_true, y_pred)
0.5
>>> metrics.accuracy_score(y_true, y_pred, normalize=False)
3

# Recall
>>> metrics.recall_score(y_true, y_pred, average='weighted')
0.5
>>> metrics.recall_score(y_true, y_pred, average=None)
array([ 1. ,  0. ,  0.5])

# Precision
>>> metrics.precision_score(y_true, y_pred, average='weighted')
0.38888888888888884
>>> metrics.precision_score(y_true, y_pred, average=None)
array([ 0.66666667,  0.        ,  0.5       ])

# F1
>>> metrics.f1_score(y_true, y_pred, average='weighted')
0.43333333333333335
>>> metrics.f1_score(y_true, y_pred, average=None)
array([ 0.8,  0. ,  0.5])

# Full Report
>>> target_names = ['Fruit 1', 'Fruit 2', 'Fruit 3']
>>> metrics.classification_report(y_true, y_pred, target_names=target_names)


             precision    recall  f1-score   support

    Fruit 1       0.67      1.00      0.80         2
    Fruit 2       0.00      0.00      0.00         2
    Fruit 3       0.50      0.50      0.50         2

avg / total       0.39      0.50      0.43         6
```

#### Cross Validation
```python
>>> from sklearn.cross_validation import train_test_split
>>> X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.5, random_state=0)

>>> # Test how well your model can recall its training data:
>>> model.fit(X_train, y_train).score(X_train, y_train)
0.943262278808

>>> # Test how well your model can predict unseen data:
>>> model.fit(X_test, y_test).score(X_test, y_test)
0.894716422024


# 10-Fold Cross Validation on your training data
>>> from sklearn import cross_validation as cval
>>> cval.cross_val_score(model, X_train, y_train, cv=10)
array([ 0.93513514,  0.99453552,  0.97237569,  0.98888889,  0.96089385,
        0.98882682,  0.99441341,  0.98876404,  0.97175141,  0.96590909])

>>> cval.cross_val_score(model, X_train, y_train, cv=10).mean()
0.97614938602520218
```
In the wild, the **best process** to use depending on how many samples you have at your disposal and the machine learning algorithms you are using, is either of the following:

1. Split your data into **training, validation, and testing sets**.
2. Setup a pipeline, and fit it with your **training set**
3. Access the accuracy of its output using your **validation set**
4. Fine tune this accuracy by **adjusting the hyperparamters** of your pipeline
5. when you're comfortable with its accuracy, finally evaluate your pipeline with the **testing set**


### Power Tuning
SciKit-Learn offers you a systematic way of doing that automatically called **GridSearchCV**. When you were using the nested for-loop approach, we mentioned that was a very naive method. GridSearchCV takes care of your parameter tuning and also tacks on end-to-end cross validation. This results in more precisely tuned parameter than depending on simple model accuracy scores, and is why the algorithm is name Grid-Search-CV.

```python
>>> from sklearn import svm, grid_search, datasets

>>> iris = datasets.load_iris()
>>> parameters = {'kernel':('linear', 'rbf'), 'C':[1, 5, 10]}
>>> model = svm.SVC()

>>> classifier = grid_search.GridSearchCV(model, parameters)
>>> classifier.fit(iris.data, iris.target)
GridSearchCV(cv=None, error_score='raise',
       estimator=SVC(C=1.0, cache_size=200, class_weight=None, coef0=0.0,
  decision_function_shape=None, degree=3, gamma='auto', kernel='rbf',
  max_iter=-1, probability=False, random_state=None, shrinking=True,
  tol=0.001, verbose=False),
       fit_params={}, iid=True, n_jobs=1,
       param_grid={'kernel': ('linear', 'rbf'), 'C': [1, 5, 10]},
       pre_dispatch='2*n_jobs', refit=True, scoring=None, verbose=0)
```

In addition to explicitly defining the parameters you want tested, you can also use randomized parameter optimization with SciKit-Learn's **RandomizedSearchCV** class. The semantics are a bit different here. First, instead of passing a list of grid objects (with GridSearchCV, you can actually perform multiple grid optimizations, consecutively), this time you pass in a your parameters as a single dictionary that holds either possible, discrete parameter values or distribution over them. [SciPy's Statistics module](https://docs.scipy.org/doc/scipy/reference/stats.html) have many such functions you can use to create continuous, discrete, and multivariate type distributions, such as expon, gamma, uniform, randin and many more:

```python
>>> parameter_dist = {
  'C': scipy.stats.expon(scale=100),
  'kernel': ['linear'],
  'gamma': scipy.stats.expon(scale=.1),
}

>>> classifier = grid_search.RandomizedSearchCV(model, parameter_dist)
>>> classifier.fit(iris.data, iris.target)

RandomizedSearchCV(cv=None, error_score='raise',
          estimator=SVC(C=1.0, cache_size=200, class_weight=None, coef0=0.0,
  decision_function_shape=None, degree=3, gamma='auto', kernel='rbf',
  max_iter=-1, probability=False, random_state=None, shrinking=True,
  tol=0.001, verbose=False),
          fit_params={}, iid=True, n_iter=10, n_jobs=1,
          param_distributions={'kernel': ['linear'], 'C': <scipy.stats._distn_infrastructure.rv_frozen object at 0x110345c50>, 'gamma': <scipy.stats._distn_infrastructure.rv_frozen object at 0x110345d90>},
          pre_dispatch='2*n_jobs', random_state=None, refit=True,
          scoring=None, verbose=0)
```

#### Pipelining

SciKit-Learn has created a pipelining class. It wraps around your entire data analysis pipeline from start to finish, and allows you to interact with the pipeline as if it were a single white-box, configurable estimator.

If you don't want to encounter errors, there are a few rules you must abide by while using SciKit-Learn's pipeline:
* Every intermediary model, or step within the pipeline must be a transformer. That means its class must implement both the **.fit()** and the **.transform()** methods. This is rather important, as the output from each step will serve as the input to the subsequent step! Every algorithm you've learned about in this class implements .fit() so you're good there, but not all of them implement .transform(). Be sure to take a look at the SciKit-Learn documentation for each algorithm to learn if it qualifier as a transformer, and make note of that on your course map.
* The very last step in your analysis pipeline only needs to implement the **.fit()** method, since it will not be feeding data into another step
```python
>>> from sklearn.pipeline import Pipeline

>>> svc = svm.SVC(kernel='linear')
>>> pca = RandomizedPCA()

>>> pipeline = Pipeline([
  ('pca', pca),
  ('svc', svc)
])
>>> pipeline.set_params(pca__n_components=5, svc__C=1, svc__gamma=0.0001)
>>> pipeline.fit(X, y)
```
### [Dive Deeper](https://courses.edx.org/courses/course-v1:Microsoft+DAT210x+6T2016/courseware/fbe3b24442e840da93cbfb06b02fc078/1913e921dd5d4e7984077931a164f7d2/)
