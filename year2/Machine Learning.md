# The Big Picture 

### Regression 
Regressor is something that predicts real valued continuos data based on imputs.
So like the house price, grades
![[Pasted image 20260117194711.png]]

### Classifier 
Classifies into some discrete class. 
![[Pasted image 20260117194800.png]]

### Clustering 
![[Pasted image 20260117194851.png]]

### Evaluation 

For Classifier 

![[Pasted image 20260117194915.png]]

For Regressor 
![[Pasted image 20260117194943.png]]

In order to know whether any given accuracy is good enough we would need to compare it with a lower bound and upper bound 
**Lower bound**
We could take into account random performance. So say I have a classifier to classify a given image into 5 labels we can take 20% to be the lower bound 
But is there a stronger baseline than random? 
Well lets say we have to classify an image into either a dog or a cat. And say we know for the test data we have 60% cats and 40%dogs. So then we could have a stronger lower bound of 60% compared to 50%. 

**Upper Bound**
Best case, usually compared to humans. 

### Machine Learning 

![[Pasted image 20260117200147.png]]

So basically we need to find `h` which given `D` aka the Train data. We could predict the grade. 
But we still need to define a model aka an *Algorithm* which in our case would be to fix the structure and then learn the parameters 
![[Pasted image 20260117200345.png]]

So finally our model strucute is going to look like this 
![[Pasted image 20260117200407.png]]

So we basically split the data into `train` and `test` and then have input and output for each. Then we would `evaluate` 

### Machine Learning Settting 
**Supervised Learning**
![[Pasted image 20260117202140.png]]

**Unsupervised Learning**
![[Pasted image 20260117202151.png]]

Clustering is an example of unsupervised learning. 

### Feature Encoding 
![[Pasted image 20260117202248.png]]
The red box is where feature encoding resides 

Raw measurements are sometimes inefficient. 
So we extract into something which we agree is more efficeint. 

So now our diagram becomes something like this 
![[Pasted image 20260117202554.png]]

**Are more features good?**
NO!!

The *curse of dimensionality.* 
So say we have 5 exapmles. 
In one feature dimension its perfect 
But with 2 dimensions with 5 by 5, we would have 20 empty spaces 
With 3 dimensions with 5 by 5 by 5, we would get 120 empty spaces.
Even worse
![[Pasted image 20260117204914.png]]

It takes more longer to compute as well. 

### Algorithm 
Now lets look at the algorithm section of the picture. 

**Nearest Neighbour classifier**
Lets say we want to classify a given image into either a cat or dog. 
We find the closest `train` data to the `test` data and assign it to whatever the `train` data is. 
![[Pasted image 20260117205405.png]]
So in the given example we would say it is a cat. 
We could use any norm to quantify the distance. So `l1, l2 or linfinity` (See computational technique notes if you don't know what this is)

### Models 

Models are classified into either Non Parametrix Model vs Parametric Model. 

**Non Parametric Model**
It means the *number of paramters are not fixed*.
It means the parameters would scala along with the training data set and depends on it. 
Examples include Nearest Neighbour
which would take in parameter as the distance from the test data to all the data points of the train data set (Hence depends on train data set). 

**Parametric Model**
It means the numbef of parameters are fixed. 
Examples include Linear Regression 

------------------

Models can even be classfied into Linear Model vs Nonlinear model

**Linear Model**
If we can separate the data sets either by a point (1D), line (2d), plane (3d) or hyperplace (>3D). Then it is a linear model 

![[Pasted image 20260117210332.png]]

**Non Linear Model**
![[Pasted image 20260117210351.png]]

We could instead use a quadratic model to separate the data sets. 
This is quite a hack way to tune the model to the data set. 
Risk of over fitting 

*Feature Space Transformation*
We could transform a Non linear model into a linear model by using feature space transformation. 
For Example 
![[Pasted image 20260117211449.png]]
We could use the feature space transformation. 
`(x1,x2) -> (x1^2, x2^2)`
![[Pasted image 20260117211648.png]]
Now it becomes a linear model. Nice!

Or we could even combine multiple simple classifiers. 
![[Pasted image 20260118130504.png]]

### Nearest Neighbour Classifier 

**Vornoi Diagram**
It represents the partiition of space at test time. 
![[Pasted image 20260118131058.png]]
So if we are in yellow, then we are cat else we are dog 

**Decision Boundary:** The brown line in the above diagram, which determines the difference between the spaces. 

*Problem:* Overfitting. The islands are making the diagram too biased towards the training data. 
Like basically we are trusting one example 

*Solution:* Instead of looking only at one neighbour, we an look at the closest 5 neighbours. K-Nearest Neighbours. 
![[Pasted image 20260118131324.png]]
So in the above diagram we have k=5 
![[Pasted image 20260118131629.png]]
Here k=21, we can see the decision boundary is much more smoother. Hence the model is simpler. 

**Underfitting**
BUT if we increakse k to 25
![[Pasted image 20260118131732.png]]
There is no decision boundary and because we have more cats than dogs, the model would always classify it to Cats. PROBLEM.

### Bias Variance Trade Offs 
![[Pasted image 20260118132108.png]]

**Low Variance / High bias :** So if our model is too general and doesn't represent the test data and  completely ignores it. 

**High Variance / Low bias :** This is when we overfit the model on top of the data. This has high variance and low bias. But this isn't good as just removing one test data aka point from the model would make it completely rearrange. 

**Bias Variance Trade Off :** This is when we can have a robust yet good fit model that does not perfectly represent the data set but happens to be robust. 

We dont want 0 erros. We want to represent the test data as simply as possible. 


# Decision Trees 
![[Pasted image 20260118132741.png]]
So during *test* time we would have a tree and it would walk down the tree to make a prediction. 

![[Pasted image 20260118133545.png]]
We can come up with a decision tree to basically split the data into answering if we want true or false. So the tick represents true and the cross false. 

![[Pasted image 20260118133640.png]]
*Note:* The decision boundary doesn't care about the internal split in each subsction. 
Like compare the above diagram with the one that is above that. 

**Def.** Decision tree learning is a method for approximating discrete classification functions by means of a tree based representation. 
They employ top down greedy search through the space of possible solutions. 

Algorithms that do this: ID3, C4.5, CART

### General Algorithm
```
1. Search for an optimal splitting rule on training data. 
2. Split your data set according to your chosen splitting rule. 
3. Repeat 1. and 2. on each splitted subset. 
```

### How to select optimal splitting rule 
We want to parition datasets into pure. So what pure means is that each partition only contains elements of one dataset. So only cats or only dogs.


**Information Entropy**
Entropy is a measure of the uncertainty of a random variable. 
It can also be seen as the average/expected amound of information required to fully define a reandom state (or variable)
*Low Entropy* is Certain/Boring/Predictable 
*High Entropy* is uncertain/Suprise/Unpredictable 

```
Let say we have K states. 
Number of Bits required B = log(K)

So now we can define a random variable I
I(x) -> amount of info required to fully determine the state of a random variable. 

1. I(x) = log(K)

2. P(x) = 1/K (The probablity of a given state)
3. K = 1/P(x) (by rearranging)

4. I(x) = log(1/P(x)) (by substituting 3 into 1)
   I(x) = -log(P(x))
```

So now, for example we have 4 boxes and we want to keep track of in which box are we going to keep the key inside of. 
If they are of equal probability then we would get 

```
I(x) = -log(0.25) = 2 bits 
```

Ok but now lets say the probability of keeping the key in the first box is 97%
So
```
P(x=box1) = 0.97
P(x=box2) = 0.01
P(x=box3) = 0.01
P(x=box4) = 0.01 
```

Now what if someone says to us that he keeps the key in box 1. This information is pretty useless (*low entropy*) as we already know the person keeps the key in the first box 97% of the time. 

But what if someone says that it's in one of the other boxes represents a very important r suprising information (*High Entropy*) as we would have never predicted him keeping it in somewhere where there is 1% chance.

Entropy is defined as the average amount of information. 

![[Pasted image 20260118174113.png]]

So it is basically the expectation. Remember I(x) is log(P(x))

**Continuos Entropy**
![[Pasted image 20260118174410.png]]

**Back to selecting the optimal split using entropy**

![[Pasted image 20260118175059.png]]

Lots of things to notice from the above slide. So first of all we know how to calculate the entropy of indivdual blocks. 
In order to combine them we take the weighted average (4/20 * 0  thingy). 
So the left one we got 0.7168 and for the right one we got 0.6915. We want to have pure which is 0 entropy (boring one) as we want to make our lives easier. So the right split is better than the left one. 

*Information Gain :* Information gain is basically the difference between the before split entropy and the after split one.
So in the above diagram, the information gain for the left one is 0.2760 and the right is 0.3013. We want to maximise infromation gain. 

So *note the contrast* we want *lower entropy* which means we want *higher infromation gain*

![[Pasted image 20260118175724.png]]

The above slide just summarises the example. 

*Note:* The `entropy` for a 50% chance is 1

### Building a decision tree. 

![[Pasted image 20260118181909.png]]
lets say we have this dataset and want to know which attribute to select as split rule. 
We would need to compute the Information Gain on each of the arguments 
![[Pasted image 20260118182326.png]]

And *remember* while computing `IG` we would need to compute the `Entropy` on the whole and then each individual one as well, so for outlook it is (sunny, overcast, rain) and then we would need to compute its average one and then subtract from the original. 

![[Pasted image 20260118182537.png]]

And then we carry out doing the same for everything 
![[Pasted image 20260118182605.png]]

Now we choose outlook as it has the highest information gain. Easy!

![[Pasted image 20260118182638.png]]

So now the first layer is done. 

Now we need to process each individual one as well

![[Pasted image 20260118182811.png]]

So if we are now in the overcast we are chilling as everything is 1. 

![[Pasted image 20260118182742.png]]

If we are in sunny, we would need to look at the Information gain at each of the other columns. *Note:* we wouldn't consider outlook itself as it makes no sense to. 

![[Pasted image 20260118182920.png]]

So finally we would repeat the same for rain. 

![[Pasted image 20260118183014.png]]

And we have a decision tree!! Yay!


# Evaluation
### Hyperparameter tuning 
Hyperparameters are model parameters that are chosen before training.
For example : the k in k-NN 
**Overall objective** Finding the hyperparameter values that lead to the best performance. 

**Naive approach**
Try different values on the thraining dataset, then select the best accoruding to the accuracy on the training dataset and then evaluate on the test dataset. 

**Right Approach**
SPlit your dataset in 3: training/validation/test 
common splits between 60%/20%/20% and 80%/10%/10%
Try different hyperparameter values on the training dataset, then select the best according to the accuracy on the validation dataset. Perform the final evaluation on the test dataset. 
So an example of a pipeline would look like this 
![[Pasted image 20260121112619.png]]

Now that the hyper parameters are fixed, you can either use the model trained on the training dataset, or combine the training and validation datasets and train a new model (with the same parameters). 
The final evaluation is always done on the test dataset. 
![[Pasted image 20260121113329.png]]

**Gotchas**
In some random case study, there was an example where the dataset is very imbalanced so the researchers used oversampling to balance the classes (ie duplicate the data of the class that has less number of samples inorder to balance the classes). Then they split some of the data off for a dedicated test set. 
They got suspiciously high 
What went wrong?
The problem was the ordering. The split after they oversampled... leading into repeat in the test dataset whch led into high performance. 

### Cross Validation 
So lets say we have a sparse dataset and we want to make the most out of it. 
We could use cross validation to achieve this.
Divide dataset into k (usually 10) equal folds/splits. Use k-1 folds for training + validation and one for testing. 
Iterate k times, each time testing on a different portion fo the data. 
Peroformance on all k held out test sets can tehn be averaged. 
![[Pasted image 20260121120631.png]]
*Notice* it doesn't really evaluate how one model performs. Instead it is evaluating the entire approach of model evaluation and training. 
![[Pasted image 20260121120742.png]]
So what happens when we go into production?
When all our experiments are done and we know the best hyperparameter values, we can returan on the whoe dataset. 
Good: Can use all teh avaliable data fro training the model. 
Bad: We dont have a way of estimating the performance of the final trained model any more. 
![[Pasted image 20260121121236.png]]

### Evaluation metrics 
**Confusion Matrix**
Confusion matrix is not really an evaluation metric. Instead its just a way of representing thing.
![[Pasted image 20260121121731.png]]
Look at the TP, FN, FP, TN once *before exam*
The second letter is what is really predicted. And the first letter just says whether that is true or false. 

**Accuracy**

![[Pasted image 20260121122013.png]]
*Note* the classification error is 1 - accuracy. 

**Precision**
![[Pasted image 20260121122153.png]]

**Recall**
![[Pasted image 20260121122324.png]]

**Precision vs Recall**
![[Pasted image 20260121122608.png]]
So like if we are prediciting if a person has Covid or not. We would want high recall and low precision. As it is ok for use to make more FP than FN. If someone has Covid we need to report it +. 

**Alternative names**
![[Pasted image 20260121122759.png]]

**F measure / F score**
![[Pasted image 20260121123041.png]]
`Beta` controls how important precision is over recall. 0.5 beta means precision will be twice as important as recall.
F2 recall will be twice as important as precision. 

**Confusion matrix: Multiple classes**
![[Pasted image 20260208194435.png]]
So this works for class 1. So say we have to calculate precision recall for class 2. We would simply make class 2 to be the positive case. 
We caclualate *average* the same way as before.. so how many data points we got correct / total number of points. 
Precision, recall we compute for each class separately. We can then compute *macro averaged* precision, recall and F1 
So now what is **macro averaged**?
*Macro averaging:* Taking average on the class level. Calculate metrics for each class and then average them together.  
`For example in the above diagram to the left, to perform macro averaging for precision we would get. 1/4 (1/3 + 1 + 1/2 + 1/2)`
*Micro averaging:* 
Now for micro averaging precision we would simply do 
`total TP / (total TP + total FP)`
which would result in `4 / (4+4)` for the top left example. Nice!
*Note:* For binary, multi class classification -> micro averaged P, R, F1 are all just equal to accuracy. This can vary for more complex measures and multi label classification.
### Classification threshold 
Most classifiers give us a probablity of predicting a particular label for a given input. 
For binary classification we need to have a classification threshold that determines the label based on probability. 
![[Pasted image 20260208211444.png]]

### ROC curve 
Receiver operating characterisitc curve. Basically a visual representation of model performance across all thresholds. 
![[Pasted image 20260208211905.png]]
The green line represents the random base line, so when a model makes predictions at random. We want our model data points to always be above (top left diagonal) the green line (random base line), otherwise our model is just shit.
Practical ROC curve
![[Pasted image 20260208212219.png]]
So basically we would choose the right top point when FN costs us so much. So basically we would choose the threshold based on what we want from our dataset.
### AUC 
Area under ROC curve. It represents the probability that the model, if given a randomly chosen positive and negative example, it would rank the positive higher than the negative. 
So for a random model generator it would be `0.5` as picking a rank that is higher than the negative is 1/2. 
![[Pasted image 20260208212657.png]]

### Regression tasks 
![[Pasted image 20260208212732.png]]

### Real use cases of what we just learnt 
![[Pasted image 20260208213456.png]]
If we just look at the accuracy we may think that the model is doing something in the second case. But if we look at the confusion matrix / recall , precision for class 2 we would easily identify that the model is technically not doing anything useful. It might be just better to say class 1 every single time. lol
![[Pasted image 20260208213706.png]]
### Imbalance test set solutions
Just divide by total number of examples per class 
![[Pasted image 20260208213755.png]]
We could even downsample or upsample to solve these issues. 
	1. Downsampling the majority class -> select randomly the same number of examples as the minority class 
	2. Upsampling the minority class -> Add duplicates until both the classes are the same size 
### Overfitting / Underfitting
Overfitting: Good performance on the training data, poor generalisation to other data.
Underfitting: Poor performance on the training data and poor generalisation to other data. 
![[Pasted image 20260208215607.png]]
![[Pasted image 20260208215642.png]]
### Can we trust the performance metrics?
Amount of data. Getting 90% on 10 sample dataset is worse than 84% on 10k data points. 
**Sample error & True error**
The **True error** of the model is real error, which means if we deploy our model in all available data sets and we calculate the error from those. 
The **Sample error** of the model is what can be done in practice. Basically the error of the model `h` on a data sample `S` 
![[Pasted image 20260208220321.png]]
The complicated formula is hillariously simple!! Idk why they wanna scare small kids?
So we want to know the true error but we can only measure the sample error. 
### Confidence interval 
An `N%` confidence interval for some parameter `q` is an interval that is expected with N% to contain q. 
So for example. `95%` confidence interval `[0.2, 0.4]` means with probability `95%` q lies between `0.2` and `0.4`. 
![[Pasted image 20260208221307.png]]
Important things to *note* from the above diagram. First we start with the `sample error (errors(h))`.  The `Zn` is taken from the table below. 
Fun Example
![[Pasted image 20260208222110.png]]
So the `errors(h) = 22%`. The `n` in this case is simply `50` *note* its the number of testing samples used!
![[Pasted image 20260208222258.png]]
So now we can say with 95% confidence that the *true error* would lie between 0.11 and 0.33. Awesome!
*Note* as `n -> infinity` we can see the square root part would just become `0` and then the sample error would just become equal to the total error!
### Testing for statistical significance 
![[Pasted image 20260208222702.png]]
we are not going into depth of these algos 
![[Pasted image 20260208222740.png]]
![[Pasted image 20260208222809.png]]
WOW! Lets goo hypotheseis testing. 
A performance testing is considered to be statistically significant if `p < 0.05` 
A `p` value of `>0.5` doesn't mean that the two algorithms are similar but it means that we can't observe a statistical difference. (basic definition of significance in statistics.)
For instance collecting more data can change the p value in one direction or the other. 

### P hacking 
P hacking is the misuse of data analysis to find patterns in data that can be presented as statistically signifcant when in fact there is no underlying effect. 

It is done by running large numbers of experiments and only paying attention to the ones that come back with significance results. 

Statistical significance is defined as being less than 5% likely due to randomness (p < 0.05). 
That means we accept that some significant results are going to be false positives. 

**Stratergies against P-Hacking**
Make sure the `p` changes with respect to the number of experiments. So becomes small for larger Ms 
![[Pasted image 20260216232329.png]]

# Neural Networks 1
**Aritificial neural networks** are a class of machine learning algorithms. Architectures of connected neurons, usually optimised with gradient descent. 
**Deep learning** referes to using neural network models with multiple hidden layers. Complex models trained with large data sets. 

![[Pasted image 20260216233132.png]]
Amy Adams is replaced with Nicolas. 
Neural networks are used to make the above transition. They use *encoders* that are trained to take any kind of faces input and encode it into a vector represenation. 
All faces are trained with the same encoder. So the encoder learns to encode any kind of face into a vector representation, but the decoder is going to be *person* specific. Note the decoder A and decoder B. 
So if we want to replace Amy's face with Nic all we need to do is first encode Amy's face and then decode using Nic's decoder. Nice!

![[Pasted image 20260216233725.png]]

### Linear Regression 
Its a form of supervised learning. 
So we have datapoints of input to output 
So we have input features `x^1, x^2, ... , x^n`
And we know the desired output features `y^1, y^2, ... , y^n`
Our goal is to learn a mapping `f: X -> Y`

Linear regression can be divided into two subsets, classification vs regression. 
![[Pasted image 20260216234021.png]]
We already know this from before!! 

We need to find a best function. 
But what is best?

**Loss function**
Our loss function is the sum of squares. 
![[Pasted image 20260216234305.png]]
Where `y^` is the predicted value and `y` is the actual value. 
We know how to calculate the predicted value, so we just replace that into our definition `y^ = ax + b`
Smaller the value, better the model. 
If our loss function returns `0` then our model is perfect. 

We use derivatives to do our learning process 
![[Pasted image 20260216234653.png]]
Here `theta` is the lowest point, and we are in `theta 0` so we calculat ethe derivative at that point, giving us the dotted line. It says us if we decrease it in that direction, then `theta 0` would become closer to `theta`

So if we now find the partial derivative for `a`
![[Pasted image 20260216234825.png]]
Simple!
Do the same for `b` as well 
![[Pasted image 20260216235010.png]]

**Training LR**
*Gradient Descent*

Gradient descent repeatedly update parameters a and b by taking small steps in the negative direction of the partial derivative. Basically the fancy name given to the process explainged above lol!
![[Pasted image 20260216235242.png]]
*Note* `alpha` is a hyper parameter! We need to set it in advance. 
![[Pasted image 20260216235423.png]]

**Gradient**
Gradient is just a vector of all partial derivatives 
![[Pasted image 20260216235505.png]]
Same as done in Calculus!

**The analytical solution**
![[Pasted image 20260216235612.png]]
THE LINEAR ALGEBRA Solution!! LOL 

**Multiple Linear Regression**
Simple Linear regresson: `1` input feature
Multiple Linear Regression: many input features 
![[Pasted image 20260217105422.png]]
![[Pasted image 20260217105554.png]]
*Note:* Its not a straight line as the model is in a higher dimension. Meaning its a straight line plane in that higher dimension.

### Artificial Neuron 
Takes in some inputs and spits out one output. 
![[Pasted image 20260217105756.png]]
The thing inside the bracket is basically `Linear Regression` (note the same formula). The only difference between Neuron and Linear Regression is the activation function `g` which is basically a non linear transformation which takes it beyond just being a normal linear model. 
The `b` is an often implicit. We can add an extra input feature with value `1` instead. *Note:* The `x4` = `1` Look at the below diagrams to understand more .

So representing the same equation with Matrix notation we get this fancy stuff
![[Pasted image 20260217110318.png]]
Well the `b` didn't dissapear anywhere. 
Its still here 
![[Pasted image 20260217110600.png]]
`theta 4` is basically the `b` and note we have `x4` is `1` 

### Logisitic Activation Functiion 
![[Pasted image 20260217110927.png]]
Basically squishes the value between `0` and `1` The *sigmoid function*

### Logistic Regression 
The logistic regression model is *NOT* regression as it is used to do binary classification lol. 
So now if we combine the above **Logistic activation function** aka **Sigmoid function** 
We get this 
![[Pasted image 20260217111554.png]]
Lovely, so logistic regression can be used to map a binary value even better than normal linear regression model. 
The model is optimised using gradient descent, which we would see in a bit. 

### Perceptron 
Perceptron is the predecessor to the neuron. Its an algorithm for supervised binary classification. 
It has 2 classes `0` and `1` 
It uses `threshold function` as the activation function. 
Here is how the `threshold` function looks like 
![[Pasted image 20260217111944.png]]
Basically its similar to the `sigmoid` function but it is **not continuos** and `gradient descent` can't be used to optimise it!!!
Here is the equation of the perceptron along with its activation function. 
![[Pasted image 20260217112122.png]]

**Perceptron Learning Rule**
Is an algorithm to optimise the perceptron 
![[Pasted image 20260217113115.png]]
This looks taunting but actually really easy. 
First of all `alpha` is a hyper parameter, the learning rate. Its quite similar to the *gradient descent* formula.
Lets look intuitively what this is really doing. There are only `3` options as the perceptron can take values `0` or `1` only. 
1. If the desired output `y` is equal to our prediction `h(x)`, then the RHS of the summation becomes `0` and the weights stay the same. So we dont fix it if it isn't broken. 
2. If `y = 1; h(x) = 0` then the weight `thera i` is increased as to make our prediction closer to the actual one by adding `xi`. Easy 
3. Now if `y = 0; h(x) = 1` then the weight `theta i` is decreased by subtracting the feature value to make it smaller. 
EASY!

**Uses of perceptron and its limitations**
Any linearly separable function can be learnt using a perceptron. 
![[Pasted image 20260217114318.png]]
So take the above example, where we want to separate the black colour from the white colours, we can easily solve this with one line. 

But it can't be used for multiple lines problem. Like the XOR one 
![[Pasted image 20260217114534.png]]
We can't solve the above problem using perceptrons.
We can solve it using neurons by using 2 different neurons, one for each line and linking them together. 
The reason why we can't use perceptrons is because it is not differentialble and we canot use gradient descent to backpropogate through the layers. 

### Multi Layer Netoworks 
We can connect multiple neurons in parallel each one will learn to detect something different. 
Each neuron is basically a feature detector. It takes in a bunch onf inputs and learns to detect one feature really good. So it becomes like a binary classifier. Does this pattern exist in the data or not. 
Single neuron isn't of much use as it basically can be used to only know one feature. Instead we can thread multiple neurons in parallel.

**Multi layer Perceptron**
*NOT* Perceptron, its a neuron. The name is just stuck as because some retarded idiot was like oh that looks like a perceptron. 
We use gradient descent and backpropogation here and differentiable functions. So certainly *not* a perceptron!!!
![[Pasted image 20260217115608.png]]

Instead of drowing all the neurons out as circles we can instead
![[Pasted image 20260217115915.png]]

Here is how the definitions look like!
![[Pasted image 20260217120216.png]]
The middle layers are a hidden layer!
![[Pasted image 20260217120537.png]]

**Traditional vs Now**
![[Pasted image 20260217121329.png]]
Traditionally we use manually crafted feature extractors, so working with the data set more. 
But now-a-days we have moved towards an end to end training. Lower layers act as feature extractors and upper layers act as classifiers

### Activation function 
If we have a data set that can be linearly separable then we want to use a linear model.
But in practice that is often not the case. 
![[Pasted image 20260217121905.png]]

That's where activation functions come in. They turn neurons and neural networks into non linear functions. They can then learn more complex relationships 

**Linear activation**
![[Pasted image 20260217122115.png]]
So the last line in the above throws light on an important bit of neural networks. 
It basically says if we have two different layers connected by a linear activation then we can just combine them into one. 
So if we want multiple layers then we would need to use a *non linear* activation function. 

**Sigmoid activation**
![[Pasted image 20260217122502.png]]

**Tanh activation**
![[Pasted image 20260217122520.png]]

**ReLU activation**
![[Pasted image 20260217122725.png]]

**Softmax activation**
![[Pasted image 20260217122739.png]]

Most activation functiosn are applied element wise. Each element is passed through the function independently. Softmax is an exception. 
Activation of the output layer should depend on the task. For example: 
1. Classifying into two classes -> sigmoid or tanh 
2. Prediciting an unbounded score -> linear 
3. Predicting a probability distribution -> softmax 


# Neural Networks 2 
### Linear regresssion and gradient descent
![[Pasted image 20260217124228.png]]

### Loss functions 
Define a loss function that we want to minimise having alower loss means we are doing better on our tasks. 
Updating the parameters using gradient descent, taking small steps in the negative direction of the gradient. 
![[Pasted image 20260217124807.png]]
So visually it would look like this 
![[Pasted image 20260217124824.png]]

**Loss functions for regression**

We have regression task when we need to predict a continuos variable.
We would often use linear activation (basically does absolutely nothing) in the output layer and optimise the network with mean squared error 
![[Pasted image 20260217125011.png]]
Simple!

**Classification**
![[Pasted image 20260217125202.png]]

**Cross entropy**
The loss function for classification. 
Lets take binary classification, we can regard the output of our network as a parameter of a Bernoulli distribution, the probability of success given an input:
![[Pasted image 20260217130242.png]]

Now to remove the powers we can take log which gives us 
![[Pasted image 20260217130304.png]]
Note we are still trying to maximise the logarithm which is the same as maximising the original product. 

Now if we want this as a loss function, we need a way of minimising something 
![[Pasted image 20260217130351.png]]
We just multiple with `-` and we divide by the number of data points as well, this is done to factor out the dependednce of the loss on the umber of data points we are dealing with, so we dont really want the loss size to depend on the number of data points in our dataset or the number of data points in our branch. 

![[Pasted image 20260217130642.png]]
It gave out a distribution, which is because of softmax 

### Recommended Activation, Loss functions for different problems.

![[Pasted image 20260217130830.png]]

### Backpropogation 
First lets talk about Batching 
**Batching**
Combining the vectors of several datapoints into one matrix to improve speed and reduce noise. 
![[Pasted image 20260217131721.png]]
We get the same format in both of the multiplication happening. It makes it faster as well. As GPUs can accelerate the performance by optimisations. 

**Forward and Backward pass**
The forward pass is trivial 
we first start with the batched data which has shape `N x K`, where N is the number of data points in our batch and k is the number of features. 
![[Pasted image 20260217132635.png]]
Then we feed it into our first linear transformation and activation
![[Pasted image 20260217132806.png]]
Then we do the same and repeat
(Read from bottom to up)
![[Pasted image 20260217132822.png]]
Which then after we use the loss function 
![[Pasted image 20260217132852.png]]

Now we calculate the loss from top to bottom 
![[Pasted image 20260217132913.png]]
And feed it through, so some sort of optimisation instead of doing everthing at that stage. 
Reason its possible is because of chain rule 
**Chain rule**
![[Pasted image 20260217133035.png]]

**Backpropogation**
Backpropogation is propogating the gradients backwards through the network layers. 
![[Pasted image 20260217133522.png]]
so the loss for `Z` comes from somewher, with that we can caluclate the loss for `W` and loss for `X` as well. 
*Note* that we changed 
![[Pasted image 20260217133652.png]]

Lets look at bakcpropogation of linear layer 
**Backpropogation of linear layer**
Consider a particular case 
![[Pasted image 20260217203651.png]]
So we have an input which has two data points, each having two features. 
Then W is `2 by 3` so the input has two features but they output to 3 neurons. 

![[Pasted image 20260217134452.png]]
Ok so right now we have `delta loss / delta z` and we need to somehow calculate all these three values. 
*Note:* The `delta loss / delta x` is the thingy for the previous layer. 
The `detla loss / delta W` is for gradient descent 
The `delta loss / delta B` apparently is needed for gradient descent as well. 

Lets start with Computing `delta loss / delta x`
![[Pasted image 20260217135019.png]]
So over here the `w1,1` is bascially jst `delta z1,1 / delta x 1, 1`

Now repeating the above for all the rows we get. 
![[Pasted image 20260217135226.png]]
So gradient of input with respect to gradient of output is in relation with W^T 
How does this come you might ask?
Well just try doing `delta Z / delta X with Z = XW, we get W^T` using these below rules
![[Pasted image 20260217204214.png]]
Nice!!

Now lets move on to calculating `delta loss / delta W`
![[Pasted image 20260217135724.png]]

We can do the same for biases as well. 
![[Pasted image 20260217135744.png]]
So now we have completed back propogting through the linear layers, now we need to back propogate through the activation function.

**Backpropogating activation functions**
![[Pasted image 20260217135933.png]]
*Note:* That we just need to find `delta L / detla Z` nothing more. Lets goo!
And obviously `delta A / delta Z in the equation A = g(Z) is simply g'(z)`
And now finally generalising it to a matrix form for the whole Z we get 
![[Pasted image 20260217204710.png]]
*Important:* Note that this isn't normal matrix multiplication. Instead it is a numpy `*`

Common activation functions differentiation  
![[Pasted image 20260217140254.png]]
Lovely!

Softmax is a bit harder, but because we are combining it with cross entropy we can simply use it to make our lives easier. 
![[Pasted image 20260217205000.png]]

### Gradient Descent 
**Training**: The process of adjusting the model parameters to fit the given data. 

**Gradient Descent:** Repeatedly update parameters by taking small steps in the negative direction of the parital derivative so the model gets better at predicting our data. 
![[Pasted image 20260217205229.png]]

Steps 
```
1. Initialisze weights randomly 
2. Loop until convergence 
		Comute gradient based on the whole dataset 
		Update weights 
3. Finish 
```

![[Pasted image 20260217205554.png]]

**Stochastic Gradient Descent (SGD)**
```
1. Initialize weights randomly 
2. Loop until convergence: 
	   Loop over each datapoint:
		   Compute gradient based on the datapoint
		   Update weights 
3. Finish 
```
This is very noisy to take stpes based only on a single datapoint 

**Mini batched Gradient descent**
```
1. Initialise wieghts randomly 
2. Loop until convergence
		Loop over batches of datapoints:
			Comptute Gradient based on the batch
			Update weights 
3. Finish 
```

This is what is mostly used in practice. 

****

**Importance of Learning Rate**
the learning rate is simply the step size for each movement 
![[Pasted image 20260217210133.png]]
If the learning rate is too small as well, there is a problem of the model getting stuck in a *local minima* rather than finding a *global minima*
The learning rate is a hyper parameter. 

**Adapter Learning rates**
The intuition about this is have a different learning rate for each parameter. Take bigger steps if a parameter has not been updated much recenlty. Take smaller steps if a prameter has been getting many big updates. 
![[Pasted image 20260217210359.png]]
In the above image, you can see its a saddle, where the minima is in one plane and not in the others. 
The normal gradient descent (SGD) gets stuck in a local minima, but the rest escape it quickly at different rates. 
Examples of Adaptive learning rates, include Adam, AdamDelta. 

**Learning rate decay**
Reducing the learning rate by a factor
Intuition is to take smaller steps as we get close to the minimum, so we don't overshoot it. 
![[Pasted image 20260217210819.png]]

# CNN
Why neural networks fail in image anayliss. 
DIMENSIONS 
![[Pasted image 20260224124659.png]]

Curse of dimensionality 
![[Pasted image 20260224124805.png]]

![[Pasted image 20260224124836.png]]

### Invariance and Equivarance 
**Shift invariance**
![[Pasted image 20260224125023.png]]

**Shift equivarance**
If we need to say the pixels that belong to the cat are `1` and which do not are `0`. Then we would make our algo a bit different.  This process is called segmentation. 

![[Pasted image 20260224125256.png]]

### Assumptions 
**Translation invariance**
a shift in the input should simply lead to a shift in the hidden representation. 

**Locality**
We believe that we should not have to look very far away from any location(i,j) in order to glean relevant information to assess what this area contains. 

### Convolutions 
![[Pasted image 20260224130456.png]]

![[Pasted image 20260224130641.png]]
if all the weights are same, between the layers, then we would have a shift invariance. 

![[Pasted image 20260224130809.png]]

WE WOULD NEVER DEFINE OUR OWN KERNEL 
![[Pasted image 20260224131132.png]]

![[Pasted image 20260224131400.png]]

Need to take notes of the second half 

# Auto Encoders
Basically used to learn the identity function. 
So to learn how to reconstruct its input.
Problem is that there is an information bottleneck. as the first neural network (encoder) would only learn the important features and nothing more that that. Now the decoder should go from that back to the original image without any information.
![[Pasted image 20260302110442.png]]

So now what is a **latent space?**
Latent space is nothing but just a compressed representation (lower dimension) of the original image. 
![[Pasted image 20260302110642.png]]
*Note* you train both at the same time. 

# Unsupervised learning 
![[Pasted image 20260302130319.png]]

## Clustering 
The task of grouping instances in some feature space together such that instances in the same group are more similar to each other than to other instances in other groups. 

### Clustering with K-means 
Its a simple algorithm 
![[Pasted image 20260302132311.png]]
*Note:* It is better to take the intial random centroids from a subset of the training set as it would guarantee a data point being there.

![[Pasted image 20260302132954.png]]

Technically with K-means algorithm we are trying to minimize this 
![[Pasted image 20260302133413.png]]

Which we are doing in the above steps. But divided into 2 steps 
![[Pasted image 20260302133438.png]]

**Hyper parameter tuning**
How to select the right value of K??
![[Pasted image 20260302133748.png]]
**ELBOW Method**
![[Pasted image 20260302133820.png]]
The loss decreases as we have more K centroids as they each become their own cluster. 

## Probability Density Function (PDF)
![[Pasted image 20260507173709.png]]
p(x) >= `0` *Note:* There is no upper bound 
but the integral should be =`1` 
**Density Estimate** is to try to find p(x) from the data we have. So they give us a set of green points and we need to find the blue one. 

### Density Estimation: Applications
- Used to find anomaly.
	![[Pasted image 20260507174912.png]]
	Concept is used in commercial flights as well, to monitor the jet engines. So if something is off and identified as an anomaly it would be taken to the garage to run some more tests and try fixing the problem. 
- Used in gerative models. 
	![[Pasted image 20260507175151.png]]
	So the way, Dalle and Nano banana works, is that they have all these images in their sample space and the condition on the prompt. So match on all the images that the prompt matches with. 
	*Note* It isn't just a normal discriminative model, like the one we saw intially, neural networks, linear regression or decision tress. It doesn't split the images, but conditions on it with probability. 
--- 
Probability Density estimation is done with two classes of approximation 
- Non Parametric 
- Parametric. 
### Non Parametric Approach. 
- Don't make any assumption about the form 
- Follow the data 
- High variance & Low bias. 
Histograms is used for density estimation. 
![[Pasted image 20260507180356.png]]
![[Pasted image 20260507180735.png]]

### Parametric approaches 
- Makes simplifed assumptions about the form 
- High bias & Low variance. 
- Number of parameters is fixed and doesn't grow with the data. 

# Evolutionary Algorithm 
Optimisation algorithm for black box functions, no access to the gradient. 
![[Pasted image 20260507194713.png]]
 
![[Pasted image 20260507195640.png]]
So we mutate the best ranked ones and cross them and then repeat. 
## Genetic operators 
Algorithms are often defined according to 3 main operators:
- Selection operator: Selects the olutions that will be reproduced
- Cross over operator: Mixes the parent's genotype
- Mutation operator: Type and frequency of the variations applied to the genotype after reproduction. 
![[Pasted image 20260507195944.png]]

## Mastermind Game
![[Pasted image 20260507200420.png]]
We usually maximise in EA as we want to find the fittest individual. 

*Genotype* is how we represent the solution 
*Phenotype*is how the genotype can be devloped into a solution. 
So for example in the mastermind course. 
- Genotype would be Given N, the number of hidden pieces, the genotype would be the binary string with N `*` 3 bits. 
- Phenotype: We would simply aggregate the bits 3 by 3 each trio of bits is turned into an integer. Then each integer correspons to a different colour. 
![[Pasted image 20260507201054.png]]

### Selection operators
Goal is to select the parents for the next generation. 
Two ways of doing selection operatation.
- Bised roulette wheel -> Each individual gets a portion in the wheel and they occupy the memory as they have. 
- Tournament  -> Randomly choose 2. Then choose the best one. Repeat till we satisfy the number of samples for our next generation. 
![[Pasted image 20260507201656.png]]
So with elitism, we can guarantee monotonic increase, as it is guaranteed that the best individual would remain in the generation. 

### Cross over operator. 
Now with elitism and selection operator we have parents. Now the parents go on to make offspings. 
![[Pasted image 20260507202232.png]]

### Mutation operator 
So we randomly flip the bit in the bitstrings. 
![[Pasted image 20260507202313.png]]

### Stopping Criterion
![[Pasted image 20260507202434.png]]

![[Pasted image 20260507202540.png]]
![[Pasted image 20260507202556.png]]

## Evolutionary Stratergies
![[Pasted image 20260507202909.png]]
### mew + lamda evolutionary stratergy 
![[Pasted image 20260507203341.png]]

## Novelty search
![[Pasted image 20260507204936.png]]

## Quality Diversity optimisation 
![[Pasted image 20260507210359.png]]


# Good Questions To Practice 
Taken from TUT Sheet 1. The Big Picture, KNN, Decision Trees 
![[Pasted image 20260118215837.png]]
