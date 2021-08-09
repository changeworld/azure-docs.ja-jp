---
title: Azure Blob Storage との間でのデータの移動 - Team Data Science Process
description: Azure Storage Explorer、AzCopy、Python、および SSIS を使用して、Azure Blob Storage 間でデータを移動します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 151375a37b80567aecf100ec3cd576882d06e3f3
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111902251"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Azure Blob Storage との間のデータの移動

Team Data Science Process では、さまざまなストレージ環境に取り込まれるデータまたは読み込まれるデータを、プロセスの各段階において最も適切な方法で処理または分析する必要があります。

## <a name="different-technologies-for-moving-data"></a>データを移動するためのさまざまなテクノロジ

以下の記事では、さまざまなテクノロジを使用して Azure Blob Storage との間で双方向にデータを移動する方法について説明されています。

* [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
  * Storage Explorer は、Windows、macOS、Linux で Azure Storage のデータを操作できる Microsoft の無料ツールです。
  * Azure のデータ サイエンス用仮想マシンによって提供されるスクリプトを使用してセットアップされた VM を使用している場合、Azure Storage Explorer は既に VM にインストールされています。
* [AzCopy](../../storage/common/storage-use-azcopy-v10.md)
  * AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 
* [Python SDK](../../storage/blobs/storage-quickstart-blobs-python.md)
  * Python 用 Azure Blob Storage クライアント ライブラリを使用して、BLOB を移動します。
* [SQL Server Integration Services (SSIS) Feature Pack for Azure](/sql/integration-services/azure-feature-pack-for-integration-services-ssis)
  * SSIS には、Azure への接続、Azure とオンプレミスのデータ ソースとの間でのデータ転送、Azure に格納したデータの処理に必要なコンポーネントが用意されています。 ハイブリッド データ統合のシナリオに共通するビジネス ニーズを満たすために SSIS を使用する標準的なシナリオの詳細については、[SQL Server Integration Services Feature Pack for Azure のさらなる活用](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238)に関するブログを参照してください。
  * SSIS のトレーニング資料については、[SSIS の実践的トレーニング](https://www.microsoft.com/sql-server/training-certification)に関するページをご覧ください。
  * SISS を使用して稼働状態にし、簡単な ETL (抽出、変換、読み込み) パッケージを作成する方法については、「 [SSIS チュートリアル: 簡単な ETL パッケージの作成](/sql/integration-services/ssis-how-to-create-an-etl-package)」を参照してください。

最適な方法はシナリオによって異なります。 高度な分析プロセスで使用されるさまざまなデータ サイエンス ワークフローで必要なリソースを確認するには、記事「[Azure Machine Learning での高度な分析のシナリオ](plan-sample-scenarios.md)」が役立ちます。

> [!NOTE]
> Azure Blob Storage の概要については、[Azure BLOB の基礎](../../storage/blobs/storage-quickstart-blobs-dotnet.md)に関する記事および [Azure BLOB サービス](/rest/api/storageservices/Blob-Service-Concepts)に関するページをご覧ください。
> 
> 

## <a name="using-azure-data-factory"></a>Azure Data Factory の使用

別の方法として、 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用して以下を行うこともできます。 

* Azure Blob Storage からデータをダウンロードするパイプラインを作成し、スケジュールを設定する
* パイプラインを発行済みの Azure Machine Learning Web サービスに渡す 
* 予測分析の結果を受け取る 
* 結果をストレージにアップロードする 

詳細については、「[Azure Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する](../../data-factory/transform-data-using-machine-learning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
この記事は、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。 データのアップロードまたはダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

* Azure サブスクリプションを設定するには、 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* ストレージ アカウントの作成と、アカウントとキー情報の取得の手順については、[Azure ストレージ アカウントについて](../../storage/common/storage-account-create.md)のページを参照してください。