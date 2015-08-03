<properties 
	pageTitle="Machine Learning で Web サービス エンドポイントを作成する | Microsoft Azure" 
	description="Azure Machine Learning で Web サービス エンドポイントを作成する" 
	services="machine-learning" 
	documentationCenter="" 
	authors="hiteshmadan" 
	manager="padou" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="04/21/2015"
	ms.author="himad"/>


# エンドポイントを作成する

Azure Machine Learning では、発行された Web サービスに対して複数のエンドポイントを作成できます。各エンドポイントは、その Web サービスの他のエンドポイントとは別に、アドレス指定、スロットル、管理が個別に実行されます。各エンドポイントに対して、一意の URL と承認キーがあります。

これにより、Azure Machine Learning のユーザーが、お客様に先物販売をすることができる Web サービスを作成できます。各エンドポイントは、独自のトレーニング済みのモデルを使用して個別にカスタマイズできますが、この Web サービスを作成した実験へのリンクは維持されます。さらに、実験の更新は選択的にエンドポイントに適用でき、カスタマイズが上書きされることはありません。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## エンドポイントの作成手順
- [http://manage.windowsazure.com](http://manage.windowsazure.com) を開き、左側の列の **Machine Learning** をクリックします。関心のある Web サービスが含まれているワークスペースをクリックします。![ワークスペースに移動する](./media/machine-learning-create-endpoint/figure-1.png)


- **[Web サービス]** タブをクリックします。![Web サービスに移動する](./media/machine-learning-create-endpoint/figure-2.png)


- 関心がある Web サービスをクリックして、利用可能なエンドポイントのリストを表示します。![エンドポイントに移動する](./media/machine-learning-create-endpoint/figure-3.png)


- 下部の **[エンドポイントの追加]** ボタンをクリックします。名前と説明を入力して、この Web サービスの他のエンドポイントに同じ名前がないことを確認します。特別な要件がない限り、調整レベルは既定値のままにします。調整の詳細については、「[API エンドポイントのスケーリング](machine-learning-scaling-endpoints.md)」をご覧ください。![エンドポイントを作成する](./media/machine-learning-create-endpoint/figure-4.png)


エンドポイントが作成されたら、同期 API、バッチ API、および Excel のワークシートによってそれを使用できます。Machine Learning Web サービスの使用については、「[発行済みの Azure Machine Learning Web サービスを使用する方法](machine-learning-consume-web-services.md)」をご覧ください。
 

<!---HONumber=July15_HO4-->