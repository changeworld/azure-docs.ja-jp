---
title: Web サービス エンドポイントを作成する - Azure Machine Learning Studio | Microsoft Docs
description: Azure Machine Learning で Web サービス エンドポイントを作成する。 Web サービスの各エンドポイントは、個別にアドレス指定、スロットル、および管理されます。
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.component: studio
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.openlocfilehash: 58418357d6e96204485887cca5d7724c42183db7
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275892"
---
# <a name="creating-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>デプロイされた Azure Machine Learning Studio Web サービスのエンドポイントを作成する
> [!NOTE]
>  このトピックでは、**従来**の Machine Learning Web サービスに適用できる手法について説明します。
> 
> 

お客様に先行して販売する Web サービスを作成するときは、それぞれのお客様に対して、Web サービスを作成した実験へのリンクが維持されているトレーニング済みのモデルを提供する必要があります。 さらに、実験に対する更新を、カスタマイズを上書きすることなく選択的にエンドポイントに適用する必要があります。

そのために、Azure Machine Learning Studio では、デプロイした Web サービスに対して複数のエンドポイントを作成できます。 Web サービスの各エンドポイントは、個別にアドレス指定、スロットル、および管理されます。 各エンドポイントは一意の URL と承認キーであり、お客様に配布することができます。



## <a name="adding-endpoints-to-a-web-service"></a>Web サービスにエンドポイントを追加する
エンドポイントを Web サービスに追加する方法は 2 つあります。

* プログラムを使用する
* Azure Machine Learning Web サービス ポータルを使用する

エンドポイントが作成されたら、同期 API、バッチ API、および Excel のワークシートによってそれを使用できます。 この UI によるエンドポイントの追加に加え、エンドポイントは Endpoint Management API を使用してプログラムで追加することもできます。

> [!NOTE]
> Web サービスにエンドポイントを追加した場合、既定のエンドポイントを削除することはできません。
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>プログラムを使用してエンドポイントを追加する
プログラムによって Web サービスにエンドポイントを追加するには、[AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) サンプル コードを使用します。

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Azure Machine Learning Web サービス ポータルを使用してエンドポイントを追加する
1. Machine Learning Studio の左側のナビゲーション列で [Web サービス] をクリックします。
2. Web サービス ダッシュボードの下部で、**[Manage endpoints (エンドポイントの管理)]** をクリックします。 Azure Machine Learning Web サービス ポータルが Web サービスのエンドポイント ページに表示されます。
3. **[新規]** をクリックします。
4. 新しいエンドポイントの名前と説明を入力します。 エンドポイントの名前は、長さが 24 文字以内で、小文字のアルファベットまたは数字で構成されている必要があります。 ログ レベルとサンプル データが有効になっているかどうかを選択します。 ログ記録の詳細については、「[Machine Learning Web サービスのログ記録の有効化](web-services-logging.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
[Azure Machine Learning Web サービスを使用する方法](consume-web-services.md)。

