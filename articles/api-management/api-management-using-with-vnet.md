---
title: Azure API Management を使用して仮想ネットワークに接続する
description: Azure API Management で仮想ネットワークへの接続を設定し、それを介して Web サービスにアクセスする方法について説明します。
services: api-management
author: vladvino
ms.service: api-management
ms.topic: how-to
ms.date: 06/08/2021
ms.author: apimpm
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 9de42ef1aa7471f489a02af6e1931c0df0252b7f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746341"
---
# <a name="connect-to-a-virtual-network-using-azure-api-management"></a>Azure API Management を使用して仮想ネットワークに接続する
Azure Virtual Network (VNET) では、任意の Azure リソースをインターネット以外のルーティング可能なネットワークに配置し、アクセスを制御できます。 その後、さまざまな VPN テクノロジを使用して VNET をオンプレミスのネットワークに接続できます。 Azure VNET の詳細については、最初に [Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページの情報をご覧ください。

Azure API Management は、VNET の内部にデプロイできるため、ネットワーク内でバックエンド サービスにアクセスできます。 開発者ポータルと API ゲートウェイは、インターネットから、または VNET 内でのみアクセスできるように構成できます。 

この記事では、API Management インスタンスの VNET 接続オプション、設定、制限、トラブルシューティングの手順について説明します。 開発者ポータルと API ゲートウェイが VNET 内でのみアクセスできる内部モードに固有の構成については、「[Azure API Management を使用して内部仮想ネットワークに接続する](./api-management-using-with-internal-vnet.md)」を参照してください。

> [!NOTE]
> API インポート ドキュメントの URL は、パブリックにアクセス可能なインターネット アドレスでホストされている必要があります。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>前提条件

+ **有効な Azure サブスクリプション** [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **API Management インスタンス。** 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>VNET 接続の有効化

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Azure ポータルを使用して VNET 接続を有効にする

1. [Azure portal](https://portal.azure.com) に移動し、お使いの API Management インスタンスを検索します。 **API Management サービス** を検索して選択します。

1. お使いの API Management インスタンスを選択します。

1. **[仮想ネットワーク]** を選択します。
1. VNET 内にデプロイされる API Management インスタンスを構成します。

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Azure portal で VNET を選択する。":::

1. 目的のアクセスの種類を選択します。

    * **オフ**: 既定の種類です。 API Management は VNET にデプロイされません。

    * **外部**:API Management のゲートウェイと開発者ポータルには、パブリック インターネットから外部ロード バランサーを使用してアクセスできます。 ゲートウェイでは、VNET 内のリソースにアクセスできます。

        ![パブリック ピアリング][api-management-vnet-public]

    * **内部**: API Management のゲートウェイと開発者ポータルには、VNET 内から内部ロード バランサーを使用してのみアクセスできます。 ゲートウェイでは、VNET 内のリソースにアクセスできます。

        ![プライベート ピアリング][api-management-vnet-private]

1. **[外部]** または **[内部]** を選択した場合、API Management サービスがプロビジョニングされているすべての場所 (リージョン) の一覧が表示されます。 
1. **[場所]** を選択します。
1. **仮想ネットワーク**、**サブネット**、**IP アドレス** を選択します。 
    * VNET の一覧には、構成しているリージョンで設定された Azure サブスクリプションで使用できる Resource Manager の VNET が設定されます。

        :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="ポータルでの VNET の設定。":::

        > [!IMPORTANT]
        > * **API バージョン 2020-12-01 以前を使用して Azure API Management インスタンスを Resource Manager VNET にデプロイする場合:** Azure API Management インスタンスのみが含まれる専用サブネット内にサービスが存在する必要があります。 他のリソースが含まれる Resource Manager VNET サブネットに Azure API Management インスタンスをデプロイしようとすると、そのデプロイは失敗します。
        >
        > * **API バージョン 2021-01-01-preview 以降を使用して、VNET に Azure API Management インスタンスをデプロイする場合:** Resource Manager VNET のみがサポートされていますが、使用されるサブネットに他のリソースが含まれていても構いません。 API Management インスタンス専用のサブネットを使用する必要はありません。

1. **[適用]** を選択します。 API Management インスタンスの **[仮想ネットワーク]** ページが、新しい VNET とサブネットの選択によって更新されます。

1. API Management インスタンスの残りの場所に対して、VNET 設定の構成を続行します。

7. 上部のナビゲーション バーで、 **[保存]** を選択してから、 **[ネットワーク構成の適用]** を選択します。

    API Management インスタンスを更新するのに 15 分から 45 分かかることがあります。

> [!NOTE]
> API バージョン 2020-12-01 以前を使用しているクライアントでは、次の場合に API Management インスタンスの VIP アドレスが変更されます。
> * VNET が有効または無効になった。 
> * API Management が **外部** から **内部** の仮想ネットワークに (またはその逆に) 移動された。

> [!IMPORTANT]
> * **API バージョン 2018-01-01 以前を使用している場合:**    
> VNET から API Management を削除するか、VNET を変更すると、VNET は最大 6 時間ロックされます。 この 6 時間以内に、VNET を削除したり、新しいリソースをデプロイしたりすることはできません。 
>
> * **API バージョン 2019-01-01 以降を使用している場合:**  
> 関連付けられている API Management サービスが削除されると、直ちに VNET が使用可能になります。

### <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>外部 VNET に API Management をデプロイする

VNET の接続は、次の方法を使用して有効にすることもできます。

### <a name="api-version-2021-01-01-preview"></a>API バージョン 2021-01-01-preview

* Azure Resource Manager [テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-external-vnet-publicip)

     [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet-publicip%2Fazuredeploy.json)

### <a name="api-version-2020-12-01"></a>API バージョン 2020-12-01

* Azure Resource Manager [テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-external-vnet)

     [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-external-vnet%2Fazuredeploy.json)

* Azure PowerShell コマンドレット - VNET で API Management インスタンスを[作成](/powershell/module/az.apimanagement/new-azapimanagement)または[更新](/powershell/module/az.apimanagement/update-azapimanagementregion)する

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>仮想ネットワーク内でホストされる Web サービスに接続する
API Management サービスを VNET に接続すると、パブリック サービスの場合と同様に、その内部のバックエンド サービスにアクセスできるようになります。 API を作成または編集するときは、Web サービスのローカル IP アドレスまたはホスト名 (VNET に対して DNS サーバーが構成されている場合) を **[Web サービスの URL]** フィールドに入力します。

![VPN からの API の追加][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>ネットワーク構成に関する一般的な問題
API Management サービスを VNET にデプロイするときに発生する可能性がある構成の誤りに関する一般的な問題は、次のとおりです。

* **カスタム DNS サーバーのセットアップ:**  
    API Management サービスは、複数の Azure サービスに依存します。 カスタム DNS サーバーを使用して VNET で API Management をホストする場合、その DNS サーバーはこれらの Azure サービスのホスト名を解決する必要があります。  
    * カスタム DNS セットアップのガイダンスについては、[Azure 仮想ネットワーク内のリソースの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)に関する記事をご覧ください。  
    * 参考情報については、[ポートの表](#required-ports)とネットワーク要件をご覧ください。

    > [!IMPORTANT]
    > VNET でカスタム DNS サーバーを使用する予定の場合は、API Management サービスをデプロイする **前** にサーバーをセットアップします。 それ以外の場合は、DNS サーバーを変更するたびに [[ネットワーク構成の適用] 操作](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates)を実行して API Management サービスを更新する必要があります。

* **API Management に必要なポート:**  
    API Management がデプロイされるサブネットへの受信および送信トラフィックは、[ネットワーク セキュリティ グループ] [ネットワーク セキュリティ グループ] を使用して制御できます。 以下のポートのいずれかが利用できない場合、API Management は正しく動作しない可能性があり、アクセス不能になる場合があります。 ブロックされるポートは、VNET で API Management を使用した場合の構成の誤りに関するもう 1 つの一般的な問題です。

<a name="required-ports"> </a> API Management サービス インスタンスが VNET でホストされている場合は、次の表のポートが使用されます。

| ソース / ターゲット ポート | Direction          | トランスポート プロトコル |   [サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags) <br> ソース / ターゲット   | 目的 (\*)                                                 | VNET の種類 |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80]、443                  | 受信            | TCP                | INTERNET / VIRTUAL_NETWORK            | API Management へのクライアント通信                      | 外部             |
| * / 3443                     | 受信            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Azure Portal と PowerShell 用の管理エンドポイント         | 外部 / 内部  |
| * / 443                  | 送信           | TCP                | VIRTUAL_NETWORK / Storage             | **Azure Storage への依存関係**                             | 外部 / 内部  |
| * / 443                  | 送信           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) と Azure KeyVault の依存関係                  | 外部 / 内部  |
| * / 1433                     | 送信           | TCP                | VIRTUAL_NETWORK / SQL                 | **Azure SQL エンドポイントへのアクセス**                           | 外部 / 内部  |
| * / 443                     | 送信           | TCP                | VIRTUAL_NETWORK / AzureKeyVault                 | **Azure KeyVault へのアクセス**                           | 外部 / 内部  |
| * / 5671, 5672, 443          | 送信           | TCP                | VIRTUAL_NETWORK / EventHub            | [Event Hub へのログ ポリシー](api-management-howto-log-event-hubs.md)および監視エージェントの依存関係 | 外部 / 内部  |
| * / 445                      | 送信           | TCP                | VIRTUAL_NETWORK / Storage             | [Git](api-management-configuration-repository-git.md) のための Azure ファイル共有への依存関係                      | 外部 / 内部  |
| * / 443、12000                     | 送信           | TCP                | VIRTUAL_NETWORK / AzureCloud            | 正常性と監視の拡張機能         | 外部 / 内部  |
| * / 1886、443                     | 送信           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | [診断ログとメトリック](api-management-howto-use-azure-monitor.md)、[Resource Health](../service-health/resource-health-overview.md)、[Application Insights](api-management-howto-app-insights.md) を公開する                   | 外部 / 内部  |
| * / 25、587、25028                       | 送信           | TCP                | VIRTUAL_NETWORK / INTERNET            | 電子メールを送信するために SMTP リレーに接続する                    | 外部 / 内部  |
| * / 6381 - 6383              | 受信および送信 | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | マシン間の[キャッシュ](api-management-caching-policies.md) ポリシーのために Redis サービスにアクセスする         | 外部 / 内部  |
| * / 4290              | 受信および送信 | UDP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | マシン間の[レート制限](api-management-access-restriction-policies.md#LimitCallRateByKey)ポリシーのために同期カウンターにアクセスする         | 外部 / 内部  |
| * / *                        | 受信            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure インフラストラクチャの Load Balancer                          | 外部 / 内部  |

>[!IMPORTANT]
> "*目的*" 列が太字の項目は、API Management サービスの正常なデプロイを必要とします。 ただし、その他のポートをブロックすると、実行中のサービスを使用し、**そのサービスを監視してコミットされた SLA を提供する** 能力が **低下** します。

+ **TLS 機能:**  
  API Management サービスで TLS/SSL 証明書チェーンの構築と検証を有効にするには、`ocsp.msocsp.com`、`mscrl.microsoft.com`、`crl.microsoft.com` への送信ネットワーク接続が必要です。 API Management にアップロードする任意の証明書に CA ルートへの完全なチェーンが含まれている場合、この依存関係は必要ありません。

+ **DNS アクセス:**  
  DNS サーバーとの通信には、`port 53` での発信アクセスが必要です。 カスタム DNS サーバーが VPN ゲートウェイの相手側にある場合、DNS サーバーは API Management をホストしているサブネットから到達できる必要があります。

+ **メトリックと正常性の監視:**  
  次のドメインで解決される Azure Monitoring エンドポイントへの送信ネットワーク接続は、ネットワーク セキュリティ グループで使用するために、AzureMonitor サービス タグの下に表示されます。

    | Azure 環境 | エンドポイント                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>gcs.prod.monitoring.core.windows.net</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  
+ **リージョン サービス タグ**:Storage、SQL、Event Hubs サービス タグへの送信接続を許可する NSG 規則は、API Management インスタンスを含むリージョンに対応するこれらのタグのリージョン バージョンを使用できます (たとえば、米国西部リージョンの API 管理インスタンスに対する Storage.WestUS など)。 複数リージョンのデプロイでは、各リージョンの NSG は、そのリージョンとプライマリ リージョンのサービス タグへのトラフィックを許可する必要があります。

    > [!IMPORTANT]
    > VNET 内の API Management インスタンスに対して[開発者ポータル](api-management-howto-developer-portal.md)の発行を有効にするには、米国西部リージョンの BLOB ストレージへの送信接続を許可します。 たとえば、NSG ルールで **Storage.WestUS** サービス タグを使用します。 現在、API Management インスタンスに対して開発者ポータルを発行するには、米国西部リージョンの BLOB ストレージへの接続が必要です。

+ **SMTP リレー:**  
  SMTP リレー用の送信ネットワーク接続。`smtpi-co1.msn.com`、`smtpi-ch1.msn.com`、`smtpi-db3.msn.com`、`smtpi-sin.msn.com`、`ies.global.microsoft.com` の各ホストで解決されます。

+ **開発者ポータル CAPTCHA:**  
  開発者ポータルの CAPTCHA 用の発信ネットワーク接続。ホスト `client.hip.live.com` と `partner.hip.live.com` で解決されます。

+ **Azure portal 診断:**  
  VNET 内から API Management 拡張機能を使用しているときに、Azure portal から診断ログのフローを有効にするには、`port 443` での `dc.services.visualstudio.com` への送信アクセスが必要です。 このアクセスは、拡張機能の使用時に発生する可能性がある問題のトラブルシューティングに役立ちます。

+ **Azure Load Balancer:**  
  `Developer` SKU では、背後にデプロイされるコンピューティング ユニットが 1 つしかないため、サービス タグ `AZURE_LOAD_BALANCER` からの受信要求を許可する必要はありません。 ただし、`Premium` のような上位の SKU にスケーリングするときは、Load Balancer からの正常性プローブのエラーでデプロイに失敗するため、[168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) からの受信が重要になります。

+ **Application Insights:**  
  API Management で [Azure Application Insights](api-management-howto-app-insights.md) の監視が有効になっている場合は、VNET から[テレメトリ エンドポイント](../azure-monitor/app/ip-addresses.md#outgoing-ports)への送信接続を許可します。

+ **Express Route またはネットワーク仮想アプライアンスを使用したオンプレミスのファイアウォールへのトラフィックの強制トンネリング:**  
  通常は、API Management の委任されたサブネットからのすべてのトラフィックを、オンプレミスのファイアウォールまたはネットワーク仮想アプライアンスに強制的に流す、独自の既定のルート (0.0.0.0/0) を構成して定義します。 このトラフィック フローでは、Azure API Management を使用した接続は切断されます。これは、発信トラフィックがオンプレミスでブロックされるか、さまざまな Azure エンドポイントで使用されなくなった認識できないアドレス セットに NAT 変換されるためです。 この問題は、いくつかの方法で解決できます。 

  * API Management サービスがデプロイされているサブネット上で、次の[サービス エンドポイント][ServiceEndpoints]を有効にします。
      * Azure SQL
      * Azure Storage
      * Azure EventHub
      * Azure ServiceBus
      * Azure KeyVault 
  
    これらのサービスに対して、API Management の委任されたサブネットからエンドポイントを直接有効にすると、サービス トラフィックの最適なルーティングを提供する Microsoft Azure バックボーン ネットワークを使用できます。 トンネリングが強制された API Management でサービス エンドポイントを使用する場合、上記の Azure サービスのトラフィックは強制的にトンネリングされません。 API Management サービスの他の依存関係トラフィックは強制的にトンネリングされ、失われることはありません。 失われた場合、API Management サービスが適切に機能しなくなります。

  * インターネットから API Management サービスの管理エンドポイントへのすべてのコントロール プレーン トラフィックは、API Management によってホストされている受信 IP の特定のセットを使用してルーティングされます。 トラフィックが強制的にトンネリングされると、応答がこれらの受信送信元 IP に対称的にマップされなくなります。 この制限を克服するには、次のユーザー定義ルート ([UDR][UDRs]) の宛先を "Internet" に設定して、トラフィックを Azure に誘導します。 コントロール プレーン トラフィックの受信 IP のセットは、「[コントロール プレーンの IP アドレス](#control-plane-ips)」に記載されています。

  * 強制的にトンネリングされる API Management サービスの他の依存関係については、ホスト名を解決してエンドポイントに到達します。 これには以下が含まれます。
      - メトリックと正常性の監視
      - Azure portal 診断
      - SMTP リレー
      - 開発者ポータル CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>トラブルシューティング
* **API Management サービスのサブネットへの最初のデプロイが成功しない:** 
  * 同じサブネットに仮想マシンをデプロイします。 
  * 仮想マシンにリモート デスクトップ接続して、Azure サブスクリプション内の次のリソースのいずれかに接続できることを確認します。
    * Azure Storage BLOB
    * Azure SQL データベース
    * Azure Storage Table

  > [!IMPORTANT]
  > 接続を確認したら、サブネットに API Management をデプロイする前に、サブネット内のすべてのリソースを削除してください。

* **ネットワーク接続の状態の確認:**  
  * API Management をサブネットにデプロイした後、ポータルを使用して、Azure Storage などの依存関係へのインスタンスの接続を確認します。 
  * ポータルの左側のメニューで、 **[Deployment and infrastructure]\(デプロイとインフラストラクチャ\)** の下にある **[ネットワーク接続の状態]** を選択します。

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="ポータルでネットワーク接続の状態を確認する":::

  | Assert | 説明 |
  | ----- | ----- |
  | **必須** | API Management に必要な Azure サービスへの接続を確認する場合に選択します。 エラーは、インスタンスが API を管理するためのコア操作を実行できないことを示します |
  | **Optional** | オプションのサービスへの接続を確認する場合に選択します。 エラーは、特定の機能 (SMTP など) が機能しないことのみを示します。 エラーが発生すると、API Management インスタンスを使用と監視を行う機能や、確約された SLA を提供する機能が低下する可能性があります。 |

  接続の問題に対処するには、「[ネットワーク構成に関する一般的な問題](#network-configuration-issues)」を確認し、必要なネットワーク設定を修正します。

* **増分更新:**  
  ネットワークを変更するときは、[NetworkStatus API](/rest/api/apimanagement/2019-12-01/networkstatus) を参照して、API Management サービスで重要なリソースへのアクセスが失われていないことを確認してください。 接続状態は、15 分間隔で更新されます。

* **リソース ナビゲーション リンク:**  
  API バージョン 2020-12-01 以前を使用して Resource Manager VNET サブネットにデプロイすると、API Management では、リソース ナビゲーション リンクを作成することでサブネットが予約されます。 サブネットに別のプロバイダーのリソースが既に含まれている場合、デプロイは **失敗** します。 同様に、API Management サービスを削除するか、別のサブネットに移動すると、リソース ナビゲーション リンクは削除されます。

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a>サブネットのサイズ要件
Azure では、各サブネット内で一部の IP アドレスが予約されており、これらを使用することはできません。 サブネットの最初と最後の IP アドレスは、プロトコルに準拠するために予約されています。 さらに 3 つのアドレスが Azure サービスのために使用されます。 詳細については、「[これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)」をご覧ください。

Azure VNET インフラストラクチャによって使用される IP アドレスに加えて、サブネットの各 API Management インスタンスでは、以下が使用されます。
* Premium SKU のユニットごとに 2 つの IP アドレス、または 
* Developer SKU 用に 1 つの IP アドレス。 

各インスタンスでは、外部ロード バランサー用に追加の IP アドレスが予約されます。 [内部 VNET](./api-management-using-with-internal-vnet.md) にデプロイする場合は、内部ロード バランサー用に追加の IP アドレスが必要です。

前述の計算によると、API Management でデプロイできるサブネットの最小サイズは /29 で、3 つの使用可能な IP アドレスが提供されます。 API Management の追加スケール ユニットごとに、さらに 2 つの IP アドレスが必要になります。

## <a name="routing"></a><a name="routing"> </a>ルーティング
+ 負荷分散されたパブリック IP アドレス (VIP) は、VNET の外部にあるすべてのサービス エンドポイントとリソースへのアクセスを提供するために予約されます。
  + 負荷分散されたパブリック IP アドレスは、Azure portal の **[概要]/[要点]** ブレードで確認できます。
+ サブネット IP 範囲 (DIP) の IP アドレスは、VNET 内のリソースにアクセスするために使用されます。

## <a name="limitations"></a><a name="limitations"> </a>制限事項
* API バージョン 2020-12-01 以前では、API Management インスタンスを含むサブネットに、他の種類の Azure リソースを含めることはできません。
* サブネットと API Management サービスは、同じサブスクリプション内になければなりません。
* API Management インスタンスが含まれるサブネットは、サブスクリプション間で移動できません。
* 内部 VNET モードで構成された複数リージョンの API Management デプロイでは、ユーザーがルーティングを所有し、複数のリージョン間の負荷分散を管理する責任を負います。
* プラットフォームの制限により、別のリージョンのグローバルにピアリングされた VNET 内のリソースと内部モードの API Management サービスの間の接続は機能しません。 詳細については、[ある仮想ネットワーク内のリソースがピアリングされた仮想ネットワーク内の Azure 内部ロード バランサーと通信できない](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)ことに関する記事をご覧ください。

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a>コントロール プレーンの IP アドレス

IP アドレスは **Azure 環境** ごとに分かれています。 受信要求を許可する場合は、**グローバル** とマークされた IP アドレスを、**リージョン** 固有の IP アドレスと共に許可する必要があります。

| **Azure 環境**|   **リージョン**|  **IP アドレス (IP address)**|
|-----------------|-------------------------|---------------|
| Azure Public| 米国中南部 (グローバル)| 104.214.19.224|
| Azure Public| 米国中北部 (グローバル)| 52.162.110.80|
| Azure Public| 米国中西部| 52.253.135.58|
| Azure Public| 韓国中部| 40.82.157.167|
| Azure Public| 英国西部| 51.137.136.0|
| Azure Public| 西日本| 40.81.185.8|
| Azure Public| 米国中北部| 40.81.47.216|
| Azure Public| 英国南部| 51.145.56.125|
| Azure Public| インド西部| 40.81.89.24|
| Azure Public| 米国東部| 52.224.186.99|
| Azure Public| 西ヨーロッパ| 51.145.179.78|
| Azure Public| 東日本| 52.140.238.179|
| Azure Public| フランス中部| 40.66.60.111|
| Azure Public| カナダ東部| 52.139.80.117|
| Azure Public| アラブ首長国連邦北部| 20.46.144.85|
| Azure Public| ブラジル南部| 191.233.24.179|
| Azure Public| ブラジル南東部| 191.232.18.181|
| Azure Public| 東南アジア| 40.90.185.46|
| Azure Public| 南アフリカ北部| 102.133.130.197|
| Azure Public| カナダ中部| 52.139.20.34|
| Azure Public| 韓国南部| 40.80.232.185|
| Azure Public| インド中部| 13.71.49.1|
| Azure Public| 米国西部| 13.64.39.16|
| Azure Public| オーストラリア南東部| 20.40.160.107|
| Azure Public| オーストラリア中部| 20.37.52.67|
| Azure Public| インド南部| 20.44.33.246|
| Azure Public| 米国中部| 13.86.102.66|
| Azure Public| オーストラリア東部| 20.40.125.155|
| Azure Public| 米国西部 2| 51.143.127.203|
| Azure Public| 米国西部 3| 20.150.167.160|
| Azure Public| 米国東部 2 EUAP| 52.253.229.253|
| Azure Public| 米国中部 EUAP| 52.253.159.160|
| Azure Public| 米国中南部| 20.188.77.119|
| Azure Public| 米国東部 2| 20.44.72.3|
| Azure Public| 北ヨーロッパ| 52.142.95.35|
| Azure Public| 東アジア| 52.139.152.27|
| Azure Public| フランス南部| 20.39.80.2|
| Azure Public| スイス西部| 51.107.96.8|
| Azure Public| オーストラリア中部 2| 20.39.99.81|
| Azure Public| アラブ首長国連邦中部| 20.37.81.41|
| Azure Public| スイス北部| 51.107.0.91|
| Azure Public| 南アフリカ西部| 102.133.0.79|
| Azure Public| ドイツ中西部| 51.116.96.0|
| Azure Public| ドイツ北部| 51.116.0.0|
| Azure Public| ノルウェー東部| 51.120.2.185|
| Azure Public| ノルウェー西部| 51.120.130.134|
| Azure China 21Vianet| 中国北部 (グローバル)| 139.217.51.16|
| Azure China 21Vianet| 中国東部 (グローバル)| 139.217.171.176|
| Azure China 21Vianet| 中国北部| 40.125.137.220|
| Azure China 21Vianet| 中国東部| 40.126.120.30|
| Azure China 21Vianet| 中国北部 2| 40.73.41.178|
| Azure China 21Vianet| 中国東部 2| 40.73.104.4|
| Azure Government| USGov バージニア州 (グローバル)| 52.127.42.160|
| Azure Government| USGov テキサス州 (グローバル)| 52.127.34.192|
| Azure Government| USGov バージニア州| 52.227.222.92|
| Azure Government| USGov アイオワ州| 13.73.72.21|
| Azure Government| USGov アリゾナ| 52.244.32.39|
| Azure Government| USGov テキサス| 52.243.154.118|
| Azure Government| USDoD 中部| 52.182.32.132|
| Azure Government| USDoD 東部| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>関連コンテンツ
* [VPN Gateway を使用して Virtual Network をバックエンドに接続する](../vpn-gateway/design.md#s2smulti)
* [異なるデプロイ モデルの Virtual Network を PowerShell を使用して接続する](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Azure API Management で API Inspector を使用して呼び出しをトレースする方法](api-management-howto-api-inspector.md)
* [Virtual Network についてよく寄せられる質問](../virtual-network/virtual-networks-faq.md)
* [サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
