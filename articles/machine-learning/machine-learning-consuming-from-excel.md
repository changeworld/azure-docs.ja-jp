<properties 
	pageTitle="Excel からの Machine Learning Web サービスの使用 | Microsoft Azure" 
	description="Excel からの Azure Machine Learning Web サービスの使用" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="tedway"/>


# Excel からの Azure Machine Learning Web サービスの使用 #

 Azure Machine Learning Studio では、Excel から直接 Web サービスを簡単に呼び出すことができ、コードを作成する必要がありません。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 手順

1. Web サービスを発行します。[このページ](machine-learning-walkthrough-5-publish-web-service.md)では、その方法を説明しています。現在、Excel ブック機能は、1 つの出力を持つ要求/応答サービス (つまり 1 つのスコア付けラベル) のみサポートしています。 
 
2. Web サービスを作成したら、Studio の左側の **[WEB サービス]** セクションをクリックして、Excel から使用する Web サービスを選択します。

3. Web サービスの **[ダッシュボード]** タブに、**要求/応答**サービスの行があります。このサービスに 1 つの出力がある場合は、その行の **[Excel ブックのダウンロード]** のリンクを確認してください。

	![][1]
 
4. **[Excel ブックのダウンロード]** をクリックして、Excel で開きます。

5. セキュリティの警告が表示されます。**[編集を有効にする]** ボタンをクリックします。

	![][2]

6. セキュリティの警告が表示されます。**[コンテンツの有効化]** ボタンをクリックして、スプレッドシートのマクロを実行します。

	![][3]
 
7. マクロが有効になると、テーブルが生成されます。青の列は RRS Web サービス、または **PARAMETERS** への入力として必要です。RRS サービス **PREDICTED VALUES** の出力は緑色で表示されます。特定の行のすべての列がいっぱいになると、ブックは自動的に、スコア付け API を呼び出し、スコア付けされた結果を表示します。

	![][4]

7. 複数の行のスコア付けを実行するには、2 番目の行にデータを入力すると、予測値が生成されます。複数の行を一度に貼り付けることもできます。

8. これで、Excel 機能 (グラフ、Power Map、条件付き書式など) と予測値を使用できます。


## ブックを共有する

マクロが機能するには、ACCESS KEY がスプレッドシートの一部である必要があります。つまり、信頼するエンティティ/個人とだけ、ブックを共有する必要があります。

## 自動更新

RRS 呼び出しは、次の 2 つの状況で発生します。

1. 初めて行のすべての **PARAMETERS** にコンテンツが挿入されたとき
    
2. すべての **PARAMETERS** に入力されている行の任意の **PARAMETERS** が変更されたとき。

[1]: ./media/machine-learning-consuming-from-excel/excellink.png
[2]: ./media/machine-learning-consuming-from-excel/enableeditting.png
[3]: ./media/machine-learning-consuming-from-excel/enablecontent.png
[4]: ./media/machine-learning-consuming-from-excel/sampletable.png
 

<!---HONumber=July15_HO4-->