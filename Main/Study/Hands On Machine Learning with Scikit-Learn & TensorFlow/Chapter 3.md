This chapter turns its attention to classification systems.

### Let's build a classifier

The famous MNIST dataset of 70,000 small images of handwritten digits will be used.

```
from sklearn.datasets import fetch_openml

mnist = fetch_openml('mnist_784', as_frame=False)
```

Generated datasets are usually returned as an (X, y) tuple containing the input data and the targets, both are numpy arrays. The others are returned as sklearn.utils.Bunch objects which are dictionaries whose entries can be accessed as attributes. They generally have the following entries:
- "DESCR" a description of the dataset
- "data" the input data, usually a 2d numpy array
- "target" labels, usually a 1d numpy array

```
X, y = mnist.data, mnist.target
```

```
X.shape
>>> (70000,784)
y.shape
>>> (70000,)
```

In this dataset, like mentioned before, there are 70,000 images. Each image is 28x28px which means it has 784 features. Each feature represents the pixel's intensity, from 0 to 255. 0 is white 255 is black. 

Lets display one of the digits from the dataset using matplotlib

```
import matplotlib.pyplot as plt

def plot_digit(image_data):
	image = image_data.reshape(28, 28)
	plt.imshow(image, cmap="binary")
	plt.axis("off")

some_digit = X[0]
plot_digit(some_digit)
plt.show()
```
We use cmap="binary" to get a grayscale color map

The image displayed is a scribbled digit 5, im not displaying the image here in these notes.

We can check the label with:
```
>>> y[0]
'5'
```

We can create train and test sets via a split like so, using python slicing:

```
X_train, X_test, y_train, y_test = X[:60000], X[60000:], y[:60000], y[60000:]
```

We want to create a classifier for all the digits, from 0 to 9, but first, lets simplify the problem. We will make a binary classifier. Let's make the classifier just classify whether a digit is a 5 or not.

```
y_train_5 = (y_train == '5')
y_test_5 = (y_test == '5')
```

Sweet, now let us pick a classifier. This book uses a Stochastic Gradient Descent Classifier from Scikit-Learn.

```
from sklearn.linear_model import SGDClassifier

sgd_clf = SGDClassifier(random_state=42)
sgd_clf.fit(X_train, y_train_5)
```

Now we can use this to detect whether a number is 5 or not:

```
>>> sgd_clf.predict([some_digit])
array([True])
```
Dont forget that 
```
some_digit = X[0]
```

### Performance Metrics

Measuring Accuracy Using Cross-Validation

```
>>> from sklearn.model_selection import cross_val_score
>>> cross_val_score(sgd_clf, X_train, y_train_5, cv=3, scoring="accuracy")
array([0.95035, 0.96035, 0.9604])
```

Sweet, above 95% accuracy on all 3 cross-validation folds.

### Confusion Matrices
```
>>> from sklearn.metrics import confusion_matrix
>>> 
>>> cm = confusion_matrix(y_train_5, y_train_pred)
>>> cm
array([[53892, 687],
[1891, 3530]])
```

Each row in a confusion matrix represents an *actual class*, while each column represents a *predicted class*. The first row of this matrix considers non-5 images (the *negative class*): 53,892 of them were correctly classified as non-5s (they are called *true negatives*), while the remaining 687 were wrongly classified as 5s (*false positives*, also called type I errors). The second row considers the images of 5s (*the positive class*): 1891 were wrongly classified as non-5s (*false negatives*, also called type II errors), while the remaining 3530 were correctly classified as 5s (*true positives*). A perfect classifier would only have true positives and true negatives, so its confusion matrix would have nonzero values only on its main diagonal (top left to bottom right)

```
array([[54579, 0],
[0, 5421]])
```

