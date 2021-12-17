---
title: Azure Private Link を使用して、ネットワークを Azure Arc に安全に接続する
description: Azure Private Link を使用して、ネットワークを Azure Arc に安全に接続する方法について説明します。
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 10b306ef659903524c5a6a62c24ffe36e074666a
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399795"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-arc"></a>Azure Private Link を使用して、ネットワークを Azure Arc に安全に接続する

[Azure Private Link](../../private-link/private-link-overview.md) を使用すると、プライベート エンドポイントを使用して Azure PaaS サービスを仮想ネットワークに安全に接続できます。 多くのサービスでは、リソースごとにエンドポイントを設定するだけです。 これは、オンプレミスまたはマルチクラウドのサーバーを Azure Arc に接続し、パブリック ネットワークを使用する代わりに、Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) またはサイト間 [VPN 接続](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 経由ですべてのトラフィックを送信できることを意味します。

Azure Arc 対応サーバー以降では、プライベート リンク スコープ モデルを使用して、複数のサーバーまたはマシンで 1 つのプライベート エンドポイントを使用して Azure Arc リソースと通信できるようにすることができます。

この記事では、Azure Arc プライベート リンク スコープ (プレビュー) を使用する場面とその設定方法について説明します。

> [!NOTE]
> Azure Arc プライベート リンク スコープ (プレビュー) は、すべての商用クラウド リージョンで利用できます。現在、米国政府機関のクラウドでは利用できません。

## <a name="advantages"></a>長所

Private Link を使用すると、次のことができます。

- パブリック ネットワーク アクセスを開かずに Azure Arc にプライベートに接続する。
- Azure Arc 対応のマシンまたはサーバーからのデータが、認可されたプライベート ネットワーク経由でのみアクセスされるようにする。 これには、マシンまたはサーバーにインストールされている [VM 拡張機能](manage-vm-extensions.md)からのデータも含まれます。これにより、デプロイ後の管理と監視のサポートが提供されます。
- プライベート エンドポイント経由で接続される特定の Azure Arc 対応サーバーおよびその他の Azure サービス リソース (Azure Monitor など) を定義することで、プライベート ネットワークからのデータ流出を防止する。
- ExpressRoute と Private Link を使用して、オンプレミスのプライベート ネットワークを Azure Arc に安全に接続する。
- すべてのトラフィックを Microsoft Azure のバックボーン ネットワーク内に収める。

詳細については、[Private Link の主な利点](../../private-link/private-link-overview.md#key-benefits)に関するページを参照してください。

## <a name="how-it-works"></a>しくみ

Azure Arc プライベート リンク スコープ (プレビュー) によって、プライベート エンドポイント (およびそれが含まれている仮想ネットワーク) が Azure リソース (この場合は Azure Arc 対応サーバー) に接続されます。 Azure Arc 対応サーバーでサポートされる VM 拡張機能 (Azure Automation Update Management や Azure Monitor など) のいずれかを有効にすると、それらのリソースが他の Azure リソースに接続されます。 例:

- Log Analytics ワークスペース。Azure Automation Update Management、Azure Automation 変更履歴とインベントリ、Azure Monitor の VM 分析情報、Log Analytics エージェントを使用した Azure Monitor ログ収集で必要となります。
- Azure Automation アカウント。Update Management と変更履歴とインベントリで必要となります。
- Azure Key Vault
- Azure Blob Storage。カスタム スクリプト拡張機能で必要となります。

:::image type="content" source="./media/private-link-security/private-link-topology.png" alt-text="基本的なリソース トポロジの図" border="true":::

Azure Arc 対応サーバーから他の Azure リソースに接続するには、サービスごとに Private Link を構成する必要があります。これは省略可能ですが、推奨されています。 Azure Private Link では、サービスごとに個別の構成が必要です。

Azure のサービス用の Private Link の構成の詳細については、[Azure Automation](../../automation/how-to/private-link-security.md)、[Azure Monitor](../../azure-monitor/logs/private-link-security.md)、[Azure Key Vault](../../key-vault/general/private-link-service.md)、または [Azure Blob Storage](../../private-link/tutorial-private-endpoint-storage-portal.md) に関する記事を参照してください。

> [!IMPORTANT]
> Azure Private Link は、現在、一般提供されています。 プライベート エンドポイントと Private Link サービス (Standard ロード バランサーの背後にあるサービス) の両方が一般提供されています。 さまざまな Azure PaaS がさまざまなスケジュールに従って Azure Private Link にオンボードされます。 Private Link 上の Azure PaaS の更新された状態については、[プライベート リンクの可用性](../../private-link/availability.md)に関する記事を参照してください。 既知の制約については、[プライベート エンドポイント](../../private-link/private-endpoint-overview.md#limitations)と [Private Link サービス](../../private-link/private-link-service-overview.md#limitations)の説明を参照してください。

* VNet でプライベート エンドポイントを使用すると、Azure Arc 対応サーバーのエンドポイントのパブリック IP を使用するのではなく、ネットワークのプールのプライベート IP を介してそれらのエンドポイントに接続することができます。 これにより、要求されていない送信トラフィックに対して VNet を開くことなく、Azure Arc 対応サーバーのリソースを使用し続けることができます。

* プライベート エンドポイントからリソースへのトラフィックは、パブリック ネットワークにはルーティングされず、Microsoft Azure のバックボーンを経由します。

* パブリック ネットワークからの取り込みとクエリを許可または拒否するように、各コンポーネントを構成できます。 これによりリソース レベルの保護が提供されるため、特定のリソースへのトラフィックを制御できます。

## <a name="restrictions-and-limitations"></a>制限事項と制約事項

Azure Arc 対応サーバーのプライベート リンク スコープ オブジェクトには、Private Link の設定を計画するときに考慮に入れる必要のある制限がいくつかあります。

- 仮想ネットワークには、最大 1 つの Azure Arc プライベート リンク スコープを関連付けることができます。

- Azure Arc 対応のマシンまたはサーバーのリソースは、1 つの Azure Arc 対応サーバー プライベート リンク スコープにのみ接続できます。

- すべてのオンプレミス マシンでは、同じ DNS フォワーダーを使用して、正しいプライベート エンドポイント情報 (FQDN レコード名とプライベート IP アドレス) を解決することで、同じプライベート エンドポイントを使用する必要があります。 詳細については、「[Azure プライベート エンドポイントの DNS 構成](../../private-link/private-endpoint-dns.md)」を参照してください。

- Azure Arc 対応のサーバーと Azure Arc プライベート リンク スコープは、同じ Azure リージョンに存在する必要があります。 プライベート エンドポイントと仮想ネットワークも同じ Azure リージョンに存在する必要がありますが、このリージョンは、Azure Arc プライベート リンク スコープと Arc 対応サーバーのリージョンとは異なるリージョンにできます。

- プレビュー期間中は、オンプレミスのネットワーク ファイアウォールを介して Azure Active Directory および Azure Resource Manager のサービス タグへのトラフィックが許可される必要があります。

- 使用するその他の Azure サービス (Azure Monitor など) のために、仮想ネットワーク内に独自のプライベート エンドポイントが必要となります。

- 現在、Azure Arc 対応サーバーのプライベート リンク スコープは、Azure 米国政府機関リージョンではご利用いただけません。

## <a name="planning-your-private-link-setup"></a>Private Link のセットアップを計画する

プライベート リンク経由でサーバーを Azure Arc に接続するには、次のことが実現されるようにネットワークを構成する必要があります。

1. [サイト間 VPN](../../vpn-gateway/tutorial-site-to-site-portal.md) または [ExpressRoute 回線](../../expressroute/expressroute-howto-linkvnet-arm.md) を使用して、オンプレミスのネットワークと Azure Virtual Network の間に接続が既に確立されている。

1. Azure Arc プライベート リンク スコープ (プレビュー) をデプロイする。これは、プライベート エンドポイント経由で Azure Arc と通信できるマシンまたはサーバーを制御し、プライベート エンドポイントを使用してそれを Azure の仮想ネットワークに関連付けます。

1. プライベート エンドポイント アドレスを解決するように、ローカル ネットワーク上の DNS 構成を更新する。

1. Azure Active Directory と Azure Resource Manager へのアクセスを許可するようにローカル ファイアウォールを構成する。 これは一時的な手順であり、これらのサービスのプライベート エンドポイントがプレビューに移行するときには必要ではなくなります。

1. Azure Arc 対応サーバーに登録されているマシンまたはサーバーをプライベート リンク スコープに関連付ける。

1. 必要に応じて、マシンまたはサーバーが管理されている (次のような) 他の Azure サービスのプライベート エンドポイントをデプロイする。

    - Azure Monitor
    - Azure Automation
    - Azure Blob Storage
    - Azure Key Vault

この記事では、ExpressRoute 回線またはサイト間 VPN 接続が既に設定されていることを前提としています。

## <a name="network-configuration"></a>ネットワーク構成

Azure Arc 対応サーバーは複数の Azure サービスと統合され、クラウドの管理とガバナンスをハイブリッド マシンまたはサーバーにもたらします。 これらのサービスのほとんどでは既にプライベート エンドポイントが提供されていますが、これらのサービスによってプライベート エンドポイントが提供されるまで、インターネット経由で Azure Active Directory と Azure Resource Manager にアクセスできるように、ファイアウォールとルーティング規則を構成する必要があります。

これを実現する方法は 2 つあります。

- インターネットに送信されるすべてのトラフィックを Azure VPN または ExpressRoute 回線経由でルーティングするようにネットワークが構成されている場合は、Azure のサブネットに関連付けられたネットワーク セキュリティ グループ (NSG) を構成し、[サービス タグ](../../virtual-network/service-tags-overview.md)を使用して Azure AD および Azure への送信 TCP 443 (HTTPS) アクセスを許可できます。 NSG ルールは次のようになります。

    |設定 |Azure AD ルール | Azure ルール |
    |--------|--------------|-----------------------------|
    |ソース |仮想ネットワーク |仮想ネットワーク |
    |Source port ranges |* |* |
    |到着地 |サービス タグ |サービス タグ |
    |宛先サービス タグ |AzureActiveDirectory |AzureResourceManager |
    |宛先ポート範囲 |443 |443 |
    |プロトコル |Tcp |Tcp |
    |アクション |Allow |Allow |
    |優先度 |150 (インターネット アクセスをブロックするルールよりも低い必要があります) |151 (インターネット アクセスをブロックするルールよりも低い必要があります) |
    |名前 |AllowAADOutboundAccess |AllowAzOutboundAccess |

- ダウンロード可能なサービス タグ ファイルを使用して、Azure AD と Azure への送信 TCP 443 (HTTPS) アクセスを許可するように、ローカル ネットワーク上のファイアウォールを構成します。 この JSON ファイルには、Azure AD と Azure によって使用されるすべてのパブリック IP アドレスの範囲が含まれており、毎月更新されて変更が反映されます。 Azure AD のサービス タグは `AzureActiveDirectory` で、Azure のサービス タグは `AzureResourceManager` です。 ファイアウォール規則を構成する方法については、ネットワーク管理者とネットワーク ファイアウォール ベンダーにお問い合わせください。

ネットワーク トラフィックのフローについては、「[しくみ](#how-it-works)」セクションの視覚的な図を参照してください。

## <a name="create-a-private-link-scope"></a>プライベート リンク スコープを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal で **[リソースの作成]** に移動して、「**Azure Arc プライベート リンク スコープ**」を検索します。 または、次のリンクを使用して、ポータルの [[Azure Arc プライベート リンク スコープ]](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2FprivateLinkScopes) ページを開きます。

    :::image type="content" source="./media/private-link-security/find-scope.png" alt-text="プライベート リンク スコープを見つける" border="true":::

1. **［作成］** を選択します

1. サブスクリプションとリソース グループを選択します。 プレビュー期間中は、仮想ネットワークと Azure Arc 対応サーバーが、Azure Arc プライベート リンク スコープと同じサブスクリプションにある必要があります。

1. Azure Arc プライベート リンク スコープに名前を指定します。 意味のある明確な名前を使用することをお勧めします。

   必要に応じて、この Azure Arc プライベート リンク スコープ (プレビュー) に関連付けられているすべての Azure Arc 対応マシンまたはサーバーに、プライベート エンドポイントを介してサービスにデータを送信することを要求できます。 **[パブリック ネットワーク アクセスを有効にする]** を選択した場合、この Azure Arc プライベート リンク スコープ (プレビュー) に関連付けられているマシンまたはサーバーでは、プライベート ネットワークとパブリック ネットワークの両方を介してサービスと通信できます。 この設定は、設定を変更したい場合、スコープを作成した後に変更できます。

1. **[確認および作成]** を選択します。

    :::image type="content" source="./media/private-link-security/create-private-link-scope.png" alt-text="プライベート リンク スコープの作成" border="true":::

1. 検証をパスしたら、 **[作成]** を選択します。

## <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成

Azure Arc プライベート リンク スコープ (プレビュー) を作成したら、プライベート エンドポイントを使用して 1 つ以上の仮想ネットワークに接続する必要があります。 プライベート エンドポイントによって、仮想ネットワーク アドレス空間のプライベート IP で Azure Arc サービスへのアクセスが公開されます。

1. スコープ リソースで、左側のリソース メニューの **[プライベート エンドポイント接続]** を選択します。 **[追加]** を選択して、エンドポイント作成プロセスを開始します。 Private Link センターで開始された接続をここで承認するには、そのリンクを選択し、 **[承認]** を選択します。

    :::image type="content" source="./media/private-link-security/create-private-endpoint.png" alt-text="プライベート エンドポイントの作成" border="true":::

1. サブスクリプション、リソース グループ、およびエンドポイントの名前と、それを有効にするリージョンを選択します。 リージョンは、接続する VNet のリージョンと同じにする必要があります。

1. **[Next:リソース]** を選択します。

1. **[リソース]** ページで、次の手順を実行します。

   a. Azure Arc プライベート スコープ リソースを含む **[サブスクリプション]** を選択します。

   b. **[リソースの種類]** には、**Microsoft.HybridCompute/privateLinkScopes** を選択します。

   c. **[リソース]** ドロップダウンから、前に作成したプライベート リンク スコープを選択します。

   d. **Next:構成 >** をクリックします。

    :::image type="content" source="./media/private-link-security/create-private-endpoint-configuration.png" alt-text="プライベート エンドポイントの作成を完了する" border="true":::

1. **[構成]** ページで、次の手順を実行します。

   a. Azure Monitor リソースに接続する **[仮想ネットワーク]** と **[サブネット]** を選択します。 

   b. **[プライベート DNS ゾーンとの統合]** で **[はい]** を選択して、新しいプライベート DNS ゾーンを自動で作成します。 実際の DNS ゾーンは、下のスクリーンショットに示されているものとは異なる場合があります。

     > [!NOTE]
     > **[いいえ]** を選択して DNS レコードを手動で管理する場合は、最初に、このプライベート エンドポイントとプライベート スコープ構成を含め、プライベート リンクの設定を完了させます。 次に、「[Azure プライベート エンドポイントの DNS 構成](../../private-link/private-endpoint-dns.md)」の手順に従って、DNS を構成します。 プライベート リンクの設定の準備で、空のレコードを作成しないようにしてください。 作成する DNS レコードによって、既存の設定がオーバーライドされ、Azure Arc 対応サーバーとの接続が影響を受ける可能性があります。

   c.    **[Review + create]\(レビュー + 作成\)** を選択します。

   d.    検証をパスします。

   e.    **［作成］** を選択します

## <a name="configure-on-premises-dns-forwarding"></a>オンプレミスの DNS 転送を構成する

オンプレミスのマシンまたはサーバーでは、プライベート リンクの DNS レコードをプライベート エンドポイントの IP アドレスに解決できる必要があります。 これを構成する方法は、Azure のプライベート DNS ゾーンを使用して DNS レコードを管理しているか、オンプレミスで独自の DNS サーバーを使用しているか、および構成しているサーバーの数によって異なります。

### <a name="dns-configuration-using-azure-integrated-private-dns-zones"></a>Azure 統合プライベート DNS ゾーンを使用した DNS 構成

プライベート エンドポイントの作成時に Azure Arc 対応サーバーとゲスト構成のプライベート DNS ゾーンを設定した場合、オンプレミスのマシンまたはサーバーでは、プライベート エンドポイントのアドレスを正しく解決するために、組み込みの Azure DNS サーバーに DNS クエリを転送できる必要があります。 Azure に DNS フォワーダー (専用の VM または DNS プロキシが有効になっている Azure Firewall インスタンス) が必要です。その後、プライベート エンドポイントの IP アドレスを解決するためにクエリを Azure に転送するように、オンプレミスの DNS サーバーを構成できます。

プライベート エンドポイントのドキュメントでは、[DNS フォワーダーを使用してオンプレミスのワークロード](../../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)を構成するためのガイダンスを提供しています。

### <a name="manual-dns-server-configuration"></a>DNS サーバーの手動構成

プライベート エンドポイントの作成時に Azure プライベート DNS ゾーンの使用をオプトアウトした場合は、オンプレミスの DNS サーバーに必要な DNS レコードを作成する必要があります。

1. Azure portal に移動します。

1. 仮想ネットワークとプライベート リンク スコープに関連付けられているプライベート エンドポイント リソースに移動します。

1. 左側のペインで **[DNS 構成]** を選択すると、DNS サーバーに設定する必要がある DNS レコードと対応する IP アドレスの一覧が表示されます。 FQDN と IP アドレスは、プライベート エンドポイントに対して選択したリージョンとサブネットで使用可能な IP アドレスに基づいて変更されます。

    :::image type="content" source="./media/private-link-security/dns-configuration.png" alt-text="[DNS の構成] の詳細" border="true":::

1. DNS サーバー ベンダーのガイダンスに従って、ポータルのテーブルと一致させるために必要な DNS ゾーンと A レコードを追加します。 ネットワークに適切にスコープ設定された DNS サーバーを選択してください。 この DNS サーバーを使用するすべてのマシンまたはサーバーでは、プライベート エンドポイントの IP アドレスを解決するようになります。これらは、Azure Arc プライベート リンク スコープ (プレビュー) に関連付けられている必要があります。そうしないと、接続が拒否されます。

### <a name="single-server-scenarios"></a>単一サーバーのシナリオ

Private Link を使用していくつかのマシンまたはサーバーをサポートすることのみを計画している場合は、ネットワーク全体の DNS 構成を更新したくない場合があります。 この場合、プライベート エンドポイントのホスト名と IP アドレスをオペレーティング システムの **hosts** ファイルに追加できます。 OS の構成によって、hosts ファイルはホスト名を IP アドレスに解決するための主要な方法または代替の方法になります。

#### <a name="windows"></a>Windows

1. 管理者特権を持つアカウントを使用して、**C:\Windows\System32\drivers\etc\hosts** を開きます。

1. 「[DNS サーバーの手動構成](#manual-dns-server-configuration)」の手順 3 の表に示すように、プライベート エンドポイントの IP とホスト名を追加します。 hosts ファイルでは、最初に IP アドレスがあり、その後にスペースとホスト名が続いている必要があります。

1. 変更を加えたファイルを保存します。 最初に別のディレクトリに保存してから、ファイルを元のパスにコピーする必要がある場合があります。

#### <a name="linux"></a>Linux

1. **sudoers** 権限を持つアカウントを使用し、`sudo nano /etc/hosts` を実行して hosts ファイルを開きます。

1. 「[DNS サーバーの手動構成](#manual-dns-server-configuration)」の手順 3 の表に示すように、プライベート エンドポイントの IP とホスト名を追加します。 hosts ファイルでは、最初に IP アドレスがあり、その後にスペースとホスト名が続いている必要があります。

1. 変更を加えたファイルを保存します。

## <a name="connect-to-an-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーに接続する

> [!NOTE]
> プライベート エンドポイントを持つ Azure Arc Connected Machine Agent のサポートされる最低限のバージョンはバージョン 1.4 です。 ポータルで生成される Azure Arc 対応サーバー デプロイ スクリプトでは、最新バージョンがダウンロードされます。

### <a name="configure-a-new-azure-arc-enabled-server-to-use-private-link"></a>Private Link を使用するように新しい Azure Arc 対応サーバーを構成する

マシンまたはサーバーを Azure Arc 対応サーバーに初めて接続する場合は、必要に応じて、プライベート リンク スコープに接続できます。 次の手順に従います。 

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

1. **[サーバー - Azure Arc]** に移動します。

1. **[サーバー - Azure Arc]** ページで、左上の **[追加]** を選択します。

1. **[Azure Arc でサーバーを追加]** ページで、デプロイ シナリオに応じて **[単一サーバーの追加]** または **[複数のサーバーの追加]** を選択し、 **[スクリプトの生成]** を選択します。

1. **[スクリプトの生成]** ページで、Azure 内でマシンを管理するサブスクリプションとリソース グループを選択します。 マシンのメタデータが格納される Azure の場所を選択します。 この場所は、リソース グループの場所と同じ場合も異なる場合もあります。

1. **[Prerequisites]\(前提条件\)** ページで情報を確認し、 **[次: リソースの詳細]** を選択します。

1. **[リソースの詳細]** ページで、以下を設定します。

    1. **[リソース グループ]** ドロップダウンの一覧で、マシンを管理するリソース グループを選択します。
    1. **[地域]** ドロップダウン リストで、マシンまたはサーバーのメタデータを格納する Azure リージョンを選択します。
    1. **[オペレーティング システム]** ドロップダウン リストで、スクリプトを実行するように構成されているオペレーティング システムを選択します。
    1. **[ネットワーク接続]** で、 **[プライベート エンドポイント (プレビュー)]** を選択し、ドロップダウン リストからパート 1 で作成した Azure Arc プライベート リンク スコープを選択します。

       :::image type="content" source="./media/private-link-security/arc-enabled-servers-create-script.png" alt-text="[プライベート エンドポイント] 接続オプションの選択" border="true":::

    1. **タグ** を選択します。

1. **[複数のサーバーの追加]** を選択した場合は、 **[認証]** ページで、ドロップダウン リストから Azure Arc 対応サーバー用に作成されたサービス プリンシパルを選択します。 Azure Arc 対応サーバー用のサービス プリンシパルを作成していない場合は、まず、[サービス プリンシパルを作成する方法](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)を確認して、必要なアクセス許可と作成手順について理解します。 **[次: タグ]** を選択して続行します。

1. **[タグ]** ページで、提案されている既定の **物理的な場所のタグ** を確認し、値を入力するか、**カスタム タグ** を 1 つ以上指定して標準をサポートします。

1. **[Next:Download and run script]\(次: スクリプトをダウンロードして実行する\)** を選択します。

1. **[Download and run script]\(次: スクリプトをダウンロードして実行する\)** ページで概要情報を確認し、 **[ダウンロード]** を選択します。 引き続き変更が必要な場合は、 **[前へ]** を選択します。

スクリプトをダウンロードしたら、特権 (管理者またはルート) アカウントを使用して、マシンまたはサーバーでそれを実行する必要があります。 ネットワーク構成によっては、インターネットにアクセスできるコンピューターからエージェントをダウンロードし、それをマシンまたはサーバーに転送してから、エージェントへのパスを使用してスクリプトを変更することが必要になる場合があります。 

Windows エージェントは [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent) から、Linux エージェントは [https://packages.microsoft.com](https://packages.microsoft.com) からダウンロードできます。 OS 配布ディレクトリで **azcmagent** の最新バージョンを見つけて、ローカルのパッケージ マネージャーを使用してインストールします。 

このスクリプトでは、完了後に、オンボードが正常に完了したかどうかを通知するステータス メッセージが返されます。

> [!NOTE]
> Linux サーバーに Connected Machine Agent をデプロイする場合、ネットワークの接続チェック中に 5 分の遅延が発生し、ファイアウォールが正しく構成されている場合でも、`you do not have access to login.windows.net` というエラーが発生することがあります。 これは既知の問題であり、今後のエージェントのリリースで修正されます。 ファイアウォールが正しく構成されている場合は、オンボードが引き続き成功します。

### <a name="configure-an-existing-azure-arc-enabled-server"></a>既存の Azure Arc 対応サーバーを構成する

プライベート リンク スコープの前にセットアップされた Azure Arc 対応サーバーの場合は、次の手順を実行して、Azure Arc 対応サーバーのプライベート リンク スコープの使用を開始できるようにすることができます。

1. Azure portal で、Azure Arc プライベート リンク スコープ リソースに移動します。

1. 左側のペインで、 **[Azure Arc リソース]** を選択し、 **[追加]** を選択します。

1. プライベート リンク スコープに関連付けるサーバーを一覧から選択し、 **[選択]** を選択して変更を保存します。

    > [!NOTE]
    > プライベート リンク スコープと同じサブスクリプションとリージョンにある Azure Arc 対応サーバーのみが表示されます。

    :::image type="content" source="./media/private-link-security/select-servers-private-link-scope.png" alt-text="Azure Arc リソースの選択" border="true":::

最近関連付けられたサーバーからの接続がプライベート リンク スコープで受け入れられるまでに、最大 15 分かかることがあります。

## <a name="troubleshooting"></a>トラブルシューティング

1. オンプレミスの DNS サーバーを調べて、それが Azure DNS に転送されているか、プライベート リンク ゾーンに適切な A レコードが構成されていることを確認します。 次の参照コマンドでは、Azure 仮想ネットワーク内のプライベート IP アドレスが返される必要があります。 パブリック IP アドレスを解決する場合は、マシンまたはサーバーとネットワークの DNS 構成を再確認します。

    nslookup gbl.his.arc.azure.com  nslookup agentserviceapi.guestconfiguration.azure.com

1. マシンまたはサーバーのオンボードで問題が発生した場合は、Azure Active Directory と Azure Resource Manager のサービス タグをローカルのネットワーク ファイアウォールに追加したことを確認します。 これらのサービスでプライベート エンドポイントが利用可能になるまで、エージェントではインターネット経由でこれらのサービスと通信する必要があります。

## <a name="next-steps"></a>次のステップ

* プライベート エンドポイントの詳細については、「[Azure プライベート エンドポイントとは](../../private-link/private-endpoint-overview.md)」を参照してください。

* Azure プライベート エンドポイント接続の設定で問題が発生している場合は、「[Azure プライベート エンドポイント接続の問題のトラブルシューティング](../../private-link/troubleshoot-private-endpoint-connectivity.md)」を参照してください。

* [Azure Automation](../../automation/how-to/private-link-security.md)、[Azure Monitor](../../azure-monitor/logs/private-link-security.md)、[Azure Key Vault](../../key-vault/general/private-link-service.md)、または [Azure Blob Storage](../../private-link/tutorial-private-endpoint-storage-portal.md) 用の Private Link の構成に関する記事を参照してください。
