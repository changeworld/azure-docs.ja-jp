---
title: プライベート エンドポイントを使用して Purview へのアクセスをセキュリティで保護する
description: この記事では、Purview アカウントにプライベート エンド ポイントを使用する方法についての大まかな概要を説明します
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: dba76c0b2d25c8bc962dd847e0d2ffdaa5bdd3eb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234229"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>Azure Purview アカウントにプライベート エンドポイントを使用する

> [!IMPORTANT]
> **2021 年 9 月 27 日 15:30 (UTC) より前に、** Purview アカウントの "_ポータル_" プライベート エンドポイントを作成した場合は、「[ポータルのプライベート エンドポイント用に DNS を再構成する](#reconfigure-dns-for-portal-private-endpoints)」のセクションで詳しく説明されているように、必要な操作を実行する必要があります。 **これらの操作は、2021 年 11 月 12 日より前に完了する必要があります。そうしない場合、既存のポータルのプライベート エンドポイントの機能が停止します**。


この記事では、Azure Purview に対してプライベート エンドポイントを構成する方法について説明します。

## <a name="conceptual-overview"></a>概念
ご利用の Azure Purview アカウントに [Azure プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用すると、仮想ネットワーク (VNet) 上のユーザーが Private Link を介してカタログに安全にアクセスできるようになります。 プライベート エンドポイントでは、ご利用の Purview アカウント用の VNet アドレス空間からの IP アドレスが使用されます。 VNet 上のクライアントと Purview アカウントの間のネットワーク トラフィックは、VNet と Microsoft バックボーン ネットワーク上のプライベート リンクを経由して転送されます。 

Azure Purview "_アカウント_" プライベート エンドポイントをデプロイすると、プライベート ネットワーク内から発信された、Azure Purview へのクライアント呼び出しのみを許可することができます。

プライベート ネットワーク接続を使用して Azure Purview Studio に接続するには、"_ポータル_" プライベート エンドポイントをデプロイしてください。

プライベート接続を介して Azure 仮想ネットワーク内の Azure IaaS および PaaS データ ソースとオンプレミスのデータ ソースをスキャンする必要がある場合は、"_インジェスト_" プライベート エンドポイントをデプロイしてください。 この方法を使用すれば、データ ソースから Azure Purview Data Map に流れる自分のメタデータについてネットワーク分離を確実に行うことができます。

:::image type="content" source="media/catalog-private-link/purview-private-link-overview.png" alt-text="プライベート エンドポイントを使用した Azure Purview を示すスクリーンショット。"::: 

## <a name="prerequisites"></a>前提条件

Azure Purview アカウント用のプライベート エンドポイントをデプロイする前に、次の前提条件を確実に満たしてください。

1. アクティブなサブスクリプションが含まれる Azure アカウント。 [アカウントは無料で作成できます。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
<br>
2. 既存の Azure 仮想ネットワーク。 それがない場合は、新しい [Azure 仮想ネットワーク](../virtual-network/quick-create-portal.md)をデプロイします。
<br>

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Azure Purview プライベート エンドポイントのデプロイ シナリオ

プライベート エンドポイントを使用して Azure Purview アカウントのデプロイを実行するには、次の推奨チェックリストを使用します。

|シナリオ  |目標  |
|---------|---------|
|**シナリオ 1** - [ご利用の Azure Purview に接続して、プライベートかつ安全にデータ ソースをスキャンする](./catalog-private-link-end-to-end.md) |Azure Purview アカウントへのアクセスをプライベート エンドポイント経由のみに制限する必要があります。たとえば、エンドツーエンドのネットワーク分離を保証するセルフホステッド統合ランタイムを使用して、仮想ネットワークの背後にあるオンプレミスおよび Azure 内の Azure Purview Studio、Atlas API、スキャン データ ソースにアクセスします ("_アカウント_"、"_ポータル_"、"_インジェスト_" の各プライベート エンドポイントをデプロイします)。   |
|**シナリオ 2** - [ご利用の Purview アカウントにプライベートかつ安全に接続する](./catalog-private-link-account-portal.md)   | プライベート エンドポイントを経由した _Azure Purview Studio_ および Atlas API へのアクセスなど、Azure Purview アカウントへのアクセスを有効にする必要があります ("_アカウント_" および "_ポータル_" のプライベート エンドポイントをデプロイします)。   |

## <a name="support-matrix-for-scanning-data-sources-through-_ingestion_-private-endpoint"></a>"_インジェスト_" プライベート エンドポイントを介してデータ ソースをスキャンするためのマトリックスをサポートする

ご利用の Azure Purview アカウントで "_インジェスト_" プライベート エンドポイントが使用されていて、ご利用のデータ ソースに対するパブリック アクセスが無効になっているシナリオでは、Azure Purview を使用することで、プライベート エンドポイントの背後にある次のデータ ソースをスキャンすることができます。

|プライベート エンドポイントの背後にあるデータ ソース  |統合ランタイムの種類  |資格情報の種類  |
|---------|---------|---------|
|Azure Blob Storage | セルフホステッド IR | サービス プリンシパル|
|Azure Blob Storage | セルフホステッド IR | アカウント キー|
|Azure Data Lake Storage Gen 2 | セルフホステッド IR| サービス プリンシパル|
|Azure Data Lake Storage Gen 2 | セルフホステッド IR| アカウント キー|
|Azure SQL データベース | セルフホステッド IR| SQL 認証|
|Azure SQL データベース | セルフホステッド IR| サービス プリンシパル|
|Azure SQL Managed Instance | セルフホステッド IR| SQL 認証|
|Azure Cosmos DB| セルフホステッド IR| アカウント キー|
|SQL Server | セルフホステッド IR| SQL 認証|
|Azure Synapse Analytics | セルフホステッド IR| サービス プリンシパル|
|Azure Synapse Analytics | セルフホステッド IR| SQL 認証|

## <a name="reconfigure-dns-for-portal-private-endpoints"></a>ポータルのプライベート エンドポイント用に DNS を再構成する

**2021 年 9 月 27 日 15:30 (UTC) より前に** Purview アカウントの "_ポータル_" プライベート エンドポイントを作成した場合は、このセクションで詳しく説明されている操作を実行する必要があります。

### <a name="review-your-current-dns-settings"></a>現在の DNS 設定を確認する

1. Azure portal から、ご利用の Purview アカウントを探します。 左側のメニューで **[ネットワーク]** をクリックし、**[プライベート エンドポイント接続]** を選択します。 リストにある各プライベート エンドポイントをクリックし、以下の手順に従います。

    :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-1.png" alt-text="Purview のプライベート エンドポイントを表示する画面のスクリーンショット。"lightbox="media/catalog-private-link/purview-pe-dns-updates-1.png":::

2. ターゲット サブリソースが "_ポータル_" の場合、**[DNS の構成]** を確認します。それ以外の場合は、前の手順に戻って次のプライベート エンドポイントを選択します。すべてのプライベート エンドポイントを確認し、ポータルに関連付けられているプライベート エンドポイントをすべて検証するまで、これを繰り返します。

    :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-2.png" alt-text="ポータル Purview プライベート エンドポイントを表示する画面のスクリーンショット。"lightbox="media/catalog-private-link/purview-pe-dns-updates-2.png":::

3. **[DNS の構成]** ウィンドウで、現在の設定を確認します。
   
    - **[カスタム DNS レコード]** セクションにレコードが存在する場合は、[修復シナリオ 1](#scenario-1) および [修復シナリオ 2](#scenario-2) の手順に従います。
    
        :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-3.png" alt-text="ポータル Purview プライベート エンドポイントのカスタム DNS 構成を表示する画面のスクリーンショット。"lightbox="media/catalog-private-link/purview-pe-dns-updates-3.png":::

    - **[構成名]** セクションにレコードが存在し、DNS ゾーンが `privatelink.purviewstudio.azure.com` である場合、このプライベート エンドポイントは対処不要です。 **手順 1** に戻り、残っているポータル プライベート エンドポイントを確認してください。
  
        :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-4.png" alt-text="ポータル Purview プライベート エンドポイントと新しい DNS ゾーンを表示する画面のスクリーンショット。"lightbox="media/catalog-private-link/purview-pe-dns-updates-4.png":::
    
    - **[構成名]** セクションにレコードが存在し、DNS ゾーンが `privatelink.purview.azure.com` である場合、[修復シナリオ 3](#scenario-3) の手順に従います。

        :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-5.png" alt-text="ポータル Purview プライベート エンドポイントと古い DNS ゾーンを表示する画面のスクリーンショット。"lightbox="media/catalog-private-link/purview-pe-dns-updates-5.png":::

### <a name="remediation-scenarios"></a>修復シナリオ

#### <a name="scenario-1"></a>シナリオ 1

**必要な DNS A レコードを DNS またはマシンのホスト ファイルに直接追加** した場合、**対処の必要はありません**。
    
:::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-host.png" alt-text="A レコードを含むホスト ファイルを示す画面のスクリーンショット。"lightbox="media/catalog-private-link/purview-pe-dns-updates-host.png":::

#### <a name="scenario-2"></a>シナリオ 2

**オンプレミス DNS サーバー**、**DNS フォワーダー、カスタム DNS 解決** のいずれかの構成が済んでいる場合、DNS 設定を確認して適切な措置を講じます。

1. DNS サーバーを確認します。 DNS レコードが `web.purview.azure.com` である場合や、条件付きフォワーダーが `purview.azure.com` である場合、**対処の必要はありません**。 

2. DNS レコードが `web.privatelink.purview.azure.com` である場合は、レコードを `web.privatelink.purviewstudio.azure.com` に更新します。

3. 条件付きフォワーダーが `privatelink.purview.azure.com` である場合、"決してゾーンを削除しないでください"。 新しい条件付きフォワーダーを `privatelink.purviewstudio.azure.com` に追加する必要があります。

#### <a name="scenario-3"></a>シナリオ 3

Purview アカウントに対して **Azure プライベート DNS ゾーン統合を構成** した場合は、これらの手順に従ってプライベート エンドポイントを再デプロイし、DNS 設定を再構成します。

1. 新しいポータルのプライベート エンドポイントをデプロイします。
       
    1. [Azure portal](https://portal.azure.com) に移動し、お使いの Azure Purview アカウントをクリックして、 **[設定]** で **[ネットワーク]** を選択し、 **[プライベート エンドポイント接続]** を選択します。

        :::image type="content" source="media/catalog-private-link/purview-pe-reconfigure-portal.png" alt-text="ポータルのプライベート エンドポイントの作成を示すスクリーンショット。"lightbox="media/catalog-private-link/purview-pe-reconfigure-portal.png":::

    2. **[+ プライベート エンドポイント]** を選択して、新しいプライベート エンドポイントを作成します。

    3. 基本情報を入力します。

    4. **[リソース]** タブで、 **[リソースの種類]** として **[Microsoft.Purview/account]** を選択します。

    5. **[リソース]** として Azure Purview アカウントを選択し、 **[対象サブリソース]** として **[ポータル]** を選択します。

    6. **[構成]** タブで仮想ネットワークを選択し、Azure プライベート DNS ゾーンを選択して新しい Azure DNS ゾーンを作成します。
            
        :::image type="content" source="media/catalog-private-link/purview-pe-reconfigure-portal-dns.png" alt-text="ポータルのプライベート エンドポイントと DNS 設定の作成を示すスクリーンショット。"lightbox="media/catalog-private-link/purview-pe-reconfigure-portal-dns.png":::

    7. [概要] ページに移動し、 **[作成]** を選択してポータルのプライベート エンドポイントを作成します。

2. Purview アカウントに関連付けられている、以前のポータルのプライベート エンドポイントを削除します。 

3. ポータルのプライベート エンドポイントのデプロイ時に新しい Azure プライベート DNS ゾーン (`privatelink.purviewstudio.azure.com`) が作成され、対応する A レコード (Web) がプライベート DNS ゾーンに存在することを確認します。 
    
4. Azure Purview Studio を正常に読み込むことができることを確認します。 DNS を再構成した後、新しい DNS ルーティングが有効になるまでに数分 (約 10 分) かかる場合があります。 すぐに読み込まれない場合は、数分待ってから、もう一度やり直してください。
    
5. ナビゲーションが失敗した場合は、nslookup web.purview.azure.com を実行します。これは、ポータルのプライベート エンドポイントに関連付けられているプライベート IP アドレスに解決されるはずです。
  
6. 既存のすべてのポータルのプライベート エンドポイントについて、上の手順 1. から 3. を繰り返します。 

### <a name="validation-steps"></a>検証手順

1. Azure Purview Studio を正常に読み込むことができることを確認します。 DNS を再構成した後、新しい DNS ルーティングが有効になるまでに数分 (約 10 分) かかる場合があります。 すぐに読み込まれない場合は、数分待ってから、もう一度やり直してください。

2. ナビゲーションが失敗した場合は、nslookup `web.purview.azure.com` を実行します。これは、ポータルのプライベート エンドポイントに関連付けられているプライベート IP アドレスに解決されるはずです。

## <a name="frequently-asked-questions"></a>よく寄せられる質問  

Azure Purview でのプライベート エンドポイントのデプロイに関連する FAQ については、「[Azure Purview プライベート エンドポイントに関する FAQ](./catalog-private-link-faqs.md)」を参照してください。
 
## <a name="troubleshooting-guide"></a>トラブルシューティング ガイド 
Purview アカウントのプライベート エンドポイント構成のトラブルシューティングについては、「[Purview アカウントのプライベート エンドポイント構成のトラブルシューティング](./catalog-private-link-troubleshoot.md)」を参照してください。

## <a name="known-limitations"></a>既知の制限事項
Azure Purview プライベート エンドポイントに関連する現在の制限事項の一覧を表示するには、[Azure Purview プライベート エンドポイントの既知の制限事項](./catalog-private-link-troubleshoot.md#known-limitations)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [エンド ツー エンドのプライベート ネットワークをデプロイする](./catalog-private-link-end-to-end.md)
- [Purview Studio 用のプライベート ネットワークをデプロイする](./catalog-private-link-account-portal.md)
