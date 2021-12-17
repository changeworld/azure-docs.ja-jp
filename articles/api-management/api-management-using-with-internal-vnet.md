---
title: Azure API Management を使用して内部仮想ネットワークに接続する
description: 内部モードを使用して仮想ネットワークで Azure API Management を設定して構成する方法について説明します。
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/10/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6dc297736403b6124b27f34462ffaee0bfa4cf4a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845781"
---
# <a name="connect-to-a-virtual-network-in-internal-mode-using-azure-api-management"></a>Azure API Management を使用して内部モデルで仮想ネットワークに接続する 
Azure Virtual Network (VNET) では、Azure API Management からさまざまな VPN テクノロジを使用して接続を確立することで、インターネットからはアクセスできない API を管理することができます。 API Management は、[外部](./api-management-using-with-vnet.md)モードまたは内部モードでデプロイすることができます。 VNET 接続のオプション、要件、および考慮事項については、[Azure API Management での仮想ネットワークの使用](virtual-network-concepts.md)に関するページを参照してください。

この記事では、API Management を内部 VNET モードでデプロイする方法について説明します。 このモードで表示できるのは、自分がアクセスを制御している VNET 内にある次のサービス エンドポイントだけです。
* API ゲートウェイ
* 開発者ポータル
* ダイレクト管理
* Git 

> [!NOTE]
> いずれのサービス エンドポイントも、パブリック DNS には登録されません。 VNET の [DNS を構成](#dns-configuration)するまで、サービス エンドポイントにはアクセスできません。

API Management を内部モードで使用するのは、次のようなケースです。

* Azure VPN 接続または Azure ExpressRoute を使用することで、プライベート データセンターでホストされている API へのアクセスを、外部のサード パーティが安全に行うことができるようにします。
* 一般的なゲートウェイを通じてクラウド ベースの API とオンプレミスの API を公開することによって、ハイブリッド クラウドのシナリオを有効にします。
* 単一のゲートウェイ エンドポイントを使用して複数の地理的な場所でホストされている API を管理します。

:::image type="content" source="media/api-management-using-with-internal-vnet/api-management-vnet-internal.png" alt-text="内部 VNET に接続する":::

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>前提条件

一部の前提条件は、API Management インスタンスの[コンピューティング プラットフォーム](compute-infrastructure.md)のバージョン (`stv2`または `stv1`) によって異なります。 

> [!TIP]
> ポータルを使用して、API Management インスタンスのネットワーク構成を作成または更新すると、そのインスタンスは `stv2` コンピューティング プラットフォームでホストされます。

### <a name="stv2"></a>[stv2](#tab/stv2)

+ **API Management インスタンス。** 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。

* API Management インスタンスと同じリージョンおよびサブスクリプション内に存在する **仮想ネットワークとサブネット**。 サブネットには、他の Azure リソースが含まれている場合があります。

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

   > [!NOTE]
   > `stv2` プラットフォーム上の内部仮想ネットワークに API Management サービスをデプロイすると、パブリック IP アドレス リソースを使用して、[Standard SKU](../load-balancer/skus.md) の内部ロード バランサーの背後でホストされます。

### <a name="stv1"></a>[stv1](#tab/stv1)

+ **API Management インスタンス。** 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。

* API Management インスタンスと同じリージョンおよびサブスクリプション内に存在する **仮想ネットワークとサブネット**。

    サブネットは、API Management インスタンス専用である必要があります。 他のリソースが含まれる Resource Manager VNET サブネットに Azure API Management インスタンスをデプロイしようとすると、そのデプロイは失敗します。

   > [!NOTE]
   > `stv1` プラットフォーム上の内部仮想ネットワークに API Management サービスをデプロイすると、[Basic SKU](../load-balancer/skus.md) の内部ロード バランサーの背後でホストされます。

---

## <a name="enable-vnet-connection"></a>VNET 接続の有効化

### <a name="enable-vnet-connectivity-using-the-azure-portal-stv2-platform"></a>Azure portal を使用して VNET 接続を有効にする (`stv2` プラットフォーム)

1. [Azure portal](https://portal.azure.com) に移動し、お使いの API Management インスタンスを検索します。 **API Management サービス** を検索して選択します。
1. お使いの API Management インスタンスを選択します。
1. **[仮想ネットワーク]** を選択します。
1. アクセスの種類として **[内部]** を選択します。
1. API Management サービスがプロビジョニングされている場所 (リージョン) のリストで、次の手順に従います。 
    1. **[場所]** を選択します。
    1. **仮想ネットワーク**、**サブネット**、**IP アドレス** を選択します。 
        * VNET の一覧には、構成しているリージョンで設定された Azure サブスクリプションで使用できる Resource Manager の VNET が設定されます。
1. **[適用]** を選択します。 API Management インスタンスの **[仮想ネットワーク]** ページが、新しい VNET とサブネットの選択によって更新されます。
   :::image type="content" source="media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png" alt-text="Azure portal で内部 VNET を設定する":::
1. API Management インスタンスの残りの場所に対して、VNET 設定の構成を続行します。
1. 上部のナビゲーション バーで、 **[保存]** を選択してから、 **[ネットワーク構成の適用]** を選択します。

    API Management インスタンスを更新するのに 15 分から 45 分かかることがあります。

デプロイが成功すると、 **[概要]** ブレードに、API Management サービスの **プライベート** 仮想 IP アドレスと **パブリック** 仮想 IP アドレスが表示されます。 IP アドレスの詳細については、この記事の「[ルーティング](#routing)」を参照してください。

:::image type="content" source="media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png" alt-text="Azure portal でのパブリックおよびプライベート IP アドレス"::: 

> [!NOTE]
> ゲートウェイ URL はパブリック DNS には登録されないため、Azure portal で利用可能なテスト コンソールは、**内部** VNET にデプロイされたサービスでは機能しません。 **開発者ポータル** で提供されるテスト コンソールを使用してください。

### <a name="enable-connectivity-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して接続を有効にする

#### <a name="api-version-2021-01-01-preview-stv2-platform"></a>API バージョン 2021-01-01-preview (`stv2` プラットフォーム)

* Azure Resource Manager [テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet-publicip)

     [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet-publicip%2Fazuredeploy.json)

#### <a name="api-version-2020-12-01-stv1-platform"></a>API バージョン 2020-12-01 (`stv1` プラットフォーム)

* Azure Resource Manager [テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet)

     [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

### <a name="enable-connectivity-using-azure-powershell-cmdlets-stv1-platform"></a>Azure PowerShell コマンドレットを使用して接続を有効にする (`stv1` プラットフォーム)

VNET で API Management インスタンスを[作成](/powershell/module/az.apimanagement/new-azapimanagement)または[更新](/powershell/module/az.apimanagement/update-azapimanagementregion)します。

## <a name="dns-configuration"></a>DNS の構成

外部 VNET モードでは、DNS が Azure によって管理されます。 内部 VNET モードの場合は、自身で DNS を管理する必要があります。 以下のことが推奨されます。
1. Azure [DNS プライベート ゾーン](../dns/private-dns-overview.md)を構成します。
1. その Azure DNS プライベート ゾーンを API Management サービスのデプロイ先 VNET にリンクします。 

[Azure DNS でのプライベート ゾーンの設定](../dns/private-dns-getstarted-portal.md)方法を確認してください。

> [!NOTE]
> API Management サービスは、その IP アドレス上の要求をリッスンしません。 サービスは、サービス エンドポイントに構成されたホスト名に対する要求のみに応答します。 たとえば、次のエンドポイントが該当します。
> * API ゲートウェイ
> * Azure ポータル
> * 開発者ポータル
> * ダイレクト管理エンドポイント
> * Git

### <a name="access-on-default-host-names"></a>既定のホスト名でのアクセス
API Management サービス (たとえば `contosointernalvnet`) を作成すると、次のサービス エンドポイントが既定で構成されます。

| エンドポイント | エンドポイントの構成 |
| ----- | ----- |
| API ゲートウェイ | `contosointernalvnet.azure-api.net` |
| 開発者ポータル | `contosointernalvnet.portal.azure-api.net` |
| 新しい開発者ポータル | `contosointernalvnet.developer.azure-api.net` |
| ダイレクト管理エンドポイント | `contosointernalvnet.management.azure-api.net` |
| Git | `contosointernalvnet.scm.azure-api.net` |

これらの API Management サービス エンドポイントにアクセスするために、API Management がデプロイされている VNET に接続しているサブネットに仮想マシンを作成できます。 サービスの内部仮想 IP アドレスを 10.1.0.5 と仮定すると、ホスト ファイルを次のようにマッピングできます。 Windows では、このファイルは `%SystemDrive%\drivers\etc\hosts` にあります。 

| 内部仮想 IP アドレス | エンドポイントの構成 |
| ----- | ----- |
| 10.1.0.5 | `contosointernalvnet.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.portal.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.developer.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.management.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.scm.azure-api.net` |

これで、すべてのサービス エンドポイントに、作成した仮想マシンからアクセスできるようになります。


### <a name="access-on-custom-domain-names"></a>カスタム ドメイン名でのアクセス

既定のホスト名で API Management サービスにアクセスしたくない場合: 

1. 次の画像に示すとおり、すべてのサービス エンドポイントの[カスタム ドメイン名](configure-custom-domain.md)を設定します。

    :::image type="content" source="media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png" alt-text="カスタム ドメイン名を設定する":::

2. VNET 内からアクセスできるエンドポイントにアクセスするためのレコードを DNS サーバーに作成します。

## <a name="routing"></a>ルーティング

次の仮想 IP アドレスは、内部仮想ネットワーク内の API Management インスタンス用に構成されています。 詳細については、「[Azure API Management の IP アドレス](api-management-howto-ip-addresses.md)」を参照してください。

| Virtual IP address | 説明 |
| ----- | ----- |
| **プライベート仮想 IP アドレス** | API Management インスタンスのサブネット範囲 (DIP) 内の負荷分散された IP アドレス。このアドレスを介して、API ゲートウェイ、開発者ポータル、管理、および Git エンドポイントにアクセスできます。<br/><br/>このアドレスは、VNET で使用される DNS サーバーに登録します。     |  
| **パブリック仮想 IP アドレス** | ポート 3443 を介した管理エンドポイントへのコントロール プレーン トラフィックに "*のみ*" 使用されます。 [ApiManagement][ServiceTags] サービス タグにロック ダウンすることができます。 | 

負荷分散されたパブリック IP アドレスとプライベート IP アドレスは、Azure portal の **[概要]** ブレードで確認できます。

> [!NOTE]
> API Management インスタンスの VIP アドレスは、次の場合に変更されます。
> * VNET が有効または無効になった。 
> * API Management が **外部** から **内部** の仮想ネットワーク モードに (またはその逆に) 移行された。
> * インスタンスの場所で、[ゾーン冗長](zone-redundancy.md)設定が有効になっているか、更新されているか、無効になっている (Premium SKU のみ)。
>
> その場合、VNET 内の DNS 登録、ルーティング規則、IP 制限リストを更新する必要があります。

### <a name="vip-and-dip-addresses"></a>VIP アドレスと DIP アドレス

DIP アドレスは、サービス内の基になる各仮想マシンに割り当てられ、VNET の "*内側*" にあるリソースへのアクセスに使用されます。 VIP アドレスは、VNET の "*外側*" にあるリソースへのアクセスに使用されます。 IP 制限リストを使用して VNET 内のリソースをセキュリティで保護する場合は、API Management サービスがデプロイされるサブネットの範囲全体に対して、サービスからのアクセスを許可するか制限するように指定する必要があります。

[推奨されるサブネット サイズ](virtual-network-concepts.md#subnet-size)について説明します。

#### <a name="example"></a>例

内部 VNET の Premium レベルに API Management の 1 つの[容量ユニット](api-management-capacity.md)をデプロイすると、3 つの IP アドレスが使用されます。1 つはプライベート VIP 用で、残りは 2 つの VM の各 DIP 用です。 4 つのユニットにスケールアウトすると、サブネットからの追加の DIP にさらに多くの IP が使用されます。  

宛先エンドポイントで、固定された DIP のセットのみが許可リストに含まれている場合は、今後新しいユニットを追加すると接続エラーが発生します。 この理由により、また、サブネットは完全に制御可能であるため、バックエンドでサブネット全体を許可リストに含めることをお勧めします。

## <a name="next-steps"></a>次のステップ

各項目の詳細情報

* [VNET で Azure API Management を設定するときのネットワーク構成][Common network configuration problems]
* [VNET についての FAQ](../virtual-network/virtual-networks-faq.md)
* [DNS でのレコードの作成](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags

