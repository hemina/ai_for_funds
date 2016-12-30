# Specification and feasibility analysis of the Bot "AI For Funds" 


**27 Dec. 2016**  
**By Mina HE**  				
*Copyright © reserved.*
![img](../images/machine-learning-ai-artificial-intelligence-e1462471461626-840x487.jpg?raw=true)

[toc]
## 1. Introduction
Inspired by a Kaggle competition ["The Allen AI Science Challenge"](<https://www.kaggle.com/c/the-allen-ai-science-challenge>), where people compete training the model by "reading" textbooks and "doing" exercises then letting the machine to take the 8th-grade exam for a highest score, I come to understand the power of Natural Language Processing (NLP). 

Besides the well-known NLP applications of sentiment analysis, topic classification, and entity detection, which are not quite useful for our banking/asset management industry, it’s also possible to ask machine to read the abundant text materials, providing us only the very condensed information asked.

This report describes the bot "AI for funds" in detail, including the specifications of functionalities, and the feasibility analysis considering the NLP state-of-art. 

## 2. Functional specification
The initial objectif of this artifitial intelligence bot is to apply the Knowledge-Based (KB) Question Answering (QA) model presented in [this paper][1] to the financial industry by scrapping and reading the finance-related articles on the internet. The bot will be able to answer the questions asked during Human-Machine conversation by citing and composing its reading materials. The principle of this magical process will be explained in detail in the Primary functionality part.

Furthermore, many other functionality extensions (so-called Secondary functionality) are proposed in order to improve the user experience and to reinforce the robustness of our language model. 

During our development, we will start by implementing the primary functionality while considering and reserving the corresponding interfaces for the secondary functionality which will be completed progressively in future versions.


### Primary functionality
Here are some samples of Question-Answer (QA) tasks presented in the [paper][2], where it's proved that the machine is capable to do the simple text inferencing work after being trained with a large amount of Question-Answer data. It's different from simply looking for an existing expression in the previous reading material, but more importantly, there is a logical deduction process done by the machine as what happens in our brains. Machine comprehension theories are applied to take the TOFEL test, as shown in [this paper][3].

![](https://github.com/hemina/ai_for_funds/blob/master/images/AI%20funds/QA%20sample.png)

However, in our case, it's not convenient to send a corresponding article to the bot each time before submitting our questions. We prefer talking with a bot already having browsed all wikipedia pages, and millions of authoritative and credible articles. This is the reason why we do need a Knowledge-Based (KB) Question Answering (QA) model as presented in [this paper][1].

![](https://github.com/hemina/ai_for_funds/blob/master/images/AI%20funds/key%20value%20prez.png)

How it works?

####Step 1. Knowledge bases and candidate answers building  
As shown in the figure above, before training the machine with Question-Answer dataset, we need to build the knowledge bases in the form of (key, value) pairs. The method of  knowledge bases construction comes to another big topic that we won't clearly explain in this report. Some specific methods could be found in this [paper][1]. For example, the title of a document is commonly the answer to a question that relates to the
text it contains. In this situation, we can consider the word window (some sentences in the document) as key, while the document title as value. Candidate answers could be document titles, name of specific entities, etc. The process of construction will be done during the browse of Wikipedia pages and finance-related articles.

Here is the list of potential reading ressources to be enriched:        

+ [Investing.com](<http://www.investing.com/>)
+ [Yahoo! Finance](<http://finance.yahoo.com/>)
+ [The Wall Street Journal](<http://www.wsj.com/europe>)
+ [Bloomberg](<https://www.bloomberg.com/europe>)
+ ...

####Step 2. Knowledge bases subset building   
Only when a question is submitted, we start to select a knowledge bases subset where all the (key, value) pairs are related to this question. Generally, we select those pairs where the key shares at least one word
with the question (ignoring the stopwords). This subset becomes the "Key-Value Memories" set as shown in the figure above.

####Step 3. Question/ key-value pair/ candidate embedding  
In order that the machine would be able to handle and calculate the text information, it's inevitable to convert all these texts into (multi-dimensional) vectors. There are many different ways to realize the word/sentence/document embedding, such as [LSTM (Long Short Term Memory) networks][4], [GRU (Gated Recurrent Unit) networks][5] based on [word2vec][6]/ [fasttext][7], or the simple [BoW (Bag-Of-Words)][1].

####Step 4. Key addressing and correlation degree computing  
After having re-dimensioned the question embedding vector, as semantics representation of the question, we calculate seperately the correlation degree between the question and each key in the knowledge bases subset. The correlation degree is a uniformed value in the 0-1 range, like a probability. 

####Step 5. Value reading and weighted summing  
Based on the previous step, we calculate the "sum vector" by summing all values in the (key, value) subset, weighted by the key correlation degree. This "sum vector" represents the question semantics enriched by the knowledge bases subset.

####Step 6. Question updating  
We replace the question embedding by the sum of the original embedding and the "sum vector" obtained by the previous step. 
The process between step 4 and step 6 may be recurrent for several times in order to make fully use of the knowledge bases subset and to auto-correct the semantics representation of the question.

####Step 7. Answer reasoning
With the final output of the previous step, we are currently able to calculate the correlation degree between the question and each candidate answer. It's possible to rank all these candidates and to take out the top-10 as potential answers. Or more easily, we regard the candidate with the maximum correlation degree as the best answer. 

### Secondary functionality
As explained above, the primary functionality of the bot "AI For Funds" is a Question-Answer system based on machine reading comprehesion. By discussing with potential users, many other functionalities are proposed to be developped for future versions.

####1). Entity Based Sentiment Analysis  
During the machine reading comprehension process, besides generating the (key, value) pairs for the knowledge bases construction, in the meantime, it's possible to do the Entity Based Sentiment Analysis, as shown in the figure below, provided by [IBM Watson™ AlchemyLanguage][8]. 

<div align=center> <img src="https://github.com/hemina/ai_for_funds/blob/master/images/AI%20funds/entity.png" width="80%" height="80%"> </div>

The result of Sentiment Analysis could be added into existing knowledge bases. Users could also ask questions about the Sentiment Analysis of particular entities, in which condition the bot sends back the detailed analysis result.

####2). Yes/No question Mode  
<div align=center> <img src="https://github.com/hemina/ai_for_funds/blob/master/images/AI%20funds/yesno.png" width="80%" height="80%"> </div>

In the previous Question-Answer system, candidate answers are either entities or meaningful sentences. We would like to develop a Yes/No question Mode, where the candidate answers are Yes/No. In this case, the Question-Answer problem can be converted into a binairy classification problem. This functionality could be used to verify the credibility/correctness of user's hypothesis. 

####3). Reliability control of knowledge bases  
<div align=center> <img src="https://github.com/hemina/ai_for_funds/blob/master/images/AI%20funds/stock-vector-reliability-level-conceptual-meter-indicate-hundred-percent-isolated-on-white-background-vector-195459728.jpg" width="50%" height="50%"> </div>

It is important to assure the reliability of the knowledge bases. To begin with, we only pick our source data from those official websites like [The Wall Street Journal](<http://www.wsj.com/europe>), assuming that all articles from these websites are credible. Then, to enlarge and refresh the vision of our reading machine, it's inevitable to do daily update on new reading materials. Before taking the new information into account, to update the knowledge bases or candidate answers, we need to control its credibility. This functionnality could probably be realized by applying the "Yes/No question Mode" verification process.

####4). Automatic Summarization Mode
<div align=center> <img src="https://github.com/hemina/ai_for_funds/blob/master/images/AI%20funds/summarization.jpg" width="60%" height="60%"> </div>

It happens very often, that we have a lot of documents to read, but we don't have enough time. We hope to ask someone to read for us, providing only the very condensed summary and keywords. 
The Automatic Summarization Mode of our bot is useful in this condition. Here is an [example of automatic summarization](<https://github.com/summanlp/textrank>), by applying the algorithm presented in [this paper][9]:
>**text**:  
"Automatic summarization is the process of reducing a text document with a
computer program in order to create a summary that retains the most important points
of the original document. As the problem of information overload has grown, and as
the quantity of data has increased, so has interest in automatic summarization.
Technologies that can make a coherent summary take into account variables such as
length, writing style and syntax. An example of the use of summarization technology
is search engines such as Google. Document summarization is another."

>**summary**:  
'Automatic summarization is the process of reducing a text document with a computer
program in order to create a summary that retains the most important points of the
original document.'

>**keywords**:   
document  
automatic   
summarization  
technologies  
technology

We would like to let the user choose, if the bot is authorized to memorize this document. If yes, our knowledge bases and candidate answers would be enriched with the content of this document. If no, there would not be any trace.

####5). Condensed Newsletters for subscribers
<div align=center> <img src="https://github.com/hemina/ai_for_funds/blob/master/images/AI%20funds/newsletter.jpg" width="60%" height="60%"> </div>

With the Automatic Summarization functionnality, the news summaries could be generated during daily update of the knowledge bases. It's possible to collect and organize these informations, into condensed newsletter, and send it to our subscribers every day. It would be even better if we are able to put the url link of the original sources as well as the condensed summary in the newsletter, so that our subscribers could easily reach a detailed article whenever they want.
####6). Customer Service Mode 
<div align=center> <img src="https://github.com/hemina/ai_for_funds/blob/master/images/AI%20funds/outsource-customer-service.jpg" width="60%" height="60%"> </div>

A customer service representative usually needs to answer the same question for many times. We would like to create a dataset of FAQ(Frequently Asked Questions) with detailed answers, and let our bot take the role of customer service representative. If the user is not satisfied with the bot's answer, we are going to prepare a serie of multiple choice questions, to help the user specify his question, then to help find a corresponding humain customer service representative.

####7). Image captioning and image retrieval
During the process of machine reading comprehension,  we encounter very often the images. If we skip all images, it's possible that we can't understand well the context. So we hope to add the functionnality of Image Captioning, where we translate the image into a sentence. Here are the samples generated by the algorithm of Image captioning presented in [this paper][10].
<div align=center> <img src="https://github.com/hemina/ai_for_funds/blob/master/images/AI%20funds/image%20capt.png" width="70%" height="70%"> </div>
On the other hand, when the bot answers the question, it may be more concrete and vivid to explain with image. This functionnality is accessible by applying the image retrieval algorithm as presented in [this paper][10]. In this case, images would also be put in the candidate answer set. 
<div align=center> <img src="https://github.com/hemina/ai_for_funds/blob/master/images/AI%20funds/retrieval.png" width="70%" height="70%"> </div>

####8). User feedback collection
At the end of the bot's answer, we would like to add a rating scale. If the feedback is positive, the bot will add this question-answer pair into our training set automatically. If it's negative, the bot will send this question-answer pair to our developer in order to notify as a problem. In this way, we are able to improve our user experience automatically step by step.

![](https://github.com/hemina/ai_for_funds/blob/master/images/AI%20funds/user-voice.png)

## 3. Potential problems
In the text above, we have explained what we can do and how we can do it with our intelligent bot. In this part, we are going to talk about the potential obstacles of this project.
###1). Uncertainty about reaction time
During the humain-machine interaction, each question will be put into the recurrent neural network for finding a most appropriate answer. This may take quite a long time, conducting a damage of user experience. An optimised algorithm is needed in order to reduce the reaction time.
###2). Limited accuracy
According to the Question-Answering papers mentioned above, the accuracy is around 70% for the state-of-art. Users may not be satisfied with the current situation, but there is no reason that we stop carrying it out. We firmly believe that there is a boost of performance in the near future. 
###3). Possibility of key information loss
At present, most of the researches about Natural Language Processing are concentrated on the language itself, ignoring the attached information such as the number, mathematics formula, image, url, etc. However, in the financial domain, the key information may be exactly contained in these attached information. So it's possible to lose the key information if we stick to the traditionnal pre-processing step, which eliminates all the attached information. How to clean the original text data without eliminating the other forms of information remains a question.
###4). Difficulties of training set and candidate answer set building
It's indispensable to generate a training set, where there are enough question-answer pairs related to our users' concerns, and a candidate answer set, which is supposed to include all the potential answers to users' questions. 

As for the training set, here is a list of potential question-answer sources to be enriched:

* CFA(Chartered Financial Analyst) test samples ([here](https://www.cfainstitute.org/programs/cfaprogram/courseofstudy/Documents/sample_level_I_questions.pdf) is an example)
* Qualified discussions on financial forums ([here](http://www.savingadvice.com/forums/investing-banking/84708-what-your-investment-ideas-strategies-2017-a.html) is an example)
* Financial advisor interview ([here](https://www.livecareer.com/interview-questions/financial-advisor-interview-questions-and-answers) is an example)
* ...

As for the candidate answer set, here is a list of sources to be enriched:

* All answers in the training set
* All entities detected in the training set and knowledge bases
* All titles of articles 
* All summaries of articles digested during the machine reading comprehension procedure(only if the Automatic Summarization Mode is implemented)
* ...

Undoubtedly, the process of building training set and candidate answer set will take a lot of time, scrapping and cleaning the data, and we can hardly figure out a perfect dataset which adapts to all kinds of questions. That's why we do need an automatic update mechanism, which takes in the positive user feedback, and throw the negative feedback to developers for error analysis.

## 4. Conclusion
We studied an application innovation, applying the Natural Language Processing technologies to the financial domain, to construct an intelligent bot with a Knowledge-Based Question Answering model. Many other functionnalities, such as Entity Based Sentiment Analysis, Automatic Summarization, Image captioning and image retrieval, are proposed to improve the user experience and to reinforce the robustness of our model. Not all these functionnalities are applicable since each are big subject and require a lot of work behind. A part of potential problems to be resolved are listed. Despite all these difficulties, we hope to make all our efforts to bring this whole project come true. 

[1]:<https://arxiv.org/pdf/1606.03126v2.pdf> "Key-Value Memory Networks for Directly Reading Documents"

[2]:<https://arxiv.org/pdf/1503.08895v5.pdf> "End-To-End Memory Networks"

[3]:<https://arxiv.org/pdf/1608.07775v2.pdf> "HIERARCHICAL ATTENTION MODEL FOR IMPROVED MACHINE COMPREHENSION OF SPOKEN CONTENT"

[4]:<https://arxiv.org/pdf/1506.03340v3.pdf> "Karl Moritz Hermann, Tomas Kocisky, Edward Grefenstette, Lasse Espeholt, Will Kay, Mustafa Suleyman,and Phil Blunsom. 2015. Teaching machines to read and comprehend. In Proc. of NIPS, pages 1684–1692."

[5]:<https://arxiv.org/pdf/1603.01547v2.pdf> "Rudolf Kadlec, Martin Schmid, Ondrej Bajgar, andJan Kleindienst. 2016. Text understanding with the attention sum reader network. tarXiv:1603.01547."

[6]:<https://papers.nips.cc/paper/5021-distributed-representations-of-words-and-phrases-and-their-compositionality.pdf> "Distributed Representations of Words and Phrases and their Compositionality"

[7]:<https://arxiv.org/pdf/1607.04606v1.pdf> "Enriching Word Vectors with Subword Information"

[8]:<https://alchemy-language-demo.mybluemix.net/> "IBM Watson™ AlchemyLanguage"

[9]:<https://arxiv.org/pdf/1602.03606v1.pdf> "Variations of the Similarity Function of TextRank for Automated Summarization"

[10]:<https://arxiv.org/pdf/1604.00790v3.pdf>
"Image Captioning with Deep Bidirectional LSTMs"

