---
title: "信用リスク評価のための予測分析ソリューションを Machine Learning で開発する | Microsoft Docs"
description: "信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する方法を詳しく紹介したチュートリアルです。"
keywords: "信用リスク, 予測分析ソリューション,リスク評価"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 07cb3fe0d5688d5b63fe3312cad14c2274a58a09
ms.openlocfilehash: e98a64910f28da0a8a9b4a58c717c40d791ccf00


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する

このチュートリアルでは、Machine Learning Studio でソリューションを開発するプロセスについて詳しく説明します。 Machine Learning Studio で予測分析モデルを開発し、Machine Learning Web サービスとしてデプロイします。このサービスでは、モデルが新しいデータを使用して予測を行うことができます。 

> [!TIP]
> このチュートリアルでは、ユーザーがこれまでに少なくとも 1 回は Machine Learning Studio を使用したことがあり、機械学習の概念をある程度理解していることを前提としていますが、いずれにしても専門家ではないことを想定しています。
> 
>**Azure Machine Learning Studio** をまだ使用したことがない場合は、チュートリアル「[Azure Machine Learning Studio で初めてのデータ サイエンス実験を作成する](machine-learning-create-experiment.md)」から始めることをお勧めします。 そのチュートリアルでは、Machine Learning Studio を初めて使用する場合の手順を示しつつ、実験にモジュールをドラッグ アンド ドロップして互いに結び付け、実験を実行して結果を確認する方法の基本について説明します。
>
>機械学習についてよく知らない場合は、まずビデオ シリーズ「[初心者向けデータ サイエンス](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md)」をご覧になることをお勧めします。 日常的な言葉と概念が使用されているこのビデオ シリーズは、機械学習の導入として優れています。
> 

## <a name="the-problem"></a>問題

信用貸付の申請書に記入する情報に基づいて個人の信用リスクを予測する必要があるとします。  

もちろん信用リスクの評価は複雑な問題ですが、ここでは質問に含まれるパラメーターを少し簡略化し、 そのような予測分析ソリューションを作成するために Microsoft Azure Machine Learning を Machine Learning Studio および Machine Learning Web サービスと共に使用する方法の例として取り上げます。  

## <a name="the-solution"></a>解決策

この詳細なチュートリアルでは、公開されている信用リスク データを使用して、データに基づいた予測モデルを開発してトレーニングしてから、他のユーザーが信用リスクの評価目的に使用できる Web サービスとしてモデルをデプロイします。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

信用リスク評価ソリューションを作成するのには、次の手順に従います。  

1. [Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)
3. [新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)
4. [モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)
6. [Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)

このチュートリアルは、[Cortana Intelligence ギャラリー](http://gallery.cortanaintelligence.com/)にある「[Binary Classfication: Credit risk prediction (二項分類: 信用リスク予測)](http://go.microsoft.com/fwlink/?LinkID=525270)」のサンプル実験の簡略版に基づいています。


> [!TIP]
> Machine Learning Studio の機能の概要を示す図をダウンロードして印刷する場合は、「 [Azure Machine Learning Studio 機能の概要図](machine-learning-studio-overview-diagram.md)」ご覧ください。
> 
> 



<!--HONumber=Dec16_HO3-->


