This chapter is just an introduction on what machine learning is, its applications, and challenges that come with it. 

It begins by defining what *machine learning* even is. The definition is *the science (and art) of programming computers so they can learn from data.* 

Additionally, the book gives us two more definitions as follows: 

### More general definition:
Machine Learning is the field of study that gives computers the ability to learn without being explicitly programmed.

### Engineering oriented definition:
A computer program is said to learn from experience E with respect to some task T
and some performance measure P, if its performance on T, as measured by P, improves
with experience E.

### So what is this good for? The author gives us a few answers. 
* Problems for which existing solutions require many many rules, which a machine learning algorithm can simplify.
* Problems that are far too complex for traditional problem solving methods.
* Fluctuating circumstances/environments
* Understanding and deriving conclusions from copious amounts of data.

### There are four main types of machine learning. Supervised, unsupervised, semisupervised, and reinforcement learning.
* Supervised learning is when the training data contains labels for the correct conclusions. Some important algorithms include:
	* k-Nearest Neighbors
	* Linear & Logistic Regression
	* SVM
	* Decision trees and Random Forests
	* Neural Networks
* Unsupervised learning is when the algorithm must learn without these labels, by *itself*. Some important algorithms include:
	* k-means, DBSCAN, Hierarchical Cluster Analysis
* Some algorithms can deal with partially labeled training data, usually a lot of unla‐
beled data and a little bit of labeled data. This is called semisupervised learning.
- Reinforcement learning gives the algorithm rewards for making correct choices in a certain environment, and penalizes it for making the wrong ones. 

### Batch vs Online Learning
Batch Learning means that the model must be trained on all the data at once, using all available data. This takes up a lot of computing resources and therefore is done *offline*. Once it is deployed it will not learn anymore, not unless new data is collected and the model is explicitly retrained. Online learning is the opposite of this, the model learns by being fed new data constantly, perhaps from users. This gives the advantage of faster adaptability. 

### Instance Based vs Model-Based Learning
Instance based learning learns by examples, then generalizes to new cases that it encounters via a similarity measure. Model based learning creates exactly that, a *model* of those examples, then uses that model to make predictions. 

# Exercises:
1. How would you define Machine Learning? *Machine Learning is a domain of computer programming which designs algorithms that can learn on their own and derive conclusions without being explicitly programmed.*
2. Can you name four types of problems where it shines? *Facial recognition, speech recognition, language translation, data analytics.*
3. What is a labeled training set? *It is a set of data that has the "correct answers" identified, and the algorithm must learn how to use the other information to arrive at those correct answers. *
4. What are the two most common supervised tasks? *Spam filter & predicting prices based on a set of features.*
5. Can you name four common unsupervised tasks? *Cluster user's of an app by similarity of content consumption is just one. *
6. What type of Machine Learning algorithm would you use to allow a robot to walk in various unknown terrains? *Reinforcement Learning.*
7. What type of algorithm would you use to segment your customers into multiple groups? *Unsupervised Learning.*
8. Would you frame the problem of spam detection as a supervised learning problem or an unsupervised learning problem? *Supervised Learning.*
9. What is an online learning system? *One that constantly learns off new data being fed into the model, usually from online sources such as users.*
10. What is out-of-core learning? *Out of core learning is when the data is too large to fit into memory, so then it is batched into sections and fed into the model until all of the data has run out.*
11. What type of learning algorithm relies on a similarity measure to make predictions?*Instance based learning algorithm.*
12. What is the difference between a model parameter and a learning algorithm’s hyperparameter? *Model parameters are the values that a learning algorithm learns from the data in order to make predictions. Hyperparameters are the parameters of the model itself. For example, for a neural network the parameters are the weights and biases, whereas the hyperparameters are the number of neurons, layers, activation functions.*
13. What do model-based learning algorithms search for? What is the most common strategy they use to succeed? How do they make predictions? 
14. Can you name four of the main challenges in Machine Learning?
15. If your model performs great on the training data but generalizes poorly to new instances, what is happening? Can you name three possible solutions?
16. What is a test set and why would you want to use it?
17. What is the purpose of a validation set?
18. What can go wrong if you tune hyperparameters using the test set?
19. What is repeated cross-validation and why would you prefer it to using a single validation set?