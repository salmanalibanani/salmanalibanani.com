---
title: "Fairness and bias in Machine Learning"
date: 2020-08-24T16:41:39+10:00
draft: false
tags: ["Machine Learning", "Bias"]
summary: "How bias in machine learning impacts our lives and what can we do about it."
---
Bias in machine learning models is now a well know phenomenon.  There is a serious conversation happening in the ML community on this subject.  Existance and consequences of algorithmic bias in <a href="https://www.technologyreview.com/2020/07/17/1005396/predictive-policing-algorithms-racist-dismantled-machine-learning-bias-criminal-justice/" target="_blank">predictive policing</a>, <a href="https://qz.com/work/1098954/ai-is-the-future-of-hiring-but-it-could-introduce-bias-if-were-not-careful/" target="_blank">hiring</a>, <a href="https://www.brookings.edu/research/credit-denial-in-the-age-of-ai/" target="_blank">credit lending</a> and many other applications are being acknowledged.

In this three part series, I will explain the nature of this bias which often results in unexpected consequences when algorithms are deployed in real world scenarios.  I will talk about the origins of this bias, the problems caused by this bias, and what can be done to address these problems.

## Sources of algorithmic bias
To understand the source of algorithmic bias, we must first take a look at how machine learning actually works.  

Let's say we want to teach a computer how to distinguish between apples and bananas.  In a typical machine learning setting, we find lots of pictures of apples and bananas, label them accordingly, and feed them to a learning algorithm.  The algorithm (e.g. deep learning algorithms which are inspired by information processing patterns of the humain brain) will "learn" what an apple or banana looks like.  Once the algorithm has been "trained", it can be pointed to examples of apples and bananas it hasn't "seen" before.  A successful learning algorithm will correctly determine the fruit in those previously unseen examples.

This is a typical example of "surpervised" learning, the mode of machine learning where an algorithm is provided with a set of labelled examples to learn a concept.  And this is exactly where the algorithmic bias starts to creep into the system.  **If the examples presented to the algorithm during the training phase demonstrate certain biases, the algorithm later demonstrates same biases when it is used to classify/label examples it hasn't seen before**.

Let's look at an actual historical example of this: Amazon's secret AI based recruting tool.

Amazon is reported to have an AI tool to review job applicants' resume.  The idea was simple: given a set 100 resumes, return top 5 resumes that are most suitable for a given job.  It turned out that the algorithm was not rating candidates for roles in a gender neutral way.  It was systemically downgrading women's CVs for technical jobs such as software developer.  And it not difficult to see why that was the case.  The algorithm was fed resumes submitted to the company over a period of 10 years.  Most of those resumes came from men, which is a reflection of the male dominance in tech industry.  The algorithm essentially learned the "skewness" of the real world.  It learned that male applicants were preferable, and it penalized resumes from female candidates.  The tool was ultimately decomissioned because there was no guarantee that the algorithm was not going to learn other discriminatory ways of sorting resumes.

There are other <a href="https://www.tessella.com/insights/three-causes-of-ai-bias-and-how-to-deal-with-them" target="_blank">sources of bias</a>.  But bias in training data is probably the biggest technical challenge.  

Bias in, bias out!

## References
* <a href="https://www.reuters.com/article/us-amazon-com-jobs-automation-insight/amazon-scraps-secret-ai-recruiting-tool-that-showed-bias-against-women-idUSKCN1MK08G" target="blank">Amazon scraps secret AI recruiting tool that showed bias against women</a>