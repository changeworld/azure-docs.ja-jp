---
title: 音響のための Azure アカウントを設定する - Cognitive Services
description: 音響の操作に必要な Azure Batch および Storage アカウントを設定するには、このガイドに従ってください。
services: cognitive-services
author: ashtat
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: d5e78df2cb17e8275aef3694dda90a705ef4bdaa
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181377"
---
# <a name="create-an-azure-batch-account"></a>Azure Batch アカウントを作成する
音響の操作に必要な Azure Batch および Storage アカウントを設定するには、このガイドに従ってください。 Project Acoustics の一部として開発された Unity プラグインについては、[音響の概要](what-is-acoustics.md)に関するページを参照してください。 Unity プロジェクトに音響を組み込む方法については、[使用の開始](getting-started.md)に関するページを参照してください。  

## <a name="get-an-azure-subscription"></a>Azure サブスクリプションを取得する
Batch および Storage アカウントを設定する前に、[Azure サブスクリプション](https://azure.microsoft.com/free/)が必要です。 初めてサインアップする場合、Azure には、期間限定のいくつかの無料のリソースと 200 ドルのクレジットが用意されています。

## <a name="create-azure-batch-and-storage-accounts"></a>Azure Batch および Storage アカウントを作成する
次に、[次の手順](https://docs.microsoft.com/azure/batch/batch-account-create-portal)に従って、Azure Batch アカウントとそれに関連付けられた Azure Storage アカウントを設定します。

Batch アカウントと Storage アカウントの両方について、既定のオプションを選択します。
  
  ![新しい Batch アカウント](media/NewBatchAccountCreate.png)

  ![新しい Storage アカウント](media/BatchStorageAccountCreate.png)

Azure がこれらのアカウントをデプロイするには数分かかります。 ポータルの右上隅にある完了通知を探してください。
  
  ![デプロイされたアカウント](media/BatchAccountsDeployNotification.png)

アカウントがダッシュボードに表示されるようになります。
  
  ![ポータル ダッシュボード](media/AzurePortalDashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Azure 資格情報を使用して音響ベイク UI を設定する
ダッシュボードの [Batch アカウント] リンクをクリックしてから、[Batch アカウント] ページの **[キー]** リンクをクリックして資格情報にアクセスします。
  
  ![Batch の [キー] リンク](media/BatchAccessKeys.png)

  ![Batch アカウントの資格情報](media/BatchKeysInfo.png)

ページの **[ストレージ アカウント]** リンクをクリックして Azure Storage アカウントの資格情報にアクセスします。
  
  ![ストレージ アカウントの資格情報](media/StorageKeysInfo.png)

[ベイク UI のチュートリアル](bake-ui-walkthrough.md)の説明に従って、[Bake] (ベイク) タブでこれらの資格情報を入力します。

## <a name="node-types-and-region-support"></a>ノードの種類とリージョンのサポート
Project Acoustics には、すべての Azure リージョンではサポートされない可能性のある、F および H シリーズ コンピューティング最適化 Azure VM ノードが必要です。 [この表](https://azure.microsoft.com/global-infrastructure/services)をチェックして、Batch アカウントの正しい場所を選択していることを確認してください。 現時点では、H シリーズ仮想マシンは米国東部、米国中北部、米国中南部、米国西部、米国西部 2、北ヨーロッパ、西ヨーロッパ、および西日本でサポートされています。

## <a name="upgrading-your-quota"></a>クォータのアップグレード
Azure Batch アカウントは、アカウント作成時に、20 コンピューティング コアの制限でプロビジョニングされます。 音響ワークロードは、シーン内のプローブ ポイントの数まで多数のノードにまたがって並列化できるため、この制限を増やしてベイク時間を速くすることもできます。 Azure Batch ポータル ページの **[クォータ]** リンクをクリックしてから、**[Request Quota Increase] (クォータの増加を要求する)** をクリックすることによって、クォータの増加を要求できます。

![Azure クォータの増加](media/azurequotas.png)

## <a name="next-steps"></a>次の手順
* [Unity プロジェクトへの音響の統合](getting-started.md)を開始する
* [サンプル シーン](sample-walkthrough.md)を調査する

