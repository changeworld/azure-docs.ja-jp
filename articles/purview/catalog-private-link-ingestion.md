---
title: プライベートかつ安全なデータ ソースのスキャン
description: この記事では、制限付きネットワークからデータ ソースをスキャンするプライベート エンドポイントを設定する方法について説明します
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: cbdf2220d1b4087376bc40db5b7da167144e5d9b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257198"
---
# <a name="scan-your-data-sources-privately-and-securely"></a>プライベートかつ安全なデータ ソースのスキャン

プライベート ネットワーク内、仮想ネットワーク内、およびプライベート エンドポイントの背後にあるデータ ソースをスキャンする場合は、Azure Purview インジェスト プライベート エンドポイントをデプロイして、スキャンされているデータ ソースから Azure Purview Data Map へ流れるメタデータについてネットワークの分離が確実に行われるようにする必要があります。

Azure Purview では、_インジェスト_ プライベート エンドポイントを使用して、Azure またはオンプレミス環境のデータ ソースをスキャンできます。 インジェスト プライベート エンドポイントがデプロイされると、3 つのプライベート エンドポイント リソースをデプロイして、Azure Purview 管理対象リソースにリンクする必要があります。

- BLOB プライベート エンドポイントは、Azure Purview マネージド ストレージ アカウントにリンクされます。
- キュー プライベート エンドポイントは、Azure Purview マネージド ストレージ アカウントにリンクされます。
- 名前空間プライベート エンドポイントは、Azure Purview マネージド イベント ハブ名前空間にリンクされます。

:::image type="content" source="media/catalog-private-link/purview-private-link-architecture-ingestion.png" alt-text="Azure Purview と Private Link アーキテクチャを示す図。":::

## <a name="deployment-checklist"></a>展開のチェックリスト
このガイドのいずれかのデプロイ オプションを使用して、Azure Purview アカウントのインジェスト プライベート エンドポイントを有効にします。

1. Azure Purview インジェスト プライベート エンドポイントをデプロイするのに適した Azure 仮想ネットワークとサブネットを選択します。 以下のオプションの 1 つを選択します。
   - お使いの Azure サブスクリプションに[新しい仮想ネットワーク](../virtual-network/quick-create-portal.md)をデプロイします。
   - Azure サブスクリプションで既存の Azure 仮想ネットワークとサブネットを検索します。
  
2. Azure Purview がプライベート ネットワークを使用してデータ ソースをスキャンできるよう、インジェスト用に適切な [DNS 名前解決方法](./catalog-private-link-name-resolution.md#deployment-options)を定義します。 次のオプションをどれでも使用できます。
   - このガイドで詳細に説明する手順を使用して、新しい Azure DNS ゾーンをデプロイします。
   - このガイドで詳細に説明する手順を使用して、必要な DNS レコードを既存の Azure DNS ゾーンに追加します。
   - このガイドの手順を完了したら、必要な DNS A レコードを既存の DNS サーバーに手動で追加します。
3. インジェスト プライベート エンドポイントを使用して[新しい Purview アカウント](#option-1---deploy-a-new-azure-purview-account-with-ingestion-private-endpoint)をデプロイするか、[既存の Purview アカウント](#option-2---enable-ingestion-private-endpoint-on-existing-azure-purview-accounts)のインジェスト プライベート エンドポイントをデプロイします。
4. Azure Purview インジェスト プライベート エンドポイントがデプロイされているのと同じ VNet 内に[セルフホステッド統合ランタイム](#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources)をデプロイして登録します。
5. このガイドを完了したら、必要に応じて、DNS 構成を調整します。
6. 管理マシン、セルフホステッド IR VM、Azure Purview へのデータソース間のネットワークと名前解決を検証します。 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_ingestion_-private-endpoint"></a>オプション 1 - _インジェスト_ プライベート エンドポイントを使用して新しい Azure Purview アカウントをデプロイする

1. [Azure portal](https://portal.azure.com) に移動し、 **[Purview アカウント]** ページに移動します。 **[+ 作成]** を選択して、新しい Azure Purview アカウントを作成します。

2. 基本情報を入力し、 **[ネットワーク]** タブで、接続方法を **[プライベート エンドポイント]** に設定します。 [プライベート エンドポイントを有効にする] を **[インジェストのみ]** に設定します。

      :::image type="content" source="media/catalog-private-link/purview-pe-scenario-2-1.png" alt-text="[プライベート エンドポイントの作成] ページの選択を示すスクリーンショット。":::

3. プライベート エンドポイントとペアリングする **サブスクリプション**、**仮想ネットワーク**、および **サブネット** の詳細を入力して、インジェスト プライベート エンドポイントを設定します。

4. 必要に応じて、 **[プライベート DNS インジェスト]** をオンにして、Azure プライベート DNS ゾーンを使用します。
   
   > [!IMPORTANT]
   > Azure Purview とデータ ソース間で正しい名前解決を可能にするために、正しい Azure プライベート DNS ゾーンを選択することが重要です。 また、既存の Azure プライベート DNS ゾーンを使用することも、後で DNS サーバーに DNS レコードを手動で作成することもできます。 詳細については、[プライベート エンドポイントの DNS 名前解決の構成](./catalog-private-link-name-resolution.md)に関するページを参照してください

5.  **[確認および作成]** を選択します。 **[確認と作成]** ページで、Azure によって構成が検証されます。

6.  "検証に成功しました" というメッセージが表示されたら、 **[作成]** を選択します。

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="アカウント作成の検証に成功したことを示すスクリーンショット。":::

## <a name="option-2---enable-_ingestion_-private-endpoint-on-existing-azure-purview-accounts"></a>オプション 2 - 既存の Azure Purview アカウントで _インジェスト_ プライベート エンドポイントを有効にする

1.  [Azure portal](https://portal.azure.com) に移動し、お使いの Azure Purview アカウントをクリックして、 **[設定]** で **[ネットワーク]** を選択し、 **[インジェスト プライベート エンドポイント接続]** を選択します。

2. [インジェスト プライベート エンドポイント接続] で、 **[+ 新規]** を選択して新しいインジェスト プライベート エンドポイントを作成します。

3. 基本情報を入力し、既存の仮想ネットワークとサブネットの詳細を選択します。 必要に応じて、 **[プライベート DNS インジェスト]** をオンにして、Azure プライベート DNS ゾーンを使用します。 
   
   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="プライベート エンドポイントの詳細の入力方法を示すスクリーンショット。":::
   
   > [!IMPORTANT]
   > Azure Purview とデータ ソース間で正しい名前解決を可能にするために、正しい Azure プライベート DNS ゾーンを選択することが重要です。 また、既存の Azure プライベート DNS ゾーンを使用することも、後で DNS サーバーに DNS レコードを手動で作成することもできます。 
   > 
   >詳細については、[プライベート エンドポイントの DNS 名前解決の構成](./catalog-private-link-name-resolution.md)に関するページを参照してください。


4. **[作成]** を選択して設定を終了します。

> [!NOTE]
> インジェスト プライベート エンドポイントは、前の手順で説明した Azure Purview ポータルのエクスペリエンスを介してのみ作成できます。 Private Link センターから作成することはできません。

## <a name="deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources"></a>セルフホステッド統合ランタイム (IR) をデプロイし、データ ソースをスキャンします。
Azure Purview のインジェスト プライベート エンドポイントをデプロイした後、1 つ以上のセルフホステッド統合ランタイム (IR) を設定して登録する必要があります。

- 現在、Microsoft SQL Server、Oracle、SAP など、オンプレミスのすべての種類のソースは、セルフホステッド IR ベースのスキャンでのみサポートされています。 セルフホステッド IR は、プライベート ネットワーク内で実行し、Azure 内の自分の仮想ネットワークとピアリングする必要があります。 
   
- Azure Blob Storage や Azure SQL Database などのすべての種類の Azure ソースについては、Azure Purview インジェスト プライベート エンドポイントと同じ VNet にデプロイされているセルフホステッド統合ランタイムを使用して、スキャンの実行を明示的に選択する必要があります。 

「[セルフホステッド統合ランタイムの作成および管理](manage-integration-runtimes.md)」の手順に従って、セルフホステッド IR を設定します。 その後、 **[統合ランタイム経由で接続]** ドロップダウンでそのセルフホステッド IR を選択して Azure ソースでのスキャンを設定し、ネットワークの分離を確保します。
    
   :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="セルフホステッド IR を使用して Azure スキャンを実行する方法を示すスクリーンショット。":::

> [!IMPORTANT]
> 2021 年 8 月 18 日以降に Azure Purview アカウントを作成した場合は必ず、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から最新バージョンのセルフホステッド統合ランタイムをダウンロードしてインストールしてください。
> 
## <a name="next-steps"></a>次のステップ

-  [プライベート エンドポイントの解決を検証する](./catalog-private-link-name-resolution.md)
-  [Azure Purview でデータ ソースを管理する](./manage-data-sources.md)
-  [Azure Purview アカウントのプライベート エンドポイント構成のトラブルシューティング](./catalog-private-link-troubleshoot.md)
