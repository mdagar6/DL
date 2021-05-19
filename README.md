# DALL-E: Creating Images from Text

[![hackmd-github-sync-badge](https://hackmd.io/usp9PiUGRdGQzD93wE8Bsw/badge)](https://hackmd.io/usp9PiUGRdGQzD93wE8Bsw)

A neural network that creates images from text captions. 
![](https://i.imgur.com/FosonFM.jpg)


Go to the [website](https://openai.com/blog/dall-e/) and play with the captions! 

# Autoencoder
Let's start with some definitions:

## Definitions
### Input
$x$: is observed during both training and testing
$y$: is observed during training but not testing
$z$: is not observed (neither during training nor during testing).

### Output
$h$: is computed from the input (hidden/internal)
$\tilde{y}$: is computed from the hidden (predicted y, ~ means $circa$)

Confused?
Refer to the below figure to understand the use of different variables in different machine learning techniques.
![](https://i.imgur.com/Y4CFlZc.png)

## Autoencoder

These kind of networks are used to learn the internal structure of an input and encode it in a hidden internal representation ($h$) which expresses the input.

We already learned how to train energy-based models, let's look at the below network:
![](https://i.imgur.com/NAUmcJd.png)

Here instead of computing the minimization of the energy $E$ with respect to $z$, we use an encoder that approximates the minimization and provides a hidden representation $h$ for a given $y$.
$$h = Encoder(y)$$

Then the hidden representation is converted into $\tilde{y}$ (Here we don't have a predictor, we have an encoder)
$$\tilde{y}= Decoder(h)$$

Basically, $h$ is a squashing function $f$ of the rotation of our input $y$. ($y$ is the observation) and $\tilde{y}$ is a squashing function $g$ of the rotation of our hidden representation $h$.
$$h = f(W_hy + b_h) \\
\tilde{y} = g(W_yh + b_y)$$

Note that, here $y$ and $\tilde{y}$ both belong to the same input space, and $h$ belong to $\mathbb{R}^d$ which is the internal representation. $W_h$ and $W_y$ are matrices for rotation.

$$y, \tilde{y} \in \mathbb{R}^n \\
h \in \mathbb{R}^d \\
W_h \in \mathbb{R}^{d \times n} \\
W_y \in \mathbb{R}^{n \times d}$$

This is called Autoencoder. Enocder is performeing amortizing and we dont have to minimize the enerzy  $E$ but $F$:

$$F(y) = C(y,\tilde{y}) + R(h)$$

### Reconstruction Costs
Below are the two examples of reconstruction energies:
##### Real Valued Input:
$$C(y,\tilde{y}) = ||y-\tilde{y}||^2 = ||y-Dec[Enc(y)]||^2$$
This is the square ecludian distance between $y$ and $\tilde{y}$.

##### Binary Input:
In the case of binary input, we can simply use binary cross-entropy

$$C(y,\tilde{y}) = - \sum_{i=1}^n{[y_ilog(\tilde{y_i}) + (1-y_i)log(1-\tilde{y_i})]}$$

### Loss Function
Average across all training samples of per sample loss function

$$L(F(.),Y) = \frac{1}{m}\sum_{j=1}^m{l(F(.),y^{(j)})} \in R$$
We take the energy loss and try to push the energy down on $\tilde{y}$
$$l_{energy}(F(.),y) = F(y)$$


### Use-cases
The size of the hidden representation $h$ obtained using these networks can be both smaller and larger than the input size. 

If we choose a smaller $h$, the network can be used for non-linear dimensionality reduction.

In some situations it can be useful to have a larger than input $h$, however, in this scenario, a plain autoencoder would collapse. In other words, since we are trying to reconstruct the input, the model is prone to copying all the input features into the hidden layer and passing it as the output thus essentially behaving as an identity function. This needs to be avoided as this would imply that our model fails to learn anything.

To prevent the model from collapsing, we have to employ techniques that constrain the amount of region which can take zero or low energy values. These techniques can be some sort of regularization such as sparsity constraints, adding additional noise, or sampling.

### Types of Auto-Encoders

#### Denoising autoencoder - 

We add some augmentation/corruption like Gaussian noise to an input sampled from the training manifold $\hat{y}$ before feeding it into the model and expect the reconstructed input $\tilde{y}$ to be similar to the original input $y$.
![](https://i.imgur.com/WVcDLns.png)
An important note: The noise added to the original input should be similar to what we expect in reality, so the model can easily recover from it.


![](https://i.imgur.com/j1CQe3T.jpg)
In the image above, the light color points on the spiral represent the original data manifold. As we add noise, we go farther from the original points. These noise-added points are fed into the auto-encoder to generate this graph. 
The direction of each arrow points to the original datapoint the model pushes the noise-added point towards; whereas the size of the arrow shows by how much. 
We also see a dark purple spiral region which exists because the points in this region are equi-distant from two points on the data manifold. 