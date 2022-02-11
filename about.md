---
layout: inner
title: Quarter 1 Recap
permalink: /about/
---
## Introduction

In truly adversarial spaces, deep learning models find themselves at risk of attack from adversarial models. In many real world cases, this can prove to be detrimental in the performance of these deep learning models and can have significant real world impact when they are exploited. As a result, we want to look into the idea of whether or not classifiers can be developed to be robust to these adversarial examples.
Adversarial attacks are methods used to interfere with deep learning systems- with the intent of finding ways to misclassify data. These attacks generally come in the form of targeted and untargeted attacks. Targeted attacks entail manipulating data to output a desired outcome after feeding it to a model, while untargeted attacks focus on manipulating data to simply not be recognized as it’s correct output.
We will analyze ways in which models can be trained to overlook adversarial examples through robustness. Robustness refers to the ability of an algorithm to perform strongly even when there are shifts or changes in what it is seeing, even if the base class is the same. An example of this would be photoshopping an image of a medical condition and training the model to still determine what that medical condition is, even with the unfamiliar, edited element on the image. Increasing the robustness of a model can be used to prevent possible edge cases from decreasing the accuracy of a model. We will be analyzing the methods described in Adversarial Attacks Against Medical Deep Learning Systems on adversarial attacks and adversarial training, as well as exploring robust training methods we would like to apply to medical deep learning systems. For the purpose of this paper, we will look into the dermatology case specifically to better understand the usage of adversarial attacks against deep learning systems used in determining whether or not a skin condition shows signs of melanoma, a form of skin cancer. 


# Background

An area where deep learning systems find themselves particularly at risk for adversarial attacks is within the healthcare space. Healthcare is fundamentally a sensitive space, with patient data being highly protected and every decision made having a lasting impact on the health of the people that are involved. As technology advances within this sector, deep learning algorithms are being used for new tasks, including diagnosing patients with conditions based on viewing medical images such as photographs, x-rays, and other diagnostic scans. In cases where adversarial examples attack these deep learning models, the possibilities for misdiagnosis and subsequent fraud and bodily harm begin to grow. We want to determine how to develop robust solutions to protect this sensitive data and model predictions against such adversarial examples. 
In the case of these adversarial attacks here, we wanted to get a further understanding of what parts of the algorithm these attacks tend to focus on. The understanding is that these deep learning algorithms are built using neural networks, particularly convolutional neural networks as is usually the case when working with image inputs (University of Michigan). Convolutional neural networks, or CNNs for short, work through the usage of layers that handle functionalities such as decreasing the computational power required to process the data through dimensionality reduction, extracting dominant features, and flattening in order to produce the proper classification output (Saha). In the case of this replication, we have used the same pre-trained ImageNet models with tuned parameters for classification and pre-trained ResNet-50 models to build our networks as the source material.
When it comes to the adversarial attacks, they can affect all sections of the model pipeline. Training data can be affected by data breaches and bias, training can be affected by improper training, the model can be affected by privacy breaches, deployment by system disruption and shifts on real-world data, and results by model stealing. In the scope of this paper, we will implement human-imperceptible attacks through projected gradient descent and patch attacks, both of which will be covered in the Methods section of this report. 

## Methods

### Practice: PGD Attacks 
Projected gradient descent allows us to perform onto the loss function expressed by the following equation:

This allows us to identify the optimal perturbation, which means the approximate solution, to maximize the above to better train for adversarial situations. The process we use when we implement this in the code is to start off by loading in the White Box and Black Box models and developing a method for the PGD attacks. In the case of the paper we are replicating, the PGD attacks are created from a library called Cleverhans and utilizing parameters of 20 iterations with the hyperparameter ϵ = 0.02. Once this is run, we are able to then get the results of the model through accuracy, AUC score, and Average Confidence. From here, we are able to evaluate the model on a specified label through clean and adversarial data using the batch_eval method from the Cleverhans library. Once this is done, we look into the L2 norm of the perturbations, which gives us the vector distance of the perturbation from the origin of the vector space. Given these L2 norms, we are then able to identify which images are the most perturbed, both on the healthy cases and the sick cases. Once this is complete, we can visualize our model and its performance by running it on our data and producing graphs and charts related to confidence, ROC, sensitivity, and specificity.  
### Practice: Patch Attacks 
The adversarial research paper performs an example of multiple patch attacks for three medical domains (same as mentioned above): CXR, Melanoma, and Skin Patches. The overall idea of this test is to trick a model into outputting the wrong label for detection of unusual conditions found in an image that is fed in. We do this through attaching specifically generated patches to the images using iterative approaches. 

We start by creating two resnet models. One model will be used for us to perform an adversarial attack, while the other model will be used as the model which we attempt to attack. 

After this, we perform a white box attack, in which we assume that we have access to all of the parameters of our second model and obtain a fine tuned image containing our adversarial patch. We create patches that both output 0 (negative detection) and 1 (positive detection). 

The research paper also details the performance of a black box attack, in which we assume that we don’t have access to any of our second model’s parameters, and are left to create adversarial images containing patches with the limited information we have. 

### Theory: Adversarial Attacks 

We now explore adversarial attacks and their different types. Examples of adversarial attack strategies include utilizing the Fast Gradient Descent Method and Projected Gradient Descent to Lower bound the inner maximization problem. 

We will now discuss the inner maximization problem.

We notice that h(theta) represents our model, while x is the data we feed into the model, and Y is the final output. The idea for this is that rather than minimizing the loss of our data to find the label which has the least loss when compared to our data, we want to maximize this loss to find the label that has the largest loss value from our label.
These attacks are considered 'untargeted’ as they aim to simply ensure the data fed is misclassified. For a targeted attack, we use the same strategies as specified for untargeted attacks, except in this case we misclassify our data to a specific classification. 

The difference between the two attacks is that a targeted attack must not only maximize the loss of the real data label, but also minimize the loss for our target label. 

Image from adversarial-ml-tutorial.org, Chapter 3
	
### Adversarial Training

The process of adversarial training largely revolves around the idea that creating and utilizing adversarial examples within the training process of a model will allow that model to better protect against such adversarial examples being used against it in real world practice. The main questions asked when it comes to adversarial training revolve around which adversarial examples to use within the training process. When looking into this, it can be seen that the main aspects to consider in adversarial training is that the strong attacks should be incorporated into the inner maximization problem of the algorithm in this case. General consensus in the data science community indicates that the best way to do this is through projected gradient descent methods. When it comes to how this training is structured, the following formulas can better visualize how this process works.

Beyond this, it is common to randomize over the starting positions in projected gradient descent in order to avoid issues when it comes to the ​​procedure learning loss surface. During our replication of the code for this report, we decided to utilize the pre-trained models given to us in order to offer more consistent results and performance in the later parts of our research, which we have cited in our Github repository.     

### Future Exploration: Robust Training
Robust training also finds use in convex relaxation methods, also known as providing the provable upper bounds on objectives. If we are able to add upper bounds to check the robustness of the model against adversarial examples, we can use the upper bound to help us make a model that minimizes losses based on that bound. 

Robust training is focused around the idea of minimizing loss based on an upper bound. In this case, training in this case will allow for the discovery of meaningful bounds. In order to do this, we must “the interval bounds to upper bound the cross entropy loss of a classifier, and then minimize this upper bound” (Adversarial Robustness - Theory and Practice). We believe that in future exploration of adversarial attacks in healthcare deep learning systems, a deeper look into robust training can provide more insight into how we may better combat adversarial examples in real world settings. 


