---
title: プライベート エンドポイントを使用して Purview へのアクセスをセキュリティで保護する
description: この記事では、Purview アカウントのプライベート エンドポイントを設定する方法について説明します
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: 5101e71f6caf23e48adc59c9afde402427aa9995
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112080419"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Purview アカウントのプライベート エンドポイントを使用する

Purview アカウントのプライベート エンドポイントを使用すると、仮想ネットワーク (VNet) 上のクライアントはプライベート リンクを介してカタログに安全にアクセスできるようになります。 プライベート エンドポイントでは、Purview アカウントの VNet アドレス空間からの IP アドレスが使用されます。 VNet 上のクライアントと Purview アカウントの間のネットワーク トラフィックは、VNet と Microsoft バックボーン ネットワーク上のプライベート リンクを経由することで、パブリック インターネットに露出することはありません。

   :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="Azure Purview と Private Link アーキテクチャ":::



[Azure Purview の Private Link に関してよく寄せられる質問 (FAQ)](./catalog-private-link-faqs.md) に関する記事を参照してください。

## <a name="create-purview-account-with-a-private-endpoint"></a>プライベート エンドポイントを備えた Purview アカウントの作成

1. [Azure portal](https://portal.azure.com)、Purview アカウントの順に移動します。

1. 基本情報を入力し、 **[ネットワーク]** タブで、[接続方法] を [プライベート エンドポイント] に設定します。プライベート エンドポイントとペアリングする **[サブスクリプション]、[仮想ネットワーク]、[サブネット]** の詳細を入力して、[インジェスト] プライベート エンドポイントを設定します。

   > [!NOTE]
   > インジェスト プライベート エンドポイントを作成するのは、Azure とオンプレミスの両方のソースについて、エンド ツー エンドのスキャン シナリオでネットワークの分離を有効にする場合のみです。 AWS ソースを処理するインジェスト プライベート エンドポイントは、現時点ではサポートされていません。

   :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Azure portal を使用してプライベート エンドポイントを作成する":::

1. また、必要に応じて、各インジェスト プライベート エンドポイントの **[プライベート DNS ゾーン]** を設定することもできます。

1. **[追加]** を選択して、Purview アカウントのプライベート エンドポイントを追加します。

1. [Create private endpoint]\(プライベート エンドポイントの作成\) ページで、[Purview sub-resource]\(Purview サブ リソース\) を **[アカウント]** に設定し、ご使用の仮想ネットワークとサブネットを選択し、DNS を登録するプライベート DNS ゾーンを選択します (独自の DNS サーバーを利用することも、仮想マシン上のホスト ファイルを使用して DNS レコードを作成することもできます)。

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="プライベート エンドポイント作成の選択":::

1. **[OK]** を選択します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 [確認および作成] ページが表示され、Azure によって構成が検証されます。

1. "検証に成功しました" というメッセージが表示されたら、 **[作成]** を選択します。

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="アカウント作成の &quot;検証に成功しました&quot;":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Purview Web ポータルのプライベート エンドポイントを作成する

1. 作成したばかりの Purview アカウントに移動し、[設定] セクションで [プライベート エンドポイント接続] を選択します。

1. **[+ プライベート エンドポイント]** を選択して、新しいプライベート エンドポイントを作成します。

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="ポータルのプライベート エンドポイントを作成する":::

1. 基本情報を入力します。

1. [リソース] タブで、[リソースの種類] として **[Microsoft.Purview/accounts]** を選択します。

1. [リソース] として、新しく作成した Purview アカウントを選択し、[対象サブリソース] として **[ポータル]** を選択します。
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="ポータルのプライベート エンドポイントの詳細":::

1. [構成] タブで、[仮想ネットワーク] と [プライベート DNS ゾーン] を選択します。[概要] ページに移動し、 **[作成]** をクリックして、ポータルのプライベート エンドポイントを作成します。

## <a name="private-dns-zone-requirements-for-private-endpoints"></a>プライベート エンドポイントに対するプライベート DNS ゾーンの要件
プライベート エンドポイントを作成すると、Purview の DNS CNAME リソース レコードは、プレフィックス `privatelink` を持つサブドメイン内のエイリアスに更新されます。 既定で、`privatelink` サブドメインに対応する[プライベート DNS ゾーン](../dns/private-dns-overview.md)も作成されます。これには、プライベート エンドポイントの DNS A リソース レコードが含まれます。
 
プライベート エンドポイントを持つ VNet の外部から Purview エンドポイントの URL を解決すると、Azure Purview のパブリック エンドポイントに解決されます。 プライベート エンドポイントをホストしている VNet から解決されると、Purview エンドポイントの URL はプライベート エンドポイントの IP アドレスに解決されます。

たとえば、Purview アカウント名が "PurviewA" の場合、プライベート エンドポイントをホストする VNet の外部から解決されると、次のようになります。

| Name | 種類 | 値 |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |
 
PurviewA の DNS リソース レコードは、プライベート エンドポイントをホストしている VNet 内で解決されると、次のようになります。
 
| Name | 種類 | 値 |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | A | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |
 
 > [!important]
 > DNS フォワーダーを使用しないで、代わりにオンプレミスの DNS サーバーで A レコードを直接管理し、プライベート IP アドレスを介してエンドポイントを解決している場合は、DNS サーバーに追加の A レコードを作成することが必要になる場合があります。

| Name | 種類 | 値 |
| ---------- | -------- | --------------- |
| `PurviewA.scan.Purview.azure.com` | A | \<private endpoint IP address\> |
| `PurviewA.catalog.Purview.azure.com` | A | \<private endpoint IP address\> |

<br> 

_VNet の外部からの、または Azure プライベート エンドポイントが構成されていない場合の、Azure Purview DNS 名前解決の例:_

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="外部の CorpNet からの Purview の名前解決":::

_VNet 内からの Azure Purview DNS 名前解決の例:_

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="内部の CorpNet からの Purview の名前解決":::

プライベート エンドポイントの IP アドレスを接続文字列の完全修飾ドメイン名 (FQDN) に解決するように、DNS 設定を正しく構成することが重要です。

ネットワークでカスタム DNS サーバーを使用している場合は、クライアントで Purview エンドポイントの FQDN をプライベート エンドポイントの IP アドレスに解決できる必要があります。 プライベート リンク サブドメインを VNet のプライベート DNS ゾーンに委任するように DNS サーバーを構成するか、プライベート エンドポイントの IP アドレスを使用して "PurviewA.privatelink.purview.azure.com" の A レコードを構成する必要があります。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Purview の名前解決":::

詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」をご覧ください

## <a name="enabling-access-to-azure-active-directory"></a>Azure Active Directory へのアクセスを有効にする

> [!NOTE]
> VM、VPN ゲートウェイ、または VNET ピアリング ゲートウェイはパブリック インターネットにアクセスできる場合、プライベート エンドポイントで有効になっている Purview ポータルおよび Purview アカウントにアクセスできるため、以下の残りの手順に従う必要はありません。 プライベート ネットワークで、ネットワーク セキュリティ グループの規則がすべてのパブリック インターネット トラフィックを拒否するように設定されている場合、AAD アクセスを有効にするためにいくつかの規則を追加する必要があります。 このためには、以下の手順に従ってください。

以下の手順は、Azure VM から Purview に安全にアクセスするためのものです。 VPN または他の Vnet ピアリング ゲートウェイを使用している場合も同様の手順に従う必要があります。

1. Azure portal で VM に移動し、[設定] セクションの [ネットワーク] タブを選択します。 次に、[送信ポートの規則] を選択し、[送信ポートの規則を追加する] をクリックします。

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="アウトバウンド規則の追加":::

2. [送信ポートの規則を追加する] ブレードで、 *[宛先]* として [サービス タグ] を選択し、[宛先サービス タグ] として **[AzureActiveDirectory]** を選択し、[宛先ポート範囲] として [*] を、[アクション] として [許可] を選択します。 **[優先順位] は、すべてのインターネットを拒否した規則よりも高くする必要があります**。 ルールを作成します。

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="アウトバウンド規則の詳細の追加":::

3. 同じ手順に従って、'**AzureResourceManager**' サービス タグを許可する別の規則を作成します。 Azure portal にアクセスする必要がある場合は、'*AzurePortal*' サービス タグの規則も追加できます。

4. VM に接続してブラウザーを開き、ブラウザー コンソールに移動し (Ctrl + Shift + J キー)、[ネットワーク] タブに切り替えてネットワーク要求を監視します。 URL ボックスに 「web.purview.azure.com」と入力し、AAD の資格情報を使用してログインを試みます。 おそらくログインに失敗する可能性があります。コンソールの [ネットワーク] タブで、AAD が access aadcdn.msauth.net にアクセスしようとしていますが、ブロックされていることを確認できます。

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="ログイン失敗の詳細":::

5. この場合は、VM でコマンド プロンプトを開いて、この URL (aadcdn.msauth.net) に ping を実行した後、VM のネットワーク セキュリティ規則で IP の送信ポート規則を追加します。 [宛先] を [IP アドレス] に設定し、[宛先 IP アドレス] を aadcdn の IP に設定します。 ロード バランサーとトラフィック マネージャーのために、AAD CDN の IP が動的になる場合があります。 その IP を取得したら、それを VM のホスト ファイルに追加して、ブラウザーがその IP にアクセスして、AAD CDN を取得するように強制します。

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="ping のテスト":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="AAD CDN 規則":::

6. 新しい規則を作成したら、VM に戻り、Azure Active Directory の資格情報を再度使用して、ログインを試みます。 ログインに成功すると、Purview ポータルを使用できるようになります。 ただし、場合によっては、AAD は他のドメインにリダイレクトし、使用されているアカウントの種類に基づいてログインします。 たとえば、live.com アカウントの場合、AAD は live.com にリダイレクトしてログインし、これらの要求は再度ブロックされます。 Microsoft の従業員アカウントの場合、AAD はログイン情報について msft.sts.microsoft.com にアクセスします。 ブラウザーの [ネットワーク] タブでネットワーク要求をチェックして、ブロックされているドメインの要求を確認し、前の手順を再実行して IP を取得し、その IP の要求を許可するための送信ポート規則をネットワーク セキュリティ グループに追加します (可能な場合は、URL と IP を VM のホスト ファイルに追加して DNS 解決を修正します)。 ログイン ドメインの正確な IP 範囲がわかっている場合は、それらをネットワーク規則に直接追加することもできます。

7. これで、Azure Active Directory へのログインが成功するようになるはずです。 Purview ポータルは正常に読み込まれますが、特定の Purview アカウントにしかアクセスできないため、すべての Purview アカウントの一覧表示は機能しません。 「`web.purview.azure.com/resource/{PurviewAccountName}`」と入力して、プライベート エンドポイントを正常にセットアップした Purview アカウントに直接アクセスします。
 
## <a name="ingestion-private-endpoints-and-scanning-sources-in-private-networks-vnets-and-behind-private-endpoints"></a>インジェスト プライベート エンドポイントと、プライベート ネットワーク、Vnet、およびプライベート エンドポイントの背後のスキャン ソース

Purview DataMap にスキャンされているソースから送信されるメタデータのネットワーク分離を確保する場合は、以下の手順のようにします。

1. **こちら** のセクションの手順に従って、[インジェスト プライベート エンドポイント](#creating-an-ingestion-private-endpoint)を有効にします。

1. **セルフホステッド IR** を使用してソースをスキャンします。
 
    1. SQL Server、Oracle、SAP などのすべてのオンプレミスのソースの種類は、現在、セルフホステッド IR ベースのスキャンでのみサポートされています。 セルフホステッド IR は、プライベート ネットワーク内で実行し、Azure の Vnet とピアリングする必要があります。 その後、[下](#creating-an-ingestion-private-endpoint)の手順に従って、インジェスト プライベート エンドポイントで Azure Vnet を有効にする必要があります。 

    2. Azure Blob Storage や Azure SQL Database など、すべての **Azure** ソースの種類については、明示的にセルフホステッド IR を使用してスキャンを実行するように選択し、ネットワークの分離を確保する必要があります。 「[セルフホステッド統合ランタイムの作成および管理](manage-integration-runtimes.md)」の手順に従って、セルフホステッド IR を設定します。 その後、 **[Connect via integration runtime]\(統合ランタイムを介して接続する\)** ドロップダウンでそのセルフホステッド IR を選択して、ネットワークの分離を確保します。 
    
    :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="セルフホステッド IR を使用した Azure スキャンの実行":::

> [!NOTE]
> インジェストにプライベート エンドポイントを使用する場合は、次のデータ ソースに対してのみ、スキャンに Azure Integration Runtime を使用できます。
> - Azure Blob Storage 
> - Azure Data Lake Gen 2
>  
> 他のデータ ソースの場合は、セルフホステッド統合ランタイムが必要です。
> 現在、セルフホステッド IR を使用して Azure ソースをスキャンする場合、MSI 資格情報の方法はサポートされていません。 その Azure ソースについては、サポートされているその他の資格情報の方法のいずれかを使用する必要があります。

## <a name="enable-private-endpoint-on-existing-purview-accounts"></a>既存の Purview アカウントでプライベート エンドポイントを有効にする

Purview アカウントを作成した後、Purview プライベート エンドポイントを追加するには、次の 2 つの方法があります。

- Azure portal (Purview アカウント) の使用
- プライベート リンク センターの使用

### <a name="using-the-azure-portal-purview-account"></a>Azure portal (Purview アカウント) の使用

1. Azure portal から Purview アカウントに移動し、 **[設定]** の **[ネットワーク]** セクションで [プライベート エンドポイント接続] を選択します。

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="アカウントのプライベート エンドポイントの作成":::

1. **[+ プライベート エンドポイント]** を選択して、新しいプライベート エンドポイントを作成します。

1. 基本情報を入力します。

1. [リソース] タブで、 **[リソースの種類]** として **[Microsoft.Purview/accounts]** を選択します。

1. **[リソース]** として Purview アカウントを選択し、[対象サブリソース] として **[アカウント]** を選択します。

1. [構成] タブで、 **[仮想ネットワーク]** と **[プライベート DNS ゾーン]** を選択します。[概要] ページに移動し、 **[作成]** を選択してポータルのプライベート エンドポイントを作成します。

> [!NOTE]
> [対象サブリソース] として **[ポータル]** を選択した場合も、上記と同じ手順に従う必要があります。

#### <a name="creating-an-ingestion-private-endpoint"></a>インジェスト プライベート エンドポイントの作成

1. Azure portal から Purview アカウントに移動し、 **[設定]** の **[ネットワーク]** セクションで [プライベート エンドポイント接続] を選択します。

3. **[Ingestion private endpoint connections]\(インジェスト プライベート エンドポイント接続\)** タブに移動し、 **[+New]\(+ 新規\)** を選択して新しいインジェスト プライベート エンドポイントを作成します。

4. 基本的な情報と Vnet の詳細を入力します。
 
   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="プライベート エンドポイントの詳細を入力":::

5. **[作成]** をクリックして設定を終了します。

> [!NOTE]
> インジェスト プライベート エンドポイントは、Azure portal の Purview エクスペリエンスを介してのみ作成できます。 プライベート リンク センターから作成することはできません。

### <a name="using-the-private-link-center"></a>プライベート リンク センターの使用

1. [Azure Portal](https://portal.azure.com) に移動します。

2. ページの上部にある検索バーで "プライベート リンク" を検索し、最初のオプションをクリックして、[プライベート リンク] ブレードに移動します。

3. **[+ 追加]** を選択して、基本的な詳細を入力します。

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="プライベート リンク センターから PE を作成する":::

4. [リソース] として、既に作成している Purview アカウントを選択し、[対象サブリソース] として **[アカウント]** を選択します。

5. [構成] タブで、[仮想ネットワーク] と [プライベート DNS ゾーン] を選択します。[概要] ページに移動し、 **[作成]** を選択してアカウントのプライベート エンドポイントを作成します。

> [!NOTE]
> [対象サブリソース] として **[ポータル]** を選択した場合も、上記と同じ手順に従う必要があります。

## <a name="firewalls-to-restrict-public-access"></a>パブリック アクセスを制限するファイアウォール

パブリック インターネットからの Purview アカウントへのアクセスを完全に遮断するには、下の手順に従います。 この設定は、プライベート エンドポイントとインジェスト プライベート エンドポイントの両方の接続に適用されます。

1. Azure portal から Purview アカウントに移動し、 **[設定]** の **[ネットワーク]** セクションで [プライベート エンドポイント接続] を選択します。

3. [ファイアウォール] タブに移動し、トグルが **[拒否]** に設定されていることを確認します。

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="プライベート エンドポイントのファイアウォール設定":::

## <a name="next-steps"></a>次のステップ

- [Azure Purview Data Catalog の参照](how-to-browse-catalog.md)

- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
