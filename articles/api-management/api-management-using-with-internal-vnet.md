---
title: Azure API Management を使用して内部仮想ネットワークに接続する
titleSuffix: Azure API Management
description: 内部仮想ネットワークで Azure API Management をセットアップして構成する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 06/08/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b0f67862352f2354dbdb5439542229dbee67ae00
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113107489"
---
# <a name="connect-to-an-internal-virtual-network-using-azure-api-management"></a>Azure API Management を使用して内部仮想ネットワークに接続する 
Azure Virtual Network (VNET) では、Azure API Management からさまざまな VPN テクノロジを使用して接続を確立することで、インターネットからはアクセスできない API を管理することができます。 API Management は、[外部](./api-management-using-with-vnet.md)モードまたは内部モードでデプロイすることができます。 この記事では、API Management を内部 VNET モードでデプロイする方法について説明します。

API Management が内部 VNET モードでデプロイされているとき、表示できるのは、自分がアクセスを制御している VNET 内にある次のサービス エンドポイントだけです。
* プロキシ ゲートウェイ
* 開発者ポータル
* ダイレクト管理
* Git 

> [!NOTE]
> いずれのサービス エンドポイントも、パブリック DNS には登録されません。 VNET の [DNS を構成](#apim-dns-configuration)するまで、サービス エンドポイントにはアクセスできません。

API Management を内部モードで使用するのは、次のようなケースです。

* Azure VPN 接続または Azure ExpressRoute を使用することで、プライベート データセンターでホストされている API へのアクセスを、外部のサード パーティが安全に行うことができるようにします。
* 一般的なゲートウェイを通じてクラウド ベースの API とオンプレミスの API を公開することによって、ハイブリッド クラウドのシナリオを有効にします。
* 単一のゲートウェイ エンドポイントを使用して複数の地理的な場所でホストされている API を管理します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>前提条件

+ **有効な Azure サブスクリプション**。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Azure API Management インスタンス (サポートされる SKU: Developer、Premium、Isolated)** 。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

API Management サービスが VNET にデプロイされている場合は、[一連のポート](./api-management-using-with-vnet.md#required-ports)が使用されるため、それらのポートを開放する必要があります。

## <a name="creating-an-api-management-in-an-internal-vnet"></a><a name="enable-vpn"> </a>内部 VNET に API Management を作成する
内部仮想ネットワークでの API Management サービスは、内部ロード バランサー (ILB) の背後でホストされます。 ロード バランサー SKU は、サービスの作成に使用される管理 API によって異なります。 詳細については、「[Azure Load Balancer の SKU](../load-balancer/skus.md)」を参照してください。

| API バージョン | 背後でホスト |
| ----------- | ------------- |
| 2020-12-01 | Basic SKU の内部ロード バランサー |
| 2020-01-01- プレビュー (サブスクリプションのパブリック IP アドレスを使用) | Standard SKU の内部ロード バランサー |

### <a name="enable-a-vnet-connection-using-the-azure-portal"></a>Azure portal を使用して VNET 接続を有効にする

1. [Azure portal](https://portal.azure.com/) で Azure API Management インスタンスに移動します。
1. **[仮想ネットワーク]** を選択します。
1. **[内部]** アクセスの種類を構成します。 詳細な手順については、「[Azure portal を使用して VNET 接続を有効にする](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal)」を参照してください。

    ![内部 VNET 内に Azure API Management をセットアップするためのメニュー][api-management-using-internal-vnet-menu]

4. **[保存]** を選択します。

デプロイが成功すると、 **[概要]** ブレードに、API Management サービスの **プライベート** 仮想 IP アドレスと **パブリック** 仮想 IP アドレスが表示されます。 

| Virtual IP address | 説明 |
| ----- | ----- |
| **プライベート仮想 IP アドレス** | API Management によって委任されたサブネットの範囲内から割り当てられる、負荷分散された IP アドレスです。これを介して、`gateway`、`portal`、`management`、`scm` の各エンドポイントにアクセスできます。 |  
| **パブリック仮想 IP アドレス** | `port 3443` を介した `management` エンドポイントへのコントロール プレーン トラフィックに "*のみ*" 使用されます。 [ApiManagement][ServiceTags] サービス タグにロック ダウンすることができます。 |  

![内部 VNET が構成されている API Management ダッシュボード][api-management-internal-vnet-dashboard]

> [!NOTE]
> ゲートウェイ URL はパブリック DNS には登録されないため、Azure portal で利用可能なテスト コンソールは、**内部** VNET にデプロイされたサービスでは機能しません。 **開発者ポータル** で提供されるテスト コンソールを使用してください。

### <a name="deploy-api-management-into-vnet"></a><a name="deploy-apim-internal-vnet"> </a>API Management を VNET にデプロイする

VNET の接続は、次の方法を使用して有効にすることもできます。


### <a name="api-version-2020-12-01"></a>API バージョン 2020-12-01

* Azure Resource Manager [テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet)

     [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

* Azure PowerShell コマンドレット - VNET で API Management インスタンスを[作成](/powershell/module/az.apimanagement/new-azapimanagement)または[更新](/powershell/module/az.apimanagement/update-azapimanagementregion)する

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS の構成
外部 VNET モードでは、DNS が Azure によって管理されます。 内部 VNET モードの場合は、自身で DNS を管理する必要があります。 以下のことが推奨されます。
1. Azure DNS プライベート ゾーンを構成する。
1. その Azure DNS プライベート ゾーンを API Management サービスのデプロイ先 VNET にリンクする。 

[Azure DNS でのプライベート ゾーンの設定](../dns/private-dns-getstarted-portal.md)方法を確認してください。

> [!NOTE]
> API Management サービスは、IP アドレスから送信される要求をリッスンしません。 サービスは、サービス エンドポイントに構成されたホスト名に対する要求のみに応答します。 たとえば、次のエンドポイントが該当します。
> * Gateway
> * Azure ポータル
> * 開発者ポータル
> * ダイレクト管理エンドポイント
> * Git

### <a name="access-on-default-host-names"></a>既定のホスト名でのアクセス
API Management サービス (たとえば `contosointernalvnet`) を作成すると、次のサービス エンドポイントが既定で構成されます。

| エンドポイント | エンドポイントの構成 |
| ----- | ----- |
| ゲートウェイまたはプロキシ | `contosointernalvnet.azure-api.net` |
| 開発者ポータル | `contosointernalvnet.portal.azure-api.net` |
| 新しい開発者ポータル | `contosointernalvnet.developer.azure-api.net` |
| ダイレクト管理エンドポイント | `contosointernalvnet.management.azure-api.net` |
| Git | `contosointernalvnet.scm.azure-api.net` |

これらの API Management サービス エンドポイントにアクセスするために、API Management がデプロイされている VNET に接続しているサブネットに仮想マシンを作成できます。 サービスの内部仮想 IP アドレスを 10.1.0.5 と仮定すると、ホスト ファイル (`%SystemDrive%\drivers\etc\hosts`) を次のようにマッピングできます。

| 内部仮想 IP アドレス | エンドポイントの構成 |
| ----- | ----- |
| 10.1.0.5 | `contosointernalvnet.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.portal.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.developer.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.management.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.scm.azure-api.net` |

これで、すべてのサービス エンドポイントに、作成した仮想マシンからアクセスできるようになります。
また、VNET 内でカスタム DNS サーバーを使用している場合は、DNS A レコードを作成して、VNET 内のどこからでもこれらのエンドポイントにアクセスできます。

### <a name="access-on-custom-domain-names"></a>カスタム ドメイン名でのアクセス

既定のホスト名で API Management サービスにアクセスしたくない場合: 

1. 次の画像に示すとおり、すべてのサービス エンドポイントのカスタム ドメイン名を設定します。

   ![API Management のカスタム ドメインの設定][api-management-custom-domain-name]

2. VNET 内からアクセスできるエンドポイントにアクセスするためのレコードを DNS サーバーに作成します。

## <a name="routing"></a><a name="routing"> </a>ルーティング

* サブネット範囲から割り当てられる負荷分散された "*プライベート*" 仮想 IP アドレス (DIP) は、VNET 内から API Management サービス エンドポイントにアクセスする用途に予約されます。 
    * Azure portal には、このプライベート IP アドレスが、サービスの [概要] ブレードに表示されます。 
    * このアドレスは、VNET で使用される DNS サーバーに登録します。
* 負荷分散された "*パブリック*" IP アドレス (VIP) は、管理サービス エンドポイントへのアクセスを `port 3443` 経由で提供するための予約も行われます。 
    * Azure portal には、このパブリック IP アドレスが、サービスの [概要] ブレードに表示されます。 
    * "*パブリック*" IP アドレスは、`port 3443` を介した `management` エンドポイントへのコントロール プレーン トラフィックにのみ使用します。 
    * この IP アドレスは、[ApiManagement][ServiceTags] サービス タグにロック ダウンすることができます。
* DIP アドレスは、サービス内の各仮想マシンに割り当てられ、VNET の "*内側*" にあるリソースへのアクセスに使用されます。 VIP アドレスは、VNET の "*外側*" にあるリソースへのアクセスに使用されます。 IP 制限リストを使用して VNET 内のリソースをセキュリティで保護する場合は、API Management サービスがデプロイされるサブネットの範囲全体に対して、サービスからのアクセスを許可するか制限するように指定する必要があります。
* 負荷分散されたパブリック IP アドレスとプライベート IP アドレスは、Azure portal の [概要] ブレードで確認できます。
* VNET 内のサービスを削除または追加した場合、パブリック アクセス用とプライベート アクセス用に割り当てられた IP アドレスが変化する可能性があります。 その場合、VNET 内の DNS 登録、ルーティング規則、IP 制限リストを更新する必要があります。

## <a name="related-content"></a><a name="related-content"> </a>関連コンテンツ
詳細については、以下の記事をお読みください。
* [VNET 内での Azure API Management の設定時に発生するネットワーク構成に関する一般的な問題][Common network configuration problems]
* [VNET についての FAQ](../virtual-network/virtual-networks-faq.md)
* [DNS でのレコードの作成](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
