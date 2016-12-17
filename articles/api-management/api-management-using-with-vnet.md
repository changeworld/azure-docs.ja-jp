---
title: "Azure API Management で仮想ネットワークを使用する方法"
description: "Azure API Management で仮想ネットワークへの接続を設定して Web サービスにアクセスする方法について説明します。"
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 64b58f7b-ca22-47dc-89c0-f6bb0af27a48
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: antonba
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7dfbf0e460dc8de13a4a71c1f925c93f6238df5d


---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Azure API Management で仮想ネットワークを使用する方法
Azure Virtual Network (VNET) を使用すると、任意の Azure リソースをインターネット以外のルーティング可能なネットワークに配置し、アクセスを制御できます。 これらのネットワークは、さまざまな VPN テクノロジを使用して、オンプレミスのネットワークに接続できます。 Azure Virtual Network の詳細については、まず [Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページに記載されている情報をご覧ください。

Azure API Management を仮想ネットワーク (VNET) に接続すると、ネットワーク内のバックエンド サービスにアクセスできるとともに、ネットワーク内で開発者ポータルと API ゲートウェイにアクセスできます。

## <a name="enable-vpn"> </a>VNET 接続の有効化
> VNET 接続は、**Premium** レベルと **Developer** レベルでのみ利用できます。 レベルを切り替えるには、Azure Portal で API Management サービスを開き、**[スケールと料金]** タブを開きます。 **[価格レベル]** セクションで、Premium レベルを選択し、[保存] をクリックします。
> 
> 

VNET 接続を有効にするには、Azure Portal で API Management サービスを開き、**[仮想ネットワーク]** タブに切り替えます。

![API Management の [仮想ネットワーク] メニュー][api-management-using-vnet-menu]

目的のアクセスの種類を選択します。

* **外部**: API Management のゲートウェイと開発者ポータルには、パブリック インターネットから外部ロード バランサーを使用してアクセスできます。 ゲートウェイは、仮想ネットワーク内のリソースにアクセスできます。

![パブリック ピアリング][api-management-vnet-public]

* **内部**: API Management のゲートウェイと開発者ポータルには、仮想ネットワークから内部ロード バランサーを使用してアクセスできます。 ゲートウェイは、仮想ネットワーク内のリソースにアクセスできます。

![プライベート ピアリング][api-management-vnet-private]

API Management サービスがプロビジョニングされているすべてのリージョンの一覧が表示されます。 すべてのリージョンについて、VNET とサブネットを選択します。 この一覧には、構成しているリージョンで設定された Azure サブスクリプションで使用できる従来型および ARM 仮想ネットワークが含まれます。

![VPN の選択][api-management-setup-vpn-select]

画面の上部にある **[保存]** をクリックします。 

> 更新中は、API Management サービスに対して管理操作を実行できません。 API Management のゲートウェイと開発者ポータルは引き続き使用可能です。
> API Management インスタンスの VIP アドレスは VNET を有効または無効にするたびに変更される可能性があることに注意してください。
> 
> 

## <a name="enable-vnet-powershell"> </a>PowerShell コマンドレットを使用して VNET 接続を有効にする
PowerShell コマンドレット [Set-AzureRmApiManagementVirtualNetworks](https://msdn.microsoft.com/library/mt619277.aspx) を使用して VNET 接続を有効にすることもできます。

## <a name="connect-vnet"> </a>仮想ネットワーク内でホストされる Web サービスに接続する
API Management サービスが VNET に接続された後で VNET 内のバックエンド サービスにアクセスする方法は、パブリック サービスにアクセスする方法と同じです。 単に、新しい API を作成するときや既存の API を編集するときに Web サービスのローカル IP アドレスまたはホスト名 (VNET に対して DNS サーバーが構成されている場合) を **[Web サービスの URL]** ボックスに入力するだけです。

![VPN からの API の追加][api-management-setup-vpn-add-api]

## <a name="custom-dns"> </a>カスタム DNS サーバーのセットアップ
API Management は、さまざまな Azure サービスに依存しています。 カスタム DNS サーバーを使用して VNET で API Management をホストする場合、その DNS サーバーはこれらの Azure サービスのホスト名を解決できる必要があります。 カスタム DNS のセットアップについては、 [こちらの](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) ガイダンスに従ってください。 下にある図とポートの表を参照してください。

## <a name="ports-required"> </a>API Management に必要なポート
> これらのポートのいずれかが利用できない場合、API Management は正しく動作しない可能性があり、アクセス不能になる場合があります。 VNET で API Management を使用する場合に、不正な構成に関する最も一般的な問題は、これらの 1 つまたは複数のポートがブロックされていることです。
> 
> 

API Management サービス インスタンスが VNET でホストされている場合は、次の表のポートが使用されます。

| ポート | 方向 | トランスポート プロトコル | 目的 | ソース / ターゲット | アクセスの種類 |
| --- | --- | --- | --- | --- | --- |
| 80、443 |受信 |TCP |API Management へのクライアント通信 |INTERNET / VIRTUAL_NETWORK |外部 |
| 3443 |受信 |TCP |管理エンドポイント |INTERNET / VIRTUAL_NETWORK |外部 / 内部 |
| 80、443 |送信 |TCP |Azure Storage および Azure Service Bus への依存関係 |VIRTUAL_NETWORK / INTERNET |外部 / 内部 |
| 1433 |送信 |TCP |Azure SQL への依存関係 |VIRTUAL_NETWORK / INTERNET |外部 / 内部 |
| 9350、9351、9352、9353、9354 |送信 |TCP |Service Bus への依存関係 |VIRTUAL_NETWORK / INTERNET |外部 / 内部 |
| 5671 |送信 |AMQP |Event Hub へのログ記録ポリシーのための依存関係 |VIRTUAL_NETWORK / INTERNET |外部 / 内部 |
| 6381、6382、6383 |受信/送信 |UDP |Redis Cache への依存関係 |VIRTUAL_NETWORK / VIRTUAL_NETWORK |外部 / 内部 |
| 445 |送信 |TCP |GIT のための Azure ファイル共有への依存関係 |VIRTUAL_NETWORK / INTERNET |外部 / 内部 |

## <a name="limitations"> </a>制限事項
* API Management インスタンスが含まれるサブネットには、その他の Azure リソースの種類を含めることはできません。
* サブネットと API Management サービスは、同じサブスクリプション内になければなりません。
* API Management インスタンスが含まれるサブネットは、サブスクリプション間で移動できません。
* 内部仮想ネットワークを使用する場合、[RFC 1918](https://tools.ietf.org/html/rfc1918) に記載されている範囲の IP アドレスのみが使用可能であり、パブリック IP アドレスを提供することはできません。
* 内部仮想ネットワークが構成された複数リージョンの API Management デプロイでは、DNS を所有するユーザーが分散負荷の管理を担当します。

## <a name="related-content"> </a>関連コンテンツ
* [Azure Portal でサイト間 VPN 接続を使用して仮想ネットワークを作成する][Azure Portal でサイト間 VPN 接続を使用して仮想ネットワークを作成する]
* [Azure API Management で API Inspector を使用して呼び出しをトレースする方法][Azure API Management で API Inspector を使用して呼び出しをトレースする方法]

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[VPN 接続を有効にする]: #enable-vpn
[VPN の背後にある Web サービスへの接続]: #connect-vpn
[関連コンテンツ]: #related-content


[Azure Portal でサイト間 VPN 接続を使用して仮想ネットワークを作成する]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[Azure API Management で API Inspector を使用して呼び出しをトレースする方法]: api-management-howto-api-inspector.md



<!--HONumber=Nov16_HO3-->


