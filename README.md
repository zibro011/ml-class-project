# Image Classification in Azure AutoML
Disclaimer: If GitHub cannot render notebook: please copy and paste the GitHub notebook URL into https://nbviewer.org/

## Overview
The inspiration for this project came from my interest in and passion for conservation. An issue that holds many people back from proper recycling and trash disposal is as simple as a lack of knowledge and resources. Using the Garbage Collective Data For Nature Conservation dataset by Baris Dincer on Kaggle, I set off to create an image classification model for disposables.

## The Data
The dataset I used comes from Kaggle user Baris Dincer and is title Garbage Collective Data for Nature Conservation. It consists of over 15,000 images. These images are broken into categories of battery, biological, brown glass, cardboard, clothes, green glass, metal, paper, plastic, shoes, trash, and white glass. For the purposes of my project, I ommitted the shoes category because shoes are not an item that the average person needs to dispose of on a regular basis.

I uploaded the data to Azure Machine Learning's Data Labeling interface. Due to time and resources, I needed to label all the images manually. Therefore, I cut down my dataset from 15,000 images to 2,252 images. If I were to continue this project, I would like to write a script to automatically label the images for me so I could use the entire dataset.

Finally, after labeling all my images by hand, I registered the dataset in Azure Machine Learning. The final dataset consisted of 2,252 rows and five columns. The columns are image url, label, label confidence, image heigh, and image width.

## The Experiment/ Modeling
Azure AutoML takes a dataset and runs it through multiple models using different preprocessing techniques until it finds the best model. To choose the best model, Azure calculates a primary metric of your choosing. For this project, I chose the Area Under the Curve or AUC. This is the measure of the ability of a classifier to distinguish between classes. 

I chose weighted AUC for my primary metric for two reasons that I found in Google's Machine Learning Crash Course. Firstly, AUC is scale-invariant which means "it measures how well predictions are ranked, rather than their absolute values". Second, AUC is classification-threshold-invariant which means "it measures the quality of the model's predictions irrespective of what classification threshold is chosen". These are important in my case because I did not have exactly equal numbers of samples in each class. Ultimately, I chose this metric because it is most common in image classification in comparison to the other metrics offered by AutoML.

During the AutoML experiment, each model was validated using k-fold cross validation. Because I had no way of finding the optimal k value due to resource constraints, I decided to experiment with 3 different k values. AutoML also offers a deep learning feature, so I tried that for my final model. The k values that I used in my first three experiments were 5, 3, and 8 respectively. There was no k value specified for the deep learning experiment.

In the notebook, you can see the different modeling and preprocessing techniques that were used during the AutoML runs. Here, I will discuss the best models for each experiment.

### Experiment 1
The most successful model for experiment one was the Voting Ensemble with an AUC of 0.81937. Voting Ensemble is a machine learning model that combines predictions from multiple other models.

The worst performing category was green glass. This makes sense because the different types of glass are hard to distinguish, and the green color makes it hard to distinguish from some biological images. The best performing category was clothing.

### Experiment 2
The most successful model for experiment two was also the Voting Ensemble with an AUC of 0.81375.

Similar to experiment one, green glass was the worst performing category while clothing was the best performing category.

### Experiment 3
The most successful model for experiment three was also the Voting Ensemble with an AUC of 0.81453.

Similar to experiments one and two, green glass was the worst performing category while clothing was the best performing category.

### Experiment 4
The most successful model for experiment four was Logistic Regression using the Standard Scaler Wrapper with an AUC of 0.80634. Logistic Regression is a classification algorithm that predicts the probability of a categorical variable, and the Standard Scaler Wrapper normalizes features to a value between 0 and 1 and feeds them into a wrapper model that follows a greedy search approach.

This experiment confirms that green glass was the worst performing category while clothing was the best performing category in all four experiments.

## Results
After comparing the different models, we can see that my first estimate of k (5) was actually the best performing which leads me to believe the optimal k value is probably somewhere around 5. We can also see that the Voting Ensemble model was the best performing. This makes sense because this method combines the predictions of multiple other models which increases its accuracy. With a highest AUC of 0.81937, our model is not too bad.

In the results section of the notebook, I tested my deployed model with images from the existing dataset. Unfortunately, only four of the eleven categories were successfully categorized. I also added a breakdown of the ROC for each category. The clothing category was best performing which makes sense because the pictures were more similar in style and there were more samples of that category. The clothing test was successful which is what we would expect.

## Future Plans & Thoughts
If I get the time to come back to this project, I would love to use all the available data as well as train and optimize my models manually. I think that those two adjustments would improve my model's performance immensely. 

Finally, my goal would be to deploy my model in a user interface where a user can submit a picture and my model will tell them what category it is. Ideally, I would love to create a mobile application where the user captures or submits an image, and the application uses my model to return the type of material and corresponding instructions on how to dispose of it. At the University of Minnesota, we have signs on all our different recycling and trash bins. I would like my application to expand that information by being able to categorize any item (not just those listed in the graphic above the bins) and advise people where to take their disposables if proper recycling is not available in their home, office, school, or whatever building they may be in.