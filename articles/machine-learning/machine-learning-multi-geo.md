<properties
   pageTitle="複数の地理的リージョンに関するヘルプ ドキュメント | Microsoft Azure"
   description="ワークスペースを作成し、米国中南部 (SCUS) Azure リージョン以外の Azure リージョンに Web サービスを発行する方法について学習します。"
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="paulettm"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2015"
   ms.author="tedway; neerajkh"/>

# 複数の地理的リージョンに関するヘルプ ドキュメント

現在、Azure Machine Learning の資産はすべて米国中南部 (SCUS) Azure リージョンにホストされています。この記事では、ワークスペースを作成し、その他の Azure リージョンに Web サービスを発行する方法について説明します。

## ワークスペースの作成

1. Azure 管理ポータルにサインインします。

2.  **[+新規]**、**[Data Services]**、**[Machine Learning]**、**[簡易作成]** の順にクリックします。**[場所]** で、**[東南アジア]** など別のリージョンを選択します。![Multi-Geo Help image 1][1]
3. ワークスペースを選択し、**[ML Studio にサインイン]** をクリックします。![Multi-Geo Help image 2][2]

4. これで別のリージョンにワークスペースが作成され、他のワークスペースと同じように使用できます。ワークスペースは画面の右上で切り替えます。ドロップダウンをクリックし、リージョンを選択して、ワークスペースを選択します。すべてのものはワークスペース リージョンのローカルに置かれます。たとえば、ワークスペースから作成されたすべての Web サービスは、ワークスペースと同じリージョンに置かれます。![Multi-Geo Help image 3][3]

## ギャラリーから実験を開く

ギャラリーから実験を開く場合、その実験をコピーするリージョンを選択することもできます。

![Multi-Geo Help image 4][4a]

## Web サービスの管理

再トレーニング用のサービスなどの Web サービスをプログラムによって管理するには、リージョン固有のアドレスを使用します。****https://asiasoutheast.management.azureml.net**

### 注意する点

1.	同じリージョンに属しているワークスペース間でのみ実験をコピーするこができます。将来的には、複数のリージョンにわたるワークスペース間で実験をコピーできるようにする予定です。
2.	リージョン セレクターには、一度に 1 つのリージョンのワークスペースのみが表示されます。将来的には、ユーザーがアクセスできるすべてのリージョンにあるワークスペースを同時に表示できるようにする予定です。  
3.	フリー ワークスペース、またはゲスト アクセス (匿名の) ワークスペースは米国中南部に作成およびホストされます。将来的には、フリー ワークスペースとゲスト アクセス ワークスペースをお好きなリージョンに作成できるようにする予定です。  
4.	東南アジアのワークスペースからデプロイされた Web サービスは、東南アジアにもホストされます。将来的には、1 つのリージョンで実験を作成し、生成された Web サービス エンドポイントを別のリージョンに柔軟にデプロイできるようにする予定です。  

## 詳細情報

質問がある場合は [Azure Machine Learning のフォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning)に投稿してください。

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png

<!---HONumber=August15_HO8-->