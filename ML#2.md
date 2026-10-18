#This is my second (but really first) machine learning project. 

# Need to determine: If I have 60,000 images of handwritten digits, each with the correct answer, how would I design a program that learns to identify them?


# First Pass
#what separates digits from each other? I'd say each is a series of curves, straight lines and circles.
    # so the program should take these inputs features as a first line classification. In theory this sepoarates an 8 from a 7 but not necessarily an 8 from a 6.
    # next, but related, is to learn to associate the presence of certain features with a certain digits. 
    # Next are what i'd describe as subfeatures:
        # 1. The number of them - for example 1 typiclaly has one straight line while 7 typically has two. "Typically" because people can draw it differnetly so the model will have to attricuute probaility wieghts based on this.
        # 2. the relative sizes of them - using the same 1 and 7 example, in the event people draw both 1 and 7 with two straight lines, the relative sizes between them will most often be different. i.e. the line at the top of 1 will be relatively shorter compared to the longest line in the digit than in 7.  
        # (A third step for curves maye be looking at the radius but tbd)
#after learning to associate these features with certain digits, i will have the program add a susceptibiltiy feature.
    # what does this mean? For example even amongst humans, there is sometimes issues making out handwriting. A professor writing equations on the board and his "lambda's" look like "x's". A susceptibility feature should take ths into accoutn where the model says "This looks like a seven, but I know that sometimes 1 has looked like seven" so this should be given a small probability (cross-entropy loss capures this if im not mistaken?) 
#Once trained on the data it will be tested on a data set it has not seen before. Incorporating these the models should then be able to classify with a certain confidence what the digit is. It should also share what its second best guess is.

    #If its wrong it should provide a path to digest why it assigned the incorrect label to that digit (to se if perhaps there were unobsevred qualites of the data). If it labels a 7 as a 5 (which would not have been considered a potential susceptiblaty), why did it? Did it turn out, with the rues listed above, that 5's generally have similar curvature based on how people write, or the model cant identify certain curves accruately regardless of data? THerefore the model should tell what routes it took to get there baed on the outlined rules it "learned". This will be used to make model improvements. 

#Bonus points - can we identify if the above are "concepts" that the model uses to make predictions? If the model works accurately, can we compare it to another model to see if there are any similarities in the learning described. Am i thinking about model reduction properly here? Should this be about matrix operation simplifictaion or shoudl this be about the modekl being accurate with less weights from training? A model comparison could asnwer the second but not really the first.

# Second Pass

#For a Convulted NEural NJetworjk the mdeol wont generalluy ahve rules it folloows but instead will learn its own rules thrugh training 
    # Additonallly it dioesnt "see" the digts as humans do but as a seeries of pixels. 
    #This may provide a benefit though. COuntability? the idea that the length of certain lines ca be accurately measured by number of pictures and the ratio of one number to another is still possible.
    #Problem here is identifying curvature. How would i identify a curve from pixels? HOw does that translate mathematically? Well for most curves I'd first classify as open or closed - does the end line connect to the starting point or not. This is rule out circles. Next is for a curve vs a stright line the center of it should be the "deepest" part. Where deep can be mathamatically described as a range of pixels the the right or left of the start, approached with a gradient. 
    #Separately these may exist on an axis with x, y coordinates - so each is actually a vector? This should help furtehr with geomertric representation. for example for a straight line moving through 1,1 to 2,2 the gradient should be equal to 1 (well a range around this to account for margin of error).
    Then for a curve you'll find that as you moev along the line this slope will continously chnage. I.e. the second derivative of the points is non zero.

    #Note for me
        #The computer may see a 28x28 set of pixels as a matrix of numbers where each entry encodes a number from 0 to 255 for what colour that pixel is.

# Third Pass 

the earlier writing discusses an ML algorithm that would intake rules firsthand instead of directly learning from data as in CNN. After the CNN is first created this can potentially be compared against the above for example to determine if in the CNN the natural learning of the algorithm generates “rules” in its “weights and biases” (something like mathematical numbers being represented in 1s and 0s) ie some kind of shared concept. 

In CNN you have a filter (called a kernel) going through the pixels, representing square m x m matrices.

So the kernel is different from the mathematical kernel of a matrix? Well yes, one is the vector producing the null space of a matrix A and the next is a matrix of learned representations from data (weights and biases). In this case it’s just a filter going through producing a maximum value for a complete match and 0 for a complete miss? The dot product would be 1. But then does that mean we have several different filters going through each doing this matching and perhaps a total weighted score summed at the end? Does not need to be nonnegative but the bottom line is match should be distinguishable from non match.

For the algorithm for the purpose of easy tracking / input I’ll have matches produce higher dots products with the kernels than nonmatches with complete misses producing the lower ones.

CNN will produce filters / kernels from learning. So my guess would be you have kernels to identify the rules I laid out earlier. What does that mean? A learned to identify straight lines, curves, circles etc not in the sense that the model knows what these are but in the sense of how the pixels / weights are produced inside the matrix of the kernel (perhaps think about how kernel size plays a role in this given you’re only looking at a small subspace at any given time). And since these rules are created but learned, the algorithm is able to retain useful kernels (ones that align so some innate classification understanding) or less useful kernels (ones like that don’t but can still be used to inform the useful ones via contrastive learning maybe). How would it do that? Well the most intrusive is oytcome driven ie if the model is guessing incorrectly what kernels providing the biggest numbers to that guess (because number contribution is correlated to kernel match) and over a series of tests are there kernels that contribute in this way most frequently? For example, if the model keeps seeing 7 as 5 perhaps there is a kernel or kernels contributing to it that recognizes / matches / weights the “nonlinearity” of handwritten straight lines to curves instead (if I’m thinking about this correctly). Therefore going backwards you can look at what the “confidence number” was (and here I’m using confidence number to mean the completed sum that is passed into the loss function or even just the sum after youve added the results from each kernel) for a digit, then identify what kernels contributed most to this incorrect classification and finally is this a trend. And have the algorithm do intermediate guess so the process is intermittently recursive instead of waiting until a wrong guess at the end.

my search should be no simply large activation but how is that activation being weighted into the final loss when the guess is being made

# Fourth Pass

This will be the pseudocode passed into pytorch.

Train the model on data set specific for training and have it adjust the initial weights and biases in its kernels 
This set would be a batch of digits and their correct labels
Combine these kernel outputs (from sliding across the images) to create feature maps.
Use these feature maps to observe a new training data set and then make predictions among the 10 digits.
Compare the prediction (along with the two guesses) with the correct labels using a cross-entropy loss function
Use backpropagation to observe how certain kernel weights affect the loss and update those parameters to reduce the loss corresponding to the incorrect guesses
Test on the testing dataset


# Fifth Pass

CNN have images in the format (batch, color channels, height, width)
 #does having thje algorithm write itself strengthen its understanding? Could it?
 The kernels will scan over the matrixes of the images and generate weights which the neurons will take and calculate outputs for the feature maps. 
this is also passed through ReLU to add nonlinearity. this allows the neurons to conduct more complex mathmactial operations with the wieghts and biases generated by the kernels

After this pooling is done to reduce the number opf entries to the largest per subset, reducing sencitivty while keeping the largest acvtviations 

This is then passed to the second convolution layer which will take those feature maps from the first layer as input and run further learning on them 
can also furtehr reduce computation here via using pooling

Should follow covolution -> actiavtion -> pooling -> repeat

At any staeg each of the images contain x numericla values. where x is calculated via height x width x # of feature maps. to get the number of weight and biases multiplyu the feature map by the size of the kernel. 

Then before tuning you need to flatten the nxn matruices into mxn vectors. this is done so that every point from the feature map can be used as an input in a fully connected layer


In eslier layers they use different weights but in the connected layer they use different weights? 

so they have different weights be 1 neuron is responsible for one number. ok. but what im asking is how we move from sharing weights to different ones. like how does that happen. is it because in this last layer there are. no more kernels parsing through info? 

The swicth happens based on the kind of layer you decide to create.

and the reason we don't have fully connected layers from before is there wouldn't be any specialization? no its because sharing weigth explaoits the fact that digits have simialr features which means you cna save on parameters. also how does each neuron automatically get to start searching for / corressponding to a number? how is that selected for? we select them. and then they learn through the loss function adn backpropagation 

the loss function is what will "teach" the neurons whether their predictaions are right or wrong. and then comes backpropagation. which will be dependign on how the nerurons predict, this will adjust the weights all the way up to the first layer.



# Questions this brough up to be invesitgated

Do incrasing layers increase model accuracy? why ior why not? If it does is there a saturation point? 


Randomness is neceessary beecause of recency bias


Generative model out of point clouds 
 

