---
title: 統合ランタイムの作成および管理
description: この記事では、Azure Purview で統合ランタイムを作成して管理する手順について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 1a51af8fd34516ca87d7ab98332221a308480193
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217149"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムの作成および管理

この記事では、Azure Purview でデータ ソースのスキャンを行うことができるセルフホステッド統合ランタイム (SHIR) を作成および管理する方法について説明します。

> [!NOTE]
> Purview Integration Runtime は、同じマシン上の Azure Synapse Analytics または Azure Data Factory Integration Runtime と共有することはできません。 別のマシンにインストールする必要があります。

> [!IMPORTANT]
> 2021 年 8 月 18 日以降に Azure Purview アカウントを作成した場合は必ず、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から最新バージョンのセルフホステッド統合ランタイムをダウンロードしてインストールしてください。

## <a name="prerequisites"></a>前提条件

- サポートされている Windows のバージョンは次のとおりです。
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

セルフホステッド統合ランタイムのドメイン コントローラーへのインストールはサポートされていません。

- セルフホステッド統合ランタイムには、.NET Framework 4.7.2 以降を含む 64 ビット オペレーティング システムが必要です。 詳細については、「 [.NET Framework システム要件](/dotnet/framework/get-started/system-requirements) 」をご覧ください。
- セルフホステッド統合ランタイム コンピューターに推奨される最小構成は、4 コアの 2 GHz プロセッサ、8 GB の RAM、および 80 GB の使用可能なハード ドライブ領域です。 システム要件の詳細については、[ダウンロード](https://www.microsoft.com/download/details.aspx?id=39717)のページを参照してください。
- ホスト コンピューターが休止状態の場合、セルフホステッド統合ランタイムはデータ要求に応答しません。 セルフホステッド統合ランタイムをインストールする前に、コンピューターに適切な電源プランを構成します。 コンピューターが休止状態に入るよう構成されている場合、セルフホステッド統合ランタイムのインストーラーによりメッセージが出力されます。
- セルフホステッド統合ランタイムを正常にインストールして構成するには、コンピューターの管理者である必要があります。
- スキャンは、設定したスケジュールに従って特定の頻度で実行されます。 コンピューター上のプロセッサおよび RAM の使用量は、ピーク時とアイドル時のある同じパターンに従います。 また、リソース使用量は、スキャンされるデータの量に大きく依存します。 複数のスキャン ジョブが進行中のときには、ピーク時にリソース使用率が上昇します。
- Parquet、ORC、または Avro 形式のデータの抽出中にタスクが失敗することがあります。

## <a name="setting-up-a-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムをセットアップする

セルフホステッド統合ランタイムを作成およびセットアップするには、次の手順に従います。

## <a name="create-a-self-hosted-integration-runtime"></a>自己ホスト型統合ランタイムを作成する

1. [Purview Studio](https://web.purview.azure.com/resource/) のホーム ページの左側のナビゲーション ウィンドウで、 **[Data Map]** を選択します。

2. 左側のウィンドウの **[Sources and scanning]\(ソースとスキャン\)** で **[統合ランタイム]** を選択し、 **[+ 新規]** を選択します。

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="IR の選択":::

3. **[統合ランタイムのセットアップ]** ページで **[セルフホステッド]** を選択してセルフホステッド IR を作成し、 **[続行]** を選択します。

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="新しい SHIR を作成します。":::

4. IR の名前を入力し、 [作成] を選択します。

5. **[統合ランタイムのセットアップ]** ページで、 **[手動セットアップ]** セクションに記載されている手順に従います。 統合ランタイムは、ダウンロード サイトから、それを実行する VM またはマシンにダウンロードする必要があります。

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="キーの取得":::

   - 認証キーをコピーして貼り付けます。

   - セルフホステッド統合ランタイムを、「[Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717)」からローカルの Windows マシンにダウンロードします。 インストーラーを実行します。 5\.4.7803.1 や 5.6.7795.1 などのセルフホステッド統合ランタイム バージョンがサポートされています。 

   - **[Integration Runtime (セルフホステッド) の登録]** ページで、前に保存した 2 つのキーのいずれかを貼り付け、 **[登録]** を選択します。

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="キーの入力":::

   - **[新しい統合ランタイム (セルフホステッド) ノード]** ページで **[完了]** を選択します。

6. セルフホステッド統合ランタイムが正常に登録されると、次のウィンドウが表示されます。

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="正常に登録":::

## <a name="networking-requirements"></a>ネットワーク要件

セルフホステッド統合ランタイム マシンは、正常に動作するために複数のリソースに接続する必要があります。

* セルフホステッド統合ランタイムを使用してスキャンするソース。
* Purview リソースのための資格情報を格納するために使用されている任意の Azure Key Vault。
* Purview によって作成されたマネージド ストレージ アカウント リソースとイベント ハブ リソース。

マネージ ストレージ リソースとイベント ハブ リソースは、サブスクリプション内の Purview リソースの名前が含まれるリソース グループで見つかります。 Azure Purview により、これらのリソースを使用して、他の多くのものと共にスキャンの結果が取り込まれるので、セルフホステッド統合ランタイムはこれらのリソースに直接接続できる必要があります。

企業およびマシンのファイアウォールの通過を許可する必要があるドメインとポートを以下に示します。

> [!NOTE]
> "\<managed Purview storage account>" と示されているドメインについては、Purview リソースに関連付けられているマネージド ストレージ アカウントの名前を追加します。 このリソースはポータルで見つけることができます。 リソース グループで managed-rg-\<your Purview Resource name> という名前のグループを検索します。 たとえば、managed-rg-contosoPurview のようなものです。 このリソース グループのストレージ アカウントの名前を使用します。
> 
> "\<managed Event Hub resource>" と示されているドメインについては、Purview リソースに関連付けられているマネージド イベント ハブの名前を追加します。 これは、マネージド ストレージ アカウントと同じリソース グループで見つかります。

| ドメイン名                  | 送信ポート | 説明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net` | 443            | グローバル インフラストラクチャ Purview により、スキャンを実行するために使用されます。 専用のリソースがないため、ワイルドカードが必要です。 |
| `<managed Event Hub resource>.servicebus.windows.net` | 443            | これは、Purview により、関連付けられている Service Bus と接続するために使用されます。 これは、上記のドメインを許可することによってカバーされますが、プライベート エンドポイントを使用している場合は、この単一のドメインへのアクセスをテストする必要があります。|
| `*.frontend.clouddatahub.net` | 443            | グローバル インフラストラクチャ Purview により、スキャンを実行するために使用されます。 専用のリソースがないため、ワイルドカードが必要です。 |
| `<managed Purview storage account>.core.windows.net`          | 443            | セルフホステッド統合ランタイムにより、マネージド Azure ストレージ アカウントに接続するために使用されます。|
| `<managed Purview storage account>.queue.core.windows.net` | 443            | スキャン プロセスを実行するために Purview によって使用されるキュー。 |
| `download.microsoft.com` | 443           | SHIR の更新の場合は省略可能。 |

ソースに基づいて、他の Azure または外部ソースのドメインを許可する必要がある場合があります。 Key Vault に保存されている資格情報に接続する場合の Azure Key Vault ドメインとともに、いくつかの例を以下に示します。

| ドメイン名                  | 送信ポート | 説明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `<storage account>.core.windows.net`          | 443            | オプション。Azure Storage アカウントに接続する場合。 |
| `*.database.windows.net`      | 1433           | オプション。Azure SQL Database または Azure Synapse Analytics に接続する場合。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | オプション。Azure Data Lake Store Gen 1 に接続する場合。 |
| `<datastoragename>.dfs.core.windows.net`    | 443            | オプション。Azure Data Lake Store Gen 2 に接続する場合。 |
| `<your Key Vault Name>.vault.azure.net` | 443           | 資格情報が Azure Key Vault に格納されている場合に必要です。 |
| さまざまなドメイン | ドメインに依存          | SHIR の接続先である他のソースのドメイン。 |
  
> [!IMPORTANT]
> ほとんどの環境では、DNS が正しく構成されていることを確認する必要もあります。 確認するには、お使いの SHIR マシンから **nslookup** を使用して、上記の各ドメインへの接続を調べます。 各 nslookup から、リソースの IP が返される必要があります。 [プライベート エンドポイント](catalog-private-link.md)を使用している場合は、パブリック IP ではなく、プライベート IP が返される必要があります。 IP が返されない場合、またはプライベート エンドポイントを使用しているときにパブリック IP が返される場合は、DNS と VNET の関連付け、またはプライベート エンドポイントと VNET のピアリングに、対処する必要があります。

## <a name="manage-a-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムの管理

セルフホステッド統合ランタイムを編集するには、**管理センター** の **[統合ランタイム]** に移動し、IR を選択して [編集] を選択します。 これで、説明の更新、キーのコピー、新しいキーの再生成ができるようになります。

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="IR の編集":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="IR の詳細の編集":::

セルフホステッド統合ランタイムを削除するには、管理センターの **[統合ランタイム]** に移動し、IR を選択して **[削除]** を選択します。 IR が削除されると、それに依存している実行中のスキャンは失敗します。

## <a name="next-steps"></a>次のステップ

- [削除されたアセットをスキャンが検出する方法](concept-scans-and-ingestion.md#how-scans-detect-deleted-assets)

- [Purview でプライベート エンドポイントを使用する](catalog-private-link.md)
