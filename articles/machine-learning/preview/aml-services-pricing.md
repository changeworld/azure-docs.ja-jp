---
title: "Azure Machine Learning サービスの価格と課金"
description: "この記事では、Azure Machine Learning のプレビュー機能についてよく寄せられる、価格と課金に関する質問について説明します"
services: machine-learning
author: j-martens
ms.author: jmartens
manager: cgronlund
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 03/01/2018
ms.openlocfilehash: 756295e1b237e4b638effbac084ecd891f3f2d21
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="pricing-and-billing-for-azure-machine-learning-services"></a>Azure Machine Learning サービスの価格と課金

詳細および請求書サンプルについては、[Azure の価格](https://azure.microsoft.com/pricing/details/machine-learning-services/)に関するページをご覧ください。  

課金と価格に関してよく寄せられる質問を、次にいくつか示します。

**Azure Machine Learning のプレビュー期間中に課金は行われますか?** 

Azure サブスクライバーであれば、Azure Machine Learning Workbench アプリケーションは無料で使用できます。

実験サービスとモデル管理には、有料レベルに加えて、無料レベルが用意されており、パブリック プレビュー期間中は割引料金でご利用いただけます。

**課金は実験の実行回数に応じて行われますか?**

いいえ。実験サービスでは実験を必要なだけ実行することができ、課金はユーザーの数のみに応じて行われます。 実験のコンピューティング リソースについては別途課金されます。  複数の実験を行い、お客様のソリューションに最適なモデルをお探しください。 

**課金は Web サービスの呼び出し回数に応じて行われますか?**


いいえ。 Web サービスは必要な回数だけ呼び出すことが可能であり、モデル管理の課金に影響は及びません。 アプリケーションのニーズに合わせて、デプロイのスケールを完全に自由に調整できます。

**Azure Machine Learning モデル管理で購入済みのユニット数を増減させるにはどうすればよいですか?**

ユニット数の増減は、Azure Portal または CLI のいずれかから行うことができます。 

**請求書はどのように作成されますか?**

請求書は毎日作成されます。 課金額の計算における 1 日は、UTC の午前 0 時に開始します。 請求書は月単位で生成されます。 Azure Machine Learning との組み合わせで使用された Azure サービスがある場合は、別途料金が発生します。 こうした料金には次のものが含まれますが、これらに限定されるものではありません。 
- コンピューティング料金
- HDInsight
- Azure Container Service
- Azure Container Registry 
- Azure Blob Storage
- アプリケーション インサイト
- Azure Key Vault
- Visual Studio Team Services
- Azure Event Hub
- Azure Stream Analytics の詳細、または請求書サンプルについては、[Azure の価格](https://azure.microsoft.com/pricing/details/machine-learning-services/)に関するページをご覧ください。 
