This chapter turns its attention to classification systems.

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
