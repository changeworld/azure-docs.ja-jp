---
title: "Web サービスのスケーリング | Microsoft Docs"
description: "同時実行を増やし、新しいエンドポイントを追加して Web サービスを拡大する方法について説明します。"
services: machine-learning
documentationcenter: 
author: neerajkh
manager: srikants
editor: cgronlun
keywords: "Azure Machine Learning、Web サービス、運用、スケーリング、エンドポイント、同時実行"
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/05/2016
ms.author: neerajkh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 559db01d2c9f44af17b86f99031a8827dfeb5ab3


---
# <a name="scaling-a-web-service"></a>Web サービスのスケーリング
> [!NOTE]
> このトピックでは、従来の Machine Learning Web サービスに適用できる手法について説明します。 
> 
> 

既定では、発行される Web サービスはそれぞれ、20 件の同時実行要求をサポートするように構成されますが、200 件までの同時実行要求をサポートするように構成できます。 Azure クラシック ポータルではこの値を設定する方法が提供されますが、Azure Machine Learning では Web サービスのベスト パフォーマンスを提供するようにこの値が自動的に最適化され、ポータルの値は無視されます。 

同時呼び出しの最大数 200 件でサポートされる内容を上回る負荷の API を呼び出す場合は、同じ Web サービスに複数のエンドポイントを作成する必要があります。 その後、それら全体に負荷を無作為に分配できます。

## <a name="add-new-endpoints-for-same-web-service"></a>同じ Web サービスの新しいエンドポイントを追加する
Web サービスのスケーリングは一般的なタスクです。 スケーリングの理由としてあげられるのは、200 件を越える同時要求のサポート、複数のエンドポイントによる可用性の向上、または Web サービス用の個別エンドポイントの提供です。 スケールを大きくするには、次の図に示すように [Azure クラシック ポータル](https://manage.windowsazure.com/)または [Azure Machine Learning Web サービス](https://services.azureml.net/) ポータルから同じ Web サービスに追加のエンドポイントを追加します。

新たなエンドポイントの追加の詳細については、「[エンドポイントを作成する](machine-learning-create-endpoint.md)」をご覧ください。

同時呼び出しの数を増やす場合、これに相当する高いレートで API を呼び出さないと悪影響が出ることがありますのでご注意ください。 高い負荷で構成された API に比較的低い負荷を割り当てた場合、不規則なタイムアウトや待機時間の急増が発生することがあります。

同期 API は通常、短い待機時間が望まれる状況で使用されます。 ここでの待機時間とは、API が 1 つの要求を完了するのにかかる時間を指し、ネットワークの遅延に影響しないものです。 たとえば待機時間が 50 ms の API があるとします。 スロットル レベル "高" で同時呼び出しの最大数が 20 件のときに使用可能な容量をすべて使用するには、この API を 1 秒あたり 20 × 1000 / 50 = 400 回呼び出す必要があります。 さらに増やして、同時呼び出しの最大数 200 件にすると、API を 1 秒あたり 4,000 回呼び出すことができ、この場合の待機時間は 50 ms と想定されます。

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png



<!--HONumber=Nov16_HO3-->


