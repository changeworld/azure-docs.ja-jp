<properties
   pageTitle="Web サービスのスケーリング | Microsoft Azure"
   description="同時実行を増やし、新しいエンドポイントを追加して Web サービスを拡大する方法について説明します。"
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="Azure Machine Learning、Web サービス、運用、スケーリング、エンドポイント、同時実行"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="07/06/2016"
   ms.author="neerajkh"/>

# Web サービスのスケーリング

>[AZURE.NOTE] このトピックでは、従来の Web サービスに適用できる手法について説明します。

既定では、発行される Web サービスはそれぞれ、20 件の同時実行要求をサポートするように構成されますが、200 件までの同時実行要求をサポートするように構成できます。Azure クラシック ポータルではこの値を設定する方法が提供されますが、Azure Machine Learning では Web サービスのベスト パフォーマンスを提供するようにこの値が自動的に最適化され、ポータルの値は無視されます。

同時呼び出しの最大数 200 件でサポートされる内容を上回る負荷の API を呼び出す場合は、同じ Web サービスに複数のエンドポイントを作成し、それら全体で負荷を無作為に分配する必要があります。

## 同じ Web サービスの新しいエンドポイントを追加する

200 超の同時実行要求をサポートするか、複数のエンドポイントで可用性を上げるか、Web サービスの利用者ごとにエンドポイントを提供するとき、共通のタスクとして Web サービスのスケーリングが行われます。このスケールを大きくするには、次の図に示すように [Azure クラシック ポータル](https://manage.windowsazure.com/)から同じ Web サービスに追加のエンドポイントを追加します。

同時呼び出しの数を非常に大きくする場合、これに相当する高いレートで API を動作させないと悪影響が出ることがありますのでご注意ください。高い負荷で構成された API に比較的低い負荷を割り当てた場合、不規則なタイムアウトや待機時間の急増が発生することがあります。

同期 API は通常、短い待機時間が望まれる状況で使用されます。ここでの待機時間とは、API が 1 つの要求を完了するのにかかる時間を指し、ネットワークの遅延に影響しないものです。たとえば待機時間が 50 ms の API があるとします。スロットル レベル "高" で同時呼び出しの最大数が 20 件のときに使用可能な容量をすべて使用するには、この API を 1 秒あたり 20 × 1000 / 50 = 400 回呼び出す必要があります。さらに増やして、同時呼び出しの最大数 200 件にすると、API を 1 秒あたり 4,000 回呼び出すことができ、この場合の待機時間は 50 ms と想定されます。

[Microsoft Azure クラシック ポータル](https://manage.windowsazure.com/)に進み、左にある Machine Learning アイコンをクリックし、Web サービスの発行に使用する作業空間を選択し、Web サービスをクリックし、下部パネルの **[エンドポイントの追加]** をクリックし、新しいエンドポイントの名前、説明、同時実行数を指定します。

新しいエンドポイントを追加する方法については、「[エンドポイントの作成](machine-learning-create-endpoint.md)」を参照してください。

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=AcomDC_0720_2016-->