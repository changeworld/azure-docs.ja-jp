<properties 
	pageTitle="API エンドポイントのスケーリング | Microsoft Azure" 
	description="Azure Machine Learning で Web サービス エンドポイントのスケーリングを行う" 
	services="machine-learning"
	documentationCenter="" 
	authors="hiteshmadan" 
	manager="padou" 
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="06/29/2015"
	ms.author="himad"/>


# API エンドポイントのスケーリング

Azure Machine Learning の Web サービス エンドポイントでは、スロットル レベルが選択可能であり、エンドポイントが使用するレートに合わせることができます。

エンドポイントでのスロットルの量を制御するには、Microsoft Azure 管理ポータルのスライダーを使用して、同時呼び出しの最大数を 20 ～ 200 の範囲で設定します。


同期 API は通常、短い待機時間が望まれる状況で使用されます。ここでの待機時間とは、API が 1 つの要求を完了するのにかかる時間を指し、ネットワークの遅延に影響しないものです。たとえば待機時間が 50 ms の API があるとします。スロットル レベル "高" で同時呼び出しの最大数が 20 件のときに使用可能な容量をすべて使用するには、この API を 1 秒あたり 20 × 1000 / 50 = 400 回呼び出す必要があります。さらに増やして、同時呼び出しの最大数 200 件にすると、API を 1 秒あたり 4,000 回呼び出すことができ、この場合の待機時間は 50 ms と想定されます。

同時呼び出しの最大数 200 件でサポートされる内容を上回る負荷の API を呼び出す場合は、同じ Web サービスに複数のエンドポイントを作成し、それら全体で負荷を無作為に分配する必要があります。

同時呼び出しの数を非常に大きくする場合、これに相当する高いレートで API を動作させないと悪影響が出ることがありますのでご注意ください。高い負荷で構成された API に比較的低い負荷を割り当てた場合、不規則なタイムアウトや待機時間の急増が発生することがあります。

スロットルの設定調整は、同期 API (RRS) の動作にのみ影響します。同期 API で "503 サービスを利用できません" の応答が頻繁に表示される場合、これらの設定を調整してください。

管理 UI を使用すると、同時呼び出し数を既定値の 20 より増やしてエンドポイントを拡張できます。

1. manage.windowsazure.com を開きます。
2. [Machine Learning] タブに移動します。
3. ワークスペースをクリックします。
4. エンドポイントが含まれる Web サービスに移動します。![Web サービスに移動する](./media/machine-learning-scaling-endpoints/figure-1.png)

5. エンドポイントをクリックした後、[構成] タブをクリックします。![エンドポイントの構成に移動する](./media/machine-learning-scaling-webservice/machlearn-2.png)


6. スライダーを変更して同時呼び出しのレベルを上げ、[保存] をクリックします。


 

<!---HONumber=Nov15_HO4-->