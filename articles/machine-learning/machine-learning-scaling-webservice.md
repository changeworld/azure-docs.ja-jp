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
   ms.date="05/16/2016"
   ms.author="neerajkh"/>

# Web サービスのスケーリング

## 同時実行を増やす

既定では、発行される Web サービスはそれぞれ、20 件の同時実行要求をサポートするように構成されます。下の図のように、[Microsoft Azure クラシック ポータル](https://manage.windowsazure.com/)でこの同時実行要求を 200 に増やすことができます。

[Microsoft Azure クラシック ポータル](https://manage.windowsazure.com/)に進み、左にある Machine Learning アイコンをクリックし、Web サービスの発行に使用する作業空間を選択し、Web サービスをクリックし、同時実行を増やすエンドポイントを選択し、**[構成]** をクリックします。スライダーを使用して同時実行を増やし、下部パネルにある **[保存]** をクリックします。

同時実行を増やす方法については、「[API エンドポイントのスケーリング](machine-learning-scaling-endpoints.md)」を参照してください。

   ![Machine Learning、エンドポイントの拡大。][1]

## 同じ Web サービスの新しいエンドポイントを追加する

200 超の同時実行要求をサポートするか、複数のエンドポイントで可用性を上げるか、Web サービスの利用者ごとにエンドポイントを提供するとき、共通のタスクとして Web サービスのスケーリングが行われます。同じ Web サービスのエンドポイントを追加することで規模を拡大できます。下の図のように、[Microsoft Azure クラシック ポータル](https://manage.windowsazure.com/)でエンドポイントを追加できます。

[Microsoft Azure クラシック ポータル](https://manage.windowsazure.com/)に進み、左にある Machine Learning アイコンをクリックし、Web サービスの発行に使用する作業空間を選択し、Web サービスをクリックし、下部パネルの **[エンドポイントの追加]** をクリックし、新しいエンドポイントの名前、説明、同時実行数を指定します。

新しいエンドポイントを追加する方法については、「[エンドポイントの作成](machine-learning-create-endpoint.md)」を参照してください。

   ![Machine Learning、新しいエンドポイントの追加。][2]

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=AcomDC_0525_2016-->