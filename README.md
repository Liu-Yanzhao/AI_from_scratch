# AI_from_scratch
Personal project to build a AI model from scratch

There are 3 main portions for an AI model to learn. 
1. [Forward Propogation](#forward-propogation)
2. [Back Propogation](#back-propogation)
3. [Updating Variables](#updating-variables)

# Introduction to the dataset
<img src="https://production-media.paperswithcode.com/datasets/MNIST-0000000001-2e09631a_09liOmx.jpg">

We are using a dataset from MNIST (Modified National Institute of Standards and Technology). MNIST is the de facto “hello world” dataset of computer vision. Since its release in 1999, this classic dataset of handwritten images has served as the basis for benchmarking classification algorithms.

We will be using picutres that are 28x28 training pixels. 
```
+---------+
|         |
|   784   |
|         |
+---------+
   28x28
```

There are `m` of this pictures.  This will result in a total of `m` 784 pixels overall. We can represent this in a matrix with a `m` rows and `784`  columns. The, we can transpose this matrix to make it `784` rows and `m` columns.

Now instead of each row being an example, each column is an example. 

$x = \begin{bmatrix}---x^1---\\---x^2---\\.\\.\\.\\---x^m---\\\end{bmatrix}^T=\begin{bmatrix}|&& | && && |\\ | && | && && |\\ x^1 && x^2 && ... && x^m\\|&& | && && |\\ | && | && && |\end{bmatrix}$

# Neural Network
We are going to have 3 layers
- the "zero-th" layer (input layer) has 784 units
- the first layer (hidden layer) has 10 units
- the second layer (output layer) has 10 units

```
[0]    [1]    [2]
 o      o      o
 o      o      o
 o      o      o
 .      .      .
 .      .      .
 .      .      .
 o      o      o
784     10     10
```

# Forward Propogation 
This is the process of taking an image and running it through the network to compute what the output is supposed to be. 

$A^{[0]}$ refers to the input layer. It is just equivilant to x. \
$A^{[0]}=x (784 \times m)$

$Z^{[1]}$ refers to the unactivated hidden layer. It is computed by multiplying a weight, $W^{[1]}$. with the value from the input layer, $A^{[0]}$, and adding a bias, $b^{[1]}$, to it.\
$Z^{[1]} = W^{[1]}A^{[0]} + b^{[1]}$\
if we were to look at the size of the matrix, it will look something like this\
$[10 \times m] = [10 \times 784] \times [784 \times m] + [10 \times 1]$

Now we are going to apply an activation function to it. By applying an activation function to it, an linear combination between nodes can be avoided (which creates a linear regression model which is not what we are looking for). We will be using an activation function called Rectified Linear Unit (ReLU). 

$R(x) = \left\{\begin{matrix}x & \text{if }x > 0\\0 & \text{otherwise}\\\end{matrix}\right.$\
<img style="height:200px;" src="https://www.nomidl.com/wp-content/uploads/2022/04/image-10.png">


By using this function, we can get out activated hidden layer values\
$A^{[1]}=g(Z^{[1]})=ReLU(Z^{[1]})$

now we can do something similiar to get from the hidden layer to the output\
$Z^{[2]} = W^{[2]}A^{[1]} + b{[2]}$\
But instead of using ReLU, we will apply a activation function called softmax to make the output values become probabilities, where all the probailities added up becomes 1. \
$\begin{bmatrix}-0.1\\3.8\\1.1\\-0.3\end{bmatrix} \rightarrow \frac{e^{z_i}}{\sum_{j=1}^{K}e^zj} \rightarrow \begin{bmatrix}0.02\\0.91\\0.06\\0.01\end{bmatrix}$

# Back Propogation
We will now run an algorithm to optimise the weights and biases. We are going the opposite way. We will look at the prediction and the expected outcome. We can then find how much deviation there is between the both of them and find out how much each respective weights and biases contributed to it. 

first, we will find out how much deviation there is between the prediction and the actual expected result.\
$dZ^{[2]}=A^{[2]}-Y$\
where (assume 3 is the correct answer): \
$A^{[2]}=\begin{bmatrix}0.5\\3.5\\4.7\\2.3\\.\\.\\.\end{bmatrix}$, $Y=\begin{bmatrix}0\\0\\1\\0\\.\\.\\.\end{bmatrix}$ \
This process of encoding y=3 is called hot-coding. 

now we can find out how much the weight and biases in the output layer contributed to the deviation\
$dW^{[2]}=\frac{1}{m}dZ^{[2]}A^{[1]T}$\
$db^{[2]}=\frac{1}{m} \sum dZ^{[2]}$

now we can find out how much the weight and biases in the input layer contribute to the deviation to do that, we need to take the error from the second layer, apply the weights to it in reverse, to get the error of the first layer. Then we can take gprime to undo the ReLU function on the value to get the true error. \
$dZ^{[1]} = dZ^{[2]} \times g^1(1)$\
from here, we do ssomehting similiar to what we did between the output and hidden layer.\
$dW^{[1]}=\frac{1}{m}dZ^{[1]}x^{[1]T}$\
$db^{[1]}=\frac{1}{m} \sum dZ^{[1]}$

# Updating Variables 
This is the final part, we update the parameters with the decided value to nudge them by. \
$\alpha = \text{learning rate}$\
$W^{[1]}=W^{[1]} - \alpha dW^{[1]}$\
$b^{[1]}=b^{[1]} - \alpha db^{[1]}$\
$W^{[2]}=W^{[2]} - \alpha dW^{[2]}$\
$b^{[2]}=b^{[2]} - \alpha db^{[2]}$

Once we update it, we run it for x iterations. 