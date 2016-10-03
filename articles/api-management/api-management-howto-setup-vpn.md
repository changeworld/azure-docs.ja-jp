<properties
	pageTitle="Azure API Management で VPN 接続を設定する方法"
	description="Azure API Management で VPN 接続を設定して Web サービスにアクセスする方法について説明します。"
	services="api-management"
	documentationCenter=""
	authors="antonba"
	manager="erikre"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="antonba"/>

# Azure API Management で VPN 接続を設定する方法

API Management の VPN のサポートにより、API Management ゲートウェイを Azure Virtual Network (従来型) に接続することができます。これにより、API Management のお客様は、オンプレミスのバックエンド Web サービスまたはパブリック インターネットからアクセスできないバックエンド Web サービスに安全に接続できます。

>[AZURE.NOTE] Azure API Management は、従来型 VNET と連携します。従来型 VNET を作成する方法については、「[」Azure ポータルを使用した仮想ネットワーク (従来型) の作成](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)」を参照してください。従来の VNET を ARM VNETS に接続する方法の詳細については、「[従来の Vnet を新しい Vnet に接続する](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)」を参照してください。

## <a name="enable-vpn"> </a>VPN 接続を有効にする

>VPN 接続は、**Premium** レベルと **Developer** レベルでのみ利用できます。Premium レベルに切り替えるには、[Azure クラシック ポータル][]で API Management サービスを開き、**[スケール]** タブを開きます。**[全般]** セクションで、Premium レベルを選択し、[保存] をクリックします。

VPN 接続を有効にするには、[Azure クラシック ポータル][]で API Management サービスを開き、**[構成]** タブに切り替えます。

[VPN] セクションで、**[VPN 接続]** を **[オン]** に切り替えます。

![API Management インスタンスの [構成] タブ][api-management-setup-vpn-configure]

API Management サービスがプロビジョニングされているすべてのリージョンの一覧が表示されます。

すべてのリージョンについて、VPN とサブネットを選択します。VPN の一覧は、構成しているリージョンで設定された Azure サブスクリプションで使用できる仮想ネットワークに基づいて設定されます。

![VPN の選択][api-management-setup-vpn-select]

ページの下部にある **[保存]** をクリックします。更新中は、Azure クラシック ポータルで API Management サービスに対して他の操作を実行できません。サービス ゲートウェイは使用可能な状態に保持されるため、実行時の呼び出しは影響を受けません。

ゲートウェイの VIP アドレスは VPN を有効または無効にするたびに変化することに注意してください。

## <a name="connect-vpn"> </a>VPN の背後にある Web サービスへの接続

API Management サービスが VPN に接続された後で仮想ネットワーク内の Web サービスにアクセスする方法は、パブリック サービスにアクセスする方法と同じです。単に、新しい API を作成するときや既存の API を編集するときに Web サービスのローカル アドレスまたはホスト名 (Azure Virtual Network に対して DNS サーバーが構成されている場合) を **[Web サービスの URL]** ボックスに入力するだけです。

![VPN からの API の追加][api-management-setup-vpn-add-api]

## API Management VPN のサポートに必要なポート

API Management サービス インスタンスが VNET でホストされている場合は、次の表のポートが使用されます。これらのポートがブロックされている場合は、サービスが正しく機能しない可能性があります。VNET で API Management を使用する場合に、不正な構成に関する最も一般的な問題は、これらの 1 つまたは複数のポートがブロックされていることです。

| ポート | 方向 | トランスポート プロトコル | 目的 | ソース / ターゲット |
|------------------------------|------------------|--------------------|------------------------------------------------------------------|-----------------------------------|
| 80、443 | 受信 | TCP | API Management へのクライアント通信 | INTERNET / VIRTUAL\_NETWORK |
| 80,443 | 送信 | TCP | Azure Storage および Azure Service Bus への API Management の依存 | VIRTUAL\_NETWORK / INTERNET |
| 1433 | 送信 | TCP | SQL への API Management の依存 | VIRTUAL\_NETWORK / INTERNET |
| 9350、9351、9352、9353、9354 | 送信 | TCP | Service Bus への API Management の依存 | VIRTUAL\_NETWORK / INTERNET |
| 5671 | 送信 | AMQP | Event Hub へのログ記録ポリシーのための API Management の依存 | VIRTUAL\_NETWORK / INTERNET |
| 6381、6382、6383 | 受信/送信 | UDP | Redis Cache への API Management の依存 | VIRTUAL\_NETWORK / VIRTUAL\_NETWORK |
| 445 | 送信 | TCP | GIT のための Azure ファイル共有への API Management の依存 | VIRTUAL\_NETWORK / INTERNET |

## <a name="custom-dns"> </a>カスタム DNS サーバーのセットアップ

API Management は、さまざまな Azure サービスに依存しています。カスタム DNS サーバーが使用される VNET で API Management サービス インスタンスをホストする場合、このサービス インスタンスは、これらの Azure サービスのホスト名を解決できる必要があります。カスタム DNS のセットアップについては、[こちらの](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)ガイダンスに従ってください。

## <a name="related-content"></a>関連コンテンツ


* [Azure クラシック ポータルでサイト間 VPN 接続を使用して仮想ネットワークを作成する][]
* [Azure API Management で API Inspector を使用して呼び出しをトレースする方法][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Azure クラシック ポータル]: https://manage.windowsazure.com/

[Azure クラシック ポータルでサイト間 VPN 接続を使用して仮想ネットワークを作成する]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[Azure API Management で API Inspector を使用して呼び出しをトレースする方法]: api-management-howto-api-inspector.md

<!---HONumber=AcomDC_0921_2016-->