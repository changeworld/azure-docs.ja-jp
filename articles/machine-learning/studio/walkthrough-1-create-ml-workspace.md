---
title: 手順 1:ワークスペースの作成 - Azure Machine Learning Studio | Microsoft Docs
description: 予測ソリューションの開発チュートリアルの手順 1:新しい Azure Machine Learning Studio ワークスペースを設定する方法について説明します。
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
editor: cgronlun
ms.assetid: b3c97e3d-16ba-4e42-9657-2562854a1e04
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: bc83fa6e3fa7d5ef31515309f5c1cd0b025c8906
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256368"
---
# <a name="walkthrough-step-1-create-an-azure-machine-learning-studio-workspace"></a>チュートリアル手順 1:Azure Machine Learning Studio ワークスペースの作成
これは、「[チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](walkthrough-develop-predictive-solution.md)」の最初の手順です。

1. **Machine Learning ワークスペースの作成**
2. [既存のデータをアップロードする](walkthrough-2-upload-data.md)
3. [新しい実験を作成する](walkthrough-3-create-new-experiment.md)
4. [モデルをトレーニングして評価する](walkthrough-4-train-and-evaluate-models.md)
5. [Web サービスをデプロイする](walkthrough-5-publish-web-service.md)
6. [Web サービスにアクセスする](walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Machine Learning Studio を使用するには、Microsoft Azure Machine Learning ワークスペースが必要です。 このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。  

ワークスペースの作成と、所有者または共同作成者としてのユーザーの追加は、Azure サブスクリプションの管理者が実行する必要があります。 詳細については、「[Azure Machine Learning ワークスペースの作成と共有](create-workspace.md)」を参照してください。

ワークスペースが作成された後、Machine Learning Studio ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)) を開きます。 ワークスペースが複数ある場合は、ウィンドウの右上隅のツールバーでワークスペースを選択できます。

![Studio でのワークスペースの選択][2]

> [!TIP]
> 自分がワークスペースの所有者になっている場合は、自分以外のユーザーを後からワークスペースに招待することで、作業中の実験を共有することができます。 これは、Machine Learning Studio の **[設定]** ページから実行でき、 必要な情報は各ユーザーの Microsoft アカウントまたは組織アカウントだけです。
> 
> **[設定]** ページで **[ユーザー]** をクリックします。次に、ウィンドウの下部にある **[INVITE MORE USERS]** をクリックします。
> 
> 

- - -
**次:[既存のデータをアップロードする](walkthrough-2-upload-data.md)**

[1]: ./media/walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/walkthrough-1-create-ml-workspace/open-workspace.png
