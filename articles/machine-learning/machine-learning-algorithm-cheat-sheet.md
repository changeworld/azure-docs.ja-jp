<properties
	pageTitle="機械学習アルゴリズム チート シート | Microsoft Azure"
	description="印刷可能な機械学習アルゴリズム チート シートは、Azure Machine Learning Studio で予測モデルに適したアルゴリズムを選択するのに役立ちます。"
	keywords="アルゴリズム チート シート、チート シート、Machine Learning アルゴリズム"
	services="machine-learning"
	documentationCenter=""
	authors="brohrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/10/2016"
	ms.author="brohrer;garye" />


# Microsoft Azure Machine Learning Studio の機械学習アルゴリズム チート シート

**Microsoft Azure Machine Learning のアルゴリズム チート シート**を使用すると、Microsoft Azure Machine Learning のアルゴリズム ライブラリから、予測分析ソリューションに適した機械学習アルゴリズムを選択できます。

[Azure Machine Learning Studio](https://studio.azureml.net/) には、予測分析ソリューションの構築に使用できる多くの機械学習アルゴリズムが用意されています。これらのアルゴリズムは、一般的な機械学習のカテゴリ (***回帰***、***分類***、***クラスタリング***、***異常検出***) に分類され、それぞれが別の種類の機械学習の問題に対応するように設計されています。

> [AZURE.NOTE] このチート シートを使用する詳細なガイドについては、「[Microsoft Azure Machine Learning のアルゴリズムを選択する方法](machine-learning-algorithm-choice.md)」をご覧ください。

## 機械学習アルゴリズム チート シートのダウンロード

Machine Learning アルゴリズム チート シートをダウンロードし、ソリューションに適した機械学習アルゴリズムの選択方法を確認するのに役立てます。チート シートをタブロイド サイズ (11 x 17 インチ) で印刷し、手元に置いておくことができます。

**チート シートをダウンロードする: [Microsoft Azure Machine Learning Algorithm Cheat Sheet](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Machine Learning Algorithm Cheat Sheet : Machine Learning アルゴリズムの選択方法][cheat-sheet]

[cheat-sheet]: ./media/machine-learning-algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png


## アルゴリズムに関するその他のヘルプ

* さまざまな種類の機械学習アルゴリズムの詳細、その使用方法、このチート シートを使用して適切なアルゴリズムを選択する方法については、「[Microsoft Azure Machine Learning のアルゴリズムを選択する方法](machine-learning-algorithm-choice.md)」を参照してください。
* Machine Learning Studio で使用可能な機械学習アルゴリズムのカテゴリ別の一覧については、Machine Learning Studio アルゴリズムとモジュールのヘルプの[モデルの初期化][initialize-model]に関するページを参照してください。
* Machine Learning Studio で使用可能なすべてのアルゴリズムとモジュールの一覧については、Machine Learning Studio アルゴリズムとモジュールのヘルプの「[Machine Learning Studio モジュールの一覧 (アルファベット順)][a-z-list]」を参照してください。
* Machine Learning Studio の機能の概要を示す図をダウンロードして印刷する場合は、「[Azure Machine Learning Studio 機能の概要図](machine-learning-studio-overview-diagram.md)」参照してください。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- This needs to be updated based on the new Choosing and Algorithm article

## Notes and terminology definitions for the machine learning algorithm cheat sheet

* The suggestions offered in this algorithm cheat sheet are approximate rules-of-thumb. Some can be bent, and some can be flagrantly violated. This is intended to suggest a starting point. Don’t be afraid run a head-to-head competition between several algorithms on your data. There is simply no substitute for understanding the principles of each algorithm and understanding the system that generated your data.

* Every machine learning algorithm has its own style or *inductive bias*. For a specific problem, several algorithms may be appropriate and one algorithm may be a better fit than others. But knowing which will be the best fit beforehand is not always possible. In cases like these, several algorithms are listed together in the cheat sheet. An appropriate strategy would be to try one algorithm, and if the results are not yet satisfactory, try the others. Here’s an example from the [Cortana Analytics Gallery](http://gallery.azureml.net/) of an experiment that tries several algorithms against the same data and compares the results: [Compare Multi-class Classifiers: Letter recognition](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).

* There are three main categories of machine learning: **supervised learning**, **unsupervised learning**, and **reinforcement learning**.

  * In **supervised learning**, each data point is labeled or associated with a category or value of interest.  An example of a categorical label is assigning an image as either a ‘cat’ or a ‘dog’.  An example of a value label is the sale price associated with a used car. The goal of supervised learning is to study many labeled examples like these, and then to be able to make predictions about future data points - for example, to identify new photos with the correct animal or to assign accurate sale prices to other used cars. This is a popular and useful type of machine learning. All of the modules in Azure Machine Learning are supervised learning algorithms except for [K-Means Clustering][k-means-clustering].

  * In **unsupervised learning**, data points have no labels associated with them. Instead, the goal of an unsupervised learning algorithm is to organize the data in some way or to describe its structure. This can mean grouping it into clusters, as K-means does, or finding different ways of looking at complex data so that it appears simpler.

  * In **reinforcement learning**, the algorithm gets to choose an action in response to each data point. It is a common approach in robotics, where the set of sensor readings at one point in time is a data point, and the algorithm must choose the robot’s next action. It's also a natural fit for Internet of Things applications. The learning algorithm also receives a reward signal a short time later, indicating how good the decision was. Based on this, the algorithm modifies its strategy in order to achieve the highest reward. Currently there are no reinforcement learning algorithm modules in Azure ML.

* **Bayesian methods** make the assumption of statistically independent data points. This means that the unmodeled variability in one data point is uncorrelated with others, that is, it can’t be predicted. For example, if the data being recorded is the number of minutes until the next subway train arrives, two measurements taken a day apart are statistically independent. However, two measurements taken a minute apart are not statistically independent - the value of one is highly predictive of the value of the other.

* **Boosted decision tree regression** takes advantage of feature overlap or interaction among features. That means that, in any given data point, the value of one feature is somewhat predictive of the value of another. For example, in daily high/low temperature data, knowing the low temperature for the day allows you to make a reasonable guess at the high. The information contained in the two features is somewhat redundant.

* Classifying data into more than two categories can be done by either using an inherently multi-class classifier, or by combining a set of two-class classifiers into an **ensemble**. In the ensemble approach, there is a separate two-class classifier for each class - each one separates the data into two categories:  “this class” and “not this class.” Then these classifiers vote on the correct assignment of the data point. This is the operational principle behind [One-vs-All Multiclass][one-vs-all-multiclass].

* Several methods, including logistic regression and the Bayes point machine, assume **linear class boundaries**, that is, that the boundaries between classes are approximately straight lines (or hyperplanes in the more general case). Often this is a characteristic of the data that you don’t know until after you’ve tried to separate it, but it’s something that typically can be learned by visualizing beforehand. If the class boundaries look very irregular, stick with decision trees, decision jungles, support vector machines, or neural networks.

* Neural networks can be used with categorical variables by creating a **dummy variable** for each category and setting it to 1 in cases where the category applies, 0 where it doesn’t.

-->

<!-- This is how you can add a link to the image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-content-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
--> \\

<!-- Module References -->
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/

<!---HONumber=AcomDC_0211_2016-->