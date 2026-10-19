The goal of this personal project is to design a CNN capable of recognizing handwritten digits. As one of my first projects I have approached writing this readme in a way that not only makes the experiment easily replicable but also documents the design choices, things I learned and questions I had as someone with a ChemE / Finance background relatively new to the ML space.

To start, the initial question I tried to answer was: If I have 60,000 images of handwritten digits, each with the correct answer, how would I design a program that learns to identify them?

To answer that I asked myself what separates digits from each other? I'd say each is a series of curves, straight lines and circles.
   Therefore these would be the inputs the models should learn to distinguish in order to be able to classify them. It should learn to associate the presence of certain features with certain digits.
       This would theoretically allow differentiation between an 8 from a 7, but not necessarily an 8 from a 6.
   It should also learn to distinguish what could be described as subfeatures:
       (1) The amount of them - for example, 1 typically has one straight line while 7 typically has two ("typically" because people can draw it differently).
       (2) The relative sizes of them - using the same 1 and 7 example, in the event someone drew both 1 and 7 with two straight lines, the relative sizes between them will most often be different. i.e. the line at the top of 1 will be relatively shorter compared to the longest line in the digit than in 7. 
       (3) For curves, one could look at the deviation of the center of a line from a hypothetical straight line between its two end points

After learning to associate these features with certain digits, the model should have a susceptibility feature.
   What does this mean? For example, even amongst humans there are sometimes issues making out handwriting. A professor writing equations on the board and his "lambda's" look like "x's". A "susceptibility feature" would take this into account (attention from other inputs) where the model says "This looks like a seven, but I know that sometimes 1 has looked like seven" so this should be taken into account.
  
Once the model learns the parameters to make its predictions, a loss function should be used to determine how far off the predictions are from the correct answers. This will be used to calculate the gradients of the parameters used via backpropagation. Once the gradients are known, an optimizer (like SGD, Adam etc) should be used to adjust the gradient to reduce that loss and this process is repeated to improve the predictions.  Can we identify if the above are the "concepts" that the model learns to make predictions? If the model works accurately, can we compare it to another model to see if there are any similarities in the learning described? 

Refining the proposal, a Convoluted Neural Network (CNN) will be used here. It does not “see” the digits as humans do but as a series of pixels.
Immediately this brings up a question of how will it identify curvature from pixels? How does that translate mathematically? Perhaps through deviation, ie, for a curve vs a straight line the center of it should be the "deepest" part. Where deep can be mathematically described as a range of pixels the the right or left of the start, approached with a gradient. Separately these may exist on an axis with x, y coordinates - so each can actually be represented as a vector.  For example, for a straight line moving through 1,1 to 2,2 the gradient should be equal to 1 (well a range around this to account for margin of error). Then for a curve you'll find that as you move along the line this slope will instead continuously change, i.e., the second derivative of the points is non zero.

Further to this, the computer may see a 28x28 set of pixels as a matrix of numbers where each entry encodes a number from 0 to 255 for what colour that pixel is.

In CNN, you have a filter (called a kernel) going through the pixels, representing m x m matrices.

This kernel is a matrix of learned representations from data (weights and biases). In this case, it’s just a filter going through the pixels of the digits and producing a value based on a match between the current area of the image and the kernel (called activation) which the model uses to learn its parameters. Thus, the CNN will produce filters / kernels from learning. 

The pseudocode is below:

1. Train the model on a data set specific for training and have it adjust the initial weights and biases in its kernels. This set would be batches of digits and their correct labels.
2. Combine these kernel outputs (from sliding across the images) to create feature maps.
3. Pass the output from these feature maps to another layer which uses those inputs to further refine the learned weights and biases.
4. Pass these to a final layer where the model makes a prediction among the 10 digits.
5. Compare the prediction with the correct answers using a cross-entropy loss function
6. Use backpropagation to observe how certain kernel weights affect the loss and update those parameters to reduce the loss corresponding to the incorrect guesses using optimizers
7. Test on the testing dataset

CNN has images in the tensor format (batch, color channels, height, width). A 3D tensor can be thought of as a stack of matrices. The kernels will scan over the matrixes of the images and generate weights which the neurons will take and calculate outputs for the feature maps. The outputs of the feature maps also passed through ReLU, a nonlinearity function which floors the values at zero. This allows the neurons to conduct more complex mathematical operations with the weights and biases generated by the kernels. Afterwards, pooling is done to reduce the number of entries between layers, reducing sensitivity to noise while keeping the largest activations.

Hence the sequence follows convolution -> activation -> pooling -> repeat.

At any stage of each of the processes, the tensors contain x number of numerical values. Where x is calculated via height x width x # of feature maps. To get the number of weights and biases, multiply the feature map by the size of the kernel.

Then for the final layer, the stacks of matrices are flattened into stacks of vectors. This is done so that every point from the feature map can be used as an input in a fully connected layer. After this, comes tuning (i.e. the backpropagation and optimizers).

In earlier layers, the neurons shared weights but in this connected layer they use different weights. How do they move from sharing weights to having different ones? The switch happens based on the kind of layer you decide to create. The reason we don't have fully connected layers from before is because sharing weights exploits the fact that digits have similar features which means you can save on parameters. How does each neuron start corresponding to a specific number? We select them and they learn through the loss function, backpropagation and the optimizer. The loss function is what will "teach" the neurons whether their predictions are right or wrong, then comes backpropagation, which will observe how the gradients of the parameters affect the loss - information used by the optimizer to update the parameters. In this particular experiment I was interested to see how the selecting different optimizers might affect the model’s accuracy. Thus the model was designed in such a way as to be able to update gradients with the choice between using SGD and Adam.

The initial accuracy of the algorithm’s predictions is around 90%, giving much room for improvement during a further investigatory phase. It was also noticed that among the models wrong predictions, the most common error was mistaking 3’s for 5’s. Investigating this further may allow a better understanding of what the model actually “learned” inside its weights and biases.

As I continue to refine this project, further questions also include:

Will increasing the layers increase model accuracy?  If it does, is there a saturation point? 

Can a different nonlinearity function perform better here?
 

