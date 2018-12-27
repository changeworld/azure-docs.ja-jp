---
title: Azure Blob Storage との間のデータの移動 | Microsoft Docs
description: Azure Blob Storage との間でデータを移動します
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 7d0111b22df45577fccc3f4491f375ddd2e8b40f
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344469"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Azure Blob Storage との間のデータの移動

Team Data Science Process では、さまざまなストレージ環境に取り込まれるデータまたは読み込まれるデータを、プロセスの各段階において最も適切な方法で処理または分析する必要があります。
以下の記事では、さまざまなテクノロジを使用して Azure Blob Storage との間で双方向にデータを移動する方法について説明されています。

* [Azure Storage Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](move-data-to-azure-blob-using-azcopy.md)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

最適な方法はシナリオによって異なります。 高度な分析プロセスで使用されるさまざまなデータ サイエンス ワークフローで必要なリソースを確認するには、記事「 [Azure Machine Learning での高度な分析のシナリオ](plan-sample-scenarios.md) 」が役立ちます。

> [!NOTE]
> Azure BLOB ストレージの概要については、[Azure BLOB の基礎](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)に関する記事および [Azure BLOB サービス](https://msdn.microsoft.com/library/azure/dd179376.aspx)に関するページをご覧ください。
> 
> 

別の方法として、 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用して以下を行うこともできます。 

* Azure BLOB ストレージからデータをダウンロードするパイプラインを作成し、スケジュールを設定する 
* パイプラインを発行済みの Azure Machine Learning Web サービスに渡す 
* 予測分析の結果を受け取る 
* 結果をストレージにアップロードする 

詳細については、「 [Azure Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する](../../data-factory/transform-data-using-machine-learning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。 データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

* Azure サブスクリプションを設定するには、 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* ストレージ アカウントの作成と、アカウントとキー情報の取得の手順については、「 [Azure ストレージ アカウントについて](../../storage/common/storage-create-storage-account.md)」を参照してください。

