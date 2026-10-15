#This is my second (but really first) machine learning project. 

# Need to determine: If I have 60,000 images of handwritten digits, each with the correct answer, how would I design a program that learns to identify them?


# First Pass
# what separates digits from each other? I'd say each is a series of curves, straight lines and circles.
    # so the program should take these inputs features as a first line classification. In theory this sepoarates an 8 from a 7 but not necessarily an 8 from a 6.
    # next, but related, is to learn to associate the presence of certain features with a certain digits. 
    # Next are what i'd describe as subfeatures:
        # 1. The number of them - for example 1 typiclaly has one straight line while 7 typically has two. "Typically" because people can draw it differnetly so the model will have to attricuute probaility wieghts based on this.
        # 2. the relative sizes of them - using the same 1 and 7 example, in the event people draw both 1 and 7 with two straight lines, the relative sizes between them will most often be different. i.e. the line at the top of 1 will be relatively shorter compared to the longest line in the digit than in 7.  
        # (A third step for curves maye be looking at the radius but tbd)
# after learning to associate these features with certain digits, i will have the program add a susceptibiltiy feature.
    # what does this mean? For example even amongst humans, there is sometimes issues making out handwriting. A professor writing equations on the board and his "lambda's" look like "x's". A susceptibility feature should take ths into accoutn where the model says "This looks like a seven, but I know that sometimes 1 has looked like seven" so this should be given a small probability (cross-entropy loss capures this if im not mistaken?) 
# Once trained on the data it will be tested on a data set it has not seen before. Incorporating these the models should then be able to classify with a certain confidence what the digit is. It should also share what its second best guess is.

    #If its wrong it should provide a path to digest why it assigned the incorrect label to that digit (to se if perhaps there were unobsevred qualites of the data). If it labels a 7 as a 5 (which would not have been considered a potential susceptiblaty), why did it? Did it turn out, with the rues listed above, that 5's generally have similar curvature based on how people write, or the model cant identify certain curves accruately regardless of data? THerefore the model should tell what routes it took to get there baed on the outlined rules it "learned". This will be used to make model improvements. 

#Bonus points - can we identify if the above are "concepts" that the model uses to make predictions? If the model works accurately, can we compare it to another model to see if there are any similarities in the learning described. Am i thinking about model reduction properly here? Should this be about matrix operation simplifictaion or shoudl this be about the modekl being accurate with less weights from training? A model comparison could asnwer the second but not really the first.

# Second Pass

# For a Convulted NEural NJetworjk the mdeol wont generalluy ahve rules it folloows but instead will learn its own rules thrugh training 
    # Additonallly it dioesnt "see" the digts as humans do but as a seeries of pixels. 
    #This may provide a benefit though. COuntability? the idea that the length of certain lines ca be accurately measured by number of pictures and the ratio of one number to another is still possible.
    #Problem here is identifying curvature. How would i identify a curve from pixels? HOw does that translate mathematically? Well for most curves I'd first classify as open or closed - does the end line connect to the starting point or not. This is rule out circles. Next is for a curve vs a stright line the center of it should be the "deepest" part. Where deep can be mathamatically described as a range of pixels the the right or left of the start, approached with a gradient. 
    #Separately these may exist on an axis with x, y coordinates - so each is actually a vector? This should help furtehr with geomertric representation. for example for a straight line moving through 1,1 to 2,2 the gradient should be equal to 1 (well a range around this to account for margin of error).
    Then for a curve you'll find that as you moev along the line this slope will continously chnage. I.e. the second derivative of the points is non zero.

    #Note for me
        #The computer may see a 28x28 set of pixels as a matrix of numbers where each entry encodes a number from 0 to 255 for what colour that pixel is.

