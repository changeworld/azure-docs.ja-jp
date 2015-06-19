<properties 
	pageTitle="エンドポイントを作成する" 
	description="Azure Machine Learning で Web サービス エンドポイントを作成する" 
	services="machine-learning" 
	authors="hiteshmadan" 
	manager="padou" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="02/19/2015"
	ms.author="himad"/>


# エンドポイントを作成する

Azure Machine Learning では、発行された Web サービスに対して複数のエンドポイントを作成できます。各エンドポイントは、その Web サービスの他のエンドポイントとは別に、アドレス指定、スロットル、管理が個別に実行されます。各エンドポイントに対して、一意の URL と承認キーがあります。

これにより、Azure Machine Learning のユーザーが、お客様に先物販売をすることができる Web サービスを作成できます。各エンドポイントは、独自のトレーニング済みのモデルを使用して個別にカスタマイズできますが、この Web サービスを作成した実験へのリンクは維持されます。さらに、実験の更新は選択的にエンドポイントに適用でき、カスタマイズが上書きされることはありません。

## エンドポイントの作成手順
- manage.windowsazure.com を開き、左側の列の Machine Learning  をクリックします。関心のある Web サービスを持つワークスペースをクリックします。
![Navigate to workspace](./media/machine-learning-create-endpoint/figure-1.png)


- [Web サービス] タブをクリックします。
![Navigate to web services](./media/machine-learning-create-endpoint/figure-2.png)


- 関心がある Web サービスをクリックして、利用可能なエンドポイントのリストを表示します。
![Navigate to endpoint](./media/machine-learning-create-endpoint/figure-3.png)


- [エンドポイントの追加] ボタンをクリックします。名前と説明を入力して、この Web サービスの他のエンドポイントに同じ名前がないことを確認します。特別な要件がない限り、スロットル レベルは既定値のままにします。
スロットル詳細については、[API エンドポイントの拡大/縮小](machine-learning-scaling-endpoints.md)ページを参照してください。
![Create endpoint](./media/machine-learning-create-endpoint/figure-4.png)


エンドポイントが作成されたら、同期 API、バッチ API、および Excel のワークシートによってそれを使用できます。

<!--HONumber=49--> 