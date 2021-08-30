---
title: プライベート エンドポイントを使用して Azure Purview へのアクセスをセキュリティで保護する
description: この記事では、Azure Purview アカウント用のプライベート エンドポイントを設定する方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: 8a5fb386cfbf8c31abc92d75d49854ca237d4978
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121721993"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>Azure Purview アカウントにプライベート エンドポイントを使用する

お使いの Azure Purview アカウントにプライベート エンドポイントを使用すると、仮想ネットワーク (VNet) 上のクライアントとユーザーが Azure Private Link を介してカタログに安全にアクセスできるようになります。 プライベート エンドポイントでは、対象の Azure Purview アカウントのために仮想ネットワークのアドレス空間の IP アドレスを使用します。 仮想ネットワーク上のクライアントと Azure Purview アカウントとの間のネットワーク トラフィックは、仮想ネットワークおよび Microsoft バックボーン ネットワーク上のプライベート リンクを経由します。 この操作により、パブリック インターネットへの露出が排除されます。

   :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="Azure Purview と Private Link アーキテクチャを示す図。":::

[Azure Purview の Private Link に関してよく寄せられる質問 (FAQ)](./catalog-private-link-faqs.md) に関する記事を参照してください。

## <a name="create-an-azure-purview-account-with-a-private-endpoint"></a>プライベート エンドポイントを備えた Azure Purview アカウントを作成する

1. [Azure portal](https://portal.azure.com) に移動し、自分の Azure Purview アカウントに移動します。

1. 基本情報を入力し、 **[ネットワーク]** タブで、接続方法を **[プライベート エンドポイント]** に設定します。 プライベート エンドポイントとペアリングする **サブスクリプション**、**仮想ネットワーク**、および **サブネット** の詳細を入力して、インジェスト プライベート エンドポイントを設定します。

   > [!NOTE]
   > インジェスト プライベート エンドポイントを作成するのは、Azure とオンプレミスの両方のソースについて、エンド ツー エンドのスキャン シナリオでネットワークの分離を有効にする場合のみです。 AWS ソースに対応するインジェスト プライベート エンドポイントは、現時点ではサポートされていません。

   :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Azure portal でプライベート エンドポイントを作成する方法を示すスクリーンショット。":::

1. 必要に応じて、各インジェスト プライベート エンドポイントの **プライベート DNS ゾーン** を設定することもできます。

1. **[+ 追加]** を選択して、自分の Azure Purview アカウントのプライベート エンドポイントを追加します。

1. **[プライベート エンドポイントの作成]** ページで、 **[Purview sub-resource]\(Purview サブ リソース\)** として **[アカウント]** を選択します。 仮想ネットワークとサブネットを選択し、DNS を登録するプライベート DNS ゾーンを選択します。 また、独自の DNS サーバーを使用することも、自分の仮想マシン上のホスト ファイルを使用して DNS レコードを作成することもできます。

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="[プライベート エンドポイントの作成] ページの選択を示すスクリーンショット。":::

1. **[OK]** を選択します。

1. **[確認および作成]** を選択します。 **[確認と作成]** ページで、Azure によって構成が検証されます。

1. "検証に成功しました" というメッセージが表示されたら、 **[作成]** を選択します。

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="アカウント作成の検証に成功したことを示すスクリーンショット。":::

## <a name="create-a-private-endpoint-for-the-azure-purview-web-portal"></a>Azure Purview Web ポータルのプライベート エンドポイントを作成する

1. 作成した Azure Purview アカウントに移動し、 **[設定]** で **[ネットワーク]**  >  **[プライベート エンドポイントの接続]** を選択します。

1. **[+ プライベート エンドポイント]** を選択して、新しいプライベート エンドポイントを作成します。

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="ポータルのプライベート エンドポイントの作成を示すスクリーンショット。":::

1. 基本情報を入力します。

1. **[リソース]** タブで、 **[リソースの種類]** として **[Microsoft.Purview/accounts]** を選択します。

1. **[リソース]** として、新しく作成された Azure Purview アカウントを選択します。 **[対象サブリソース]** として、 **[ポータル]** を選択します。
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="ポータルのプライベート エンドポイントの詳細を示すスクリーンショット。":::

1. **[構成]** タブで、仮想ネットワークとプライベート DNS ゾーンを選択します。 [概要] ページに移動し、 **[作成]** を選択してポータルのプライベート エンドポイントを作成します。

## <a name="private-dns-zone-requirements-for-private-endpoints"></a>プライベート エンドポイントに対するプライベート DNS ゾーンの要件

プライベート エンドポイントを作成すると、Azure Purview の DNS CNAME リソース レコードは、プレフィックスが `privatelink` のサブドメイン内の別名に更新されます。 既定で、`privatelink` サブドメインに対応する[プライベート DNS ゾーン](../dns/private-dns-overview.md)も作成されます。これには、プライベート エンドポイントの DNS A リソース レコードが含まれます。

プライベート エンドポイントがある仮想ネットワークの外部から Azure Purview エンドポイントの URL を解決すると、Azure Purview のパブリック エンドポイントに解決されます。 プライベート エンドポイントをホストしている仮想ネットワークから解決されると、Azure Purview エンドポイントの URL はプライベート エンドポイントの IP アドレスに解決されます。

たとえば、Azure Purview アカウント名が "PurviewA" の場合、プライベート エンドポイントをホストする仮想ネットワークの外部から解決されると、次のようになります。

| 名前 | 型 | 値 |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |

PurviewA の DNS リソース レコードは、プライベート エンドポイントをホストしている仮想ネットワーク内で解決されると、次のようになります。

| 名前 | 型 | 値 |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | A | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |

<br>

 > [!Important]
 > DNS フォワーダーを使用しないで、代わりにオンプレミスの DNS サーバーで A レコードを直接管理し、プライベート IP アドレスを介してエンドポイントを解決する場合は、DNS サーバーに追加の A レコードを作成することが必要になる場合があります。

| 名前 | 型 | 値 |
| ---------- | -------- | --------------- |
| `PurviewA.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.scan.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.catalog.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.proxy.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.guardian.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.manifest.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cdn.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.hub.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.catalog.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cseo.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datascan.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datashare.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datasource.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.policy.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.sensitivity.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |

<br> 

次の例は、仮想ネットワークの外部からの (Azure プライベート エンドポイントが構成されていない場合の) Azure Purview の DNS 名前解決を示しています。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="CorpNet の外部からの Azure Purview の名前解決を示すスクリーンショット。":::

次の例は、仮想ネットワーク内からの Azure Purview の DNS 名前解決を示しています。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="CorpNet 内からの Purview の名前解決を示すスクリーンショット。":::

プライベート エンドポイントの IP アドレスを接続文字列の完全修飾ドメイン名 (FQDN) に解決するように、DNS 設定を正しく構成することが重要です。

ネットワークでカスタム DNS サーバーを使用している場合は、クライアントで Azure Purview エンドポイントの FQDN をプライベート エンドポイントの IP アドレスに解決できる必要があります。 Private Link サブドメインを仮想ネットワークのプライベート DNS ゾーンに委任するように、DNS サーバーを構成します。 または、プライベート エンドポイントの IP アドレスを使用して `PurviewA.privatelink.purview.azure.com` の A レコードを構成します。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Azure Purview の名前解決を示す図。":::

詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」を参照してください。

## <a name="enable-access-to-azure-active-directory"></a>Azure Active Directory へのアクセスを有効にする

> [!NOTE]
> VM、VPN ゲートウェイ、または VNet ピアリング ゲートウェイは、パブリック インターネットにアクセスできる場合、Azure Purview ポータルと、プライベート エンドポイントで有効になっている Azure Purview アカウントにアクセスできます。 このため、残りの手順に従う必要はありません。 プライベート ネットワークで、ネットワーク セキュリティ グループの規則がすべてのパブリック インターネット トラフィックを拒否するように設定されている場合、Azure Active Directory (Azure AD) アクセスを有効にするためにいくつかの規則を追加する必要があります。 そのためには、この手順に従ってください。

これらの手順は、Azure VM から Azure Purview に安全にアクセスするために提供されています。 VPN または他の VNet ピアリング ゲートウェイを使用している場合も同様の手順に従う必要があります。

1. Azure portal で自分の VM に移動し、 **[設定]** の **[ネットワーク]** を選択します。 次に、 **[送信ポートの規則]**  >  **[送信ポートの規則を追加する]** を選択します。

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="アウトバウンド規則の追加を示すスクリーンショット。":::

1. **[送信セキュリティ規則の追加]** ペインで:

   1. **[宛先]** で **[サービス タグ]** を選択します。
   1. **[宛先サービス タグ]** で、 **[AzureActiveDirectory]** を選択します。
   1. **[宛先ポート範囲]** で、[*] を選択します。
   1. **[アクション]** で、 **[許可]** を選択します。
   1. **[優先度]** の値は、すべてのインターネット トラフィックを拒否した規則よりも高くする必要があります。
   
   ルールを作成します。

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="アウトバウンド規則の詳細の追加を示すスクリーンショット。":::

1. 同じ手順に従って、**AzureResourceManager** サービス タグを許可する別の規則を作成します。 Azure portal にアクセスする必要がある場合は、**AzurePortal** サービス タグの規則も追加できます。

1. VM に接続し、ブラウザーを開きます。 Ctrl + Shift + J キーを押してブラウザー コンソールに移動し、[ネットワーク] タブに切り替えてネットワーク要求を監視します。 URL ボックスに「web.purview.azure.com」と入力し、ご自分の Azure AD の資格情報を使用してサインインを試みます。 サインインはおそらく失敗します。コンソールの **[ネットワーク]** タブで、Azure AD が aadcdn.msauth.net にアクセスする試みがブロックされたことを確認できます。

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="サインインの失敗の詳細を示すスクリーンショット。":::

1. この場合、VM でコマンド プロンプトを開き、aadcdn.msauth.net に ping を実行し、その IP を取得してから、VM のネットワーク セキュリティ規則で IP の送信ポート規則を追加します。 **[宛先]** を **[IP アドレス]** に設定し、 **[宛先 IP アドレス]** を aadcdn の IP に設定します。 Azure Load Balancer と Azure Traffic Manager のため、Azure AD Content Delivery Network の IP は動的である可能性があります。 その IP を取得した後、それを VM のホスト ファイルに追加して、ブラウザーがその IP にアクセスして、Azure AD Content Delivery Network を取得するように強制することをお勧めします。

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="テスト ping を示すスクリーンショット。":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Azure AD Content Delivery Network 規則を示すスクリーンショット。":::

1. 新しい規則が作成された後、VM に戻り、お使いの Azure AD の資格情報を再度使用してサインインを試みます。 サインインに成功すると、Azure Purview ポータルを使用できるようになります。 ただし、Azure AD は、顧客のアカウントの種類に基づいてサインインするために、他のドメインにリダイレクトする場合があります。 たとえば、live.com アカウントの場合、Azure AD は、live.com にリダイレクトしてサインインします。その後、それらの要求は再度ブロックされます。 Microsoft 従業員アカウントの場合、Azure AD は、msft.sts.microsoft.com にアクセスしてサインイン情報を取得します。

   ブラウザーの **[ネットワーク]** タブでネットワーク要求をチェックして、ブロックされているドメインの要求を確認し、前の手順を再実行して IP を取得し、その IP の要求を許可するための送信ポート規則をネットワーク セキュリティ グループに追加します。 可能な場合は、URL と IP を VM のホスト ファイルに追加して DNS 解決を修正します。 サインイン ドメインの正確な IP 範囲がわかっている場合は、それらをネットワーク規則に直接追加することもできます。

1. これで、Azure AD のサインインが成功するはずです。 Azure Purview ポータルは正常に読み込まれますが、特定の Azure Purview アカウントにしかアクセスできないため、すべての Azure Purview アカウントの一覧表示は機能しません。 「`web.purview.azure.com/resource/{PurviewAccountName}`」と入力して、プライベート エンドポイントを正常に設定した Azure Purview アカウントに直接アクセスします。

## <a name="ingestion-private-endpoints-and-scanning-sources"></a>インジェスト プライベート エンドポイントとスキャン ソース

プライベート ネットワーク内、仮想ネットワーク内、およびプライベート エンドポイントの背後にあるソースをスキャンする必要があります。 Azure Purview DataMap にスキャンされているソースから送信されるメタデータのネットワーク分離を確保するには:

1. [こちらのセクション](#create-an-ingestion-private-endpoint)の手順に従って、インジェスト プライベート エンドポイントを有効にします。

1. セルフホステッド統合ランタイム (IR) を使用してソースをスキャンします。

    1. Azure SQL Server、Oracle、SAP など、オンプレミス タイプのすべてのソースは、現在、セルフホステッド IR ベースのスキャンでのみサポートされています。 セルフホステッド IR は、プライベート ネットワーク内で実行し、Azure 内の自分の仮想ネットワークとピアリングする必要があります。 [これらの手順](#create-an-ingestion-private-endpoint)に従って、ご自分のインジェスト プライベート エンドポイントで Azure 仮想ネットワークを有効にします。

    2. Azure Blob Storage や Azure SQL Database など、すべての Azure ソースの種類については、明示的にセルフホステッド IR を使用してスキャンを実行するように選択し、ネットワークの分離を確保する必要があります。 「[セルフホステッド統合ランタイムの作成および管理](manage-integration-runtimes.md)」の手順に従って、セルフホステッド IR を設定します。 その後、 **[統合ランタイム経由で接続]** ドロップダウンでそのセルフホステッド IR を選択して Azure ソースでのスキャンを設定し、ネットワークの分離を確保します。
    
       :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="セルフホステッド IR を使用して Azure スキャンを実行する方法を示すスクリーンショット。":::

> [!NOTE]
> インジェストにプライベート エンドポイントを使用する場合は、次のデータ ソースに対してのみ、スキャンに Azure 統合ランタイムを使用できます。
>
> - Azure Blob Storage
> - Azure Data Lake Gen 2
>
> 他のデータ ソースの場合は、セルフホステッド IR が必要です。 現在、セルフホステッド IR を使用して Azure ソースをスキャンする場合、MSI 資格情報の方法はサポートされていません。 その Azure ソースについては、サポートされているその他の資格情報の方法のいずれかを使用する必要があります。

## <a name="enable-a-private-endpoint-on-existing-azure-purview-accounts"></a>既存の Azure Purview アカウントでプライベート エンドポイントを有効にする

Azure Purview アカウントを作成した後、Azure Purview プライベート エンドポイントを追加するには、2 つの方法があります。

- Azure portal を使用する (Azure Purview アカウント)。
- Private Link センターを使用する。

### <a name="use-the-azure-portal-azure-purview-account"></a>Azure portal を使用する (Azure Purview アカウント)

1. Azure portal から Azure Purview アカウントに移動し、 **[設定]**  >  **[ネットワーク]** で **[プライベート エンドポイント接続]** を選択します。

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="アカウントのプライベート エンドポイントの作成方法を示すスクリーンショット。":::

1. **[+ プライベート エンドポイント]** を選択して、新しいプライベート エンドポイントを作成します。

1. 基本情報を入力します。

1. **[リソース]** タブで、 **[リソースの種類]** として **[Microsoft.Purview/accounts]** を選択します。

1. **[リソース]** として Azure Purview アカウントを選択し、 **[対象サブリソース]** として **[アカウント]** を選択します。

1. **[構成]** タブで、仮想ネットワークとプライベート DNS ゾーンを選択します。 [概要] ページに移動し、 **[作成]** を選択してアカウントのプライベート エンドポイントを作成します。

> [!NOTE]
> **[対象サブリソース]** に **[ポータル]** を選択した場合も同じ手順に従います。

#### <a name="create-an-ingestion-private-endpoint"></a>インジェスト プライベート エンドポイントを作成する

1. Azure portal から Azure Purview アカウントに移動し、 **[設定]**  >  **[ネットワーク]** で **[プライベート エンドポイント接続]** を選択します。

1. **[Ingestion private endpoint connections]\(インジェスト プライベート エンドポイント接続\)** タブに移動し、 **[+ 新規]** を選択して新しいインジェスト プライベート エンドポイントを作成します。

1. 基本情報と仮想ネットワークの詳細を入力します。

   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="プライベート エンドポイントの詳細の入力方法を示すスクリーンショット。":::

1. **[作成]** を選択して設定を終了します。

> [!NOTE]
> インジェスト プライベート エンドポイントは、前の手順で説明した Azure Purview ポータルのエクスペリエンスを介してのみ作成できます。 Private Link センターから作成することはできません。

### <a name="use-the-private-link-center"></a>Private Link センターを使用する

1. [Azure portal](https://portal.azure.com) にアクセスします。

1. ページの上部にある検索バーで「**private link**」を検索し、最初のオプションを選択して、 **[Private Link]** ペインに移動します。

1. **[+ 追加]** を選択し、基本的な詳細を入力します。

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Private Link センターからプライベート エンドポイントを作成する方法を示すスクリーンショット。":::

1. **[リソース]** として、既に作成済みの Azure Purview アカウントを選択します。 **[対象サブリソース]** として、 **[アカウント]** を選択します。

1. **[構成]** タブで、仮想ネットワークとプライベート DNS ゾーンを選択します。 [概要] ページに移動し、 **[作成]** を選択してアカウントのプライベート エンドポイントを作成します。

> [!NOTE]
> **[対象サブリソース]** に **[ポータル]** を選択した場合も同じ手順に従います。

## <a name="firewalls-to-restrict-public-access"></a>パブリック アクセスを制限するファイアウォール

パブリック インターネットからの Azure Purview アカウントへのアクセスを完全に遮断するには、これらの手順に従います。 この設定は、プライベート エンドポイントとインジェスト プライベート エンドポイントの両方の接続に適用されます。

1. Azure portal から Azure Purview アカウントに移動し、 **[設定]**  >  **[ネットワーク]** で **[プライベート エンドポイント接続]** を選択します。

1. **[ファイアウォール]** タブに移動し、トグルが **[拒否]** に設定されていることを確認します。

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="プライベート エンドポイントのファイアウォール設定を示すスクリーンショット。":::

## <a name="next-steps"></a>次の手順

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
