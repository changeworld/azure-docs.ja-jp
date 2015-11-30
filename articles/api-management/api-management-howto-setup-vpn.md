<properties
	pageTitle="Azure API Management で VPN 接続を設定する方法"
	description="Azure API Management で VPN 接続を設定して Web サービスにアクセスする方法について説明します。"
	services="api-management"
	documentationCenter=""
	authors="antonba"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/16/2015"
	ms.author="antonba"/>

# Azure API Management で VPN 接続を設定する方法

API Management の VPN のサポートにより、API Management ゲートウェイを Azure Virtual Network に接続することができます。これにより、API Management のお客様は、オンプレミスのバックエンド Web サービスまたはパブリック インターネットからアクセスできないバックエンド Web サービスに安全に接続できます。

## <a name="enable-vpn"> </a>VPN 接続を有効にする

>VPN 接続は、**Premium** レベルでのみ利用できます。Premium レベルに切り替えるには、[管理ポータル][]で API Management サービスを開き、**[スケール]** タブを開きます。 **[全般]** セクションで、Premium レベルを選択し、[保存] をクリックします。

VPN 接続を有効にするには、[管理ポータル][]で API Management サービスを開き、**[構成]** タブに切り替えます。

[VPN] セクションで、**[VPN 接続]** を **[オン]** に切り替えます。

![API Management インスタンスの [構成] タブ][api-management-setup-vpn-configure]

API Management サービスがプロビジョニングされているすべてのリージョンの一覧が表示されます。

すべてのリージョンについて、VPN とサブネットを選択します。VPN の一覧は、構成しているリージョンで設定された Azure サブスクリプションで使用できる仮想ネットワークに基づいて設定されます。

![VPN の選択][api-management-setup-vpn-select]

ページの下部にある **[保存]** をクリックします。更新中は、Microsoft Azure 管理ポータルで API Management サービスに対して他の操作を実行できません。サービス ゲートウェイは使用可能な状態に保持されるため、実行時の呼び出しは影響を受けません。

ゲートウェイの VIP アドレスは VPN を有効または無効にするたびに変化することに注意してください。

## <a name="connect-vpn"> </a>VPN の背後にある Web サービスへの接続

API Management サービスが VPN に接続された後で仮想ネットワーク内の Web サービスにアクセスする方法は、パブリック サービスにアクセスする方法と同じです。単に、新しい API を作成するときや既存の API を編集するときに Web サービスのローカル アドレスまたはホスト名 (Azure Virtual Network に対して DNS サーバーが構成されている場合) を **[Web サービスの URL]** ボックスに入力するだけです。

![VPN からの API の追加][api-management-setup-vpn-add-api]


## <a name="related-content"></a>関連コンテンツ


 * [チュートリアル: サイト間クロスプレミス接続用の仮想ネットワークの作成][]
 * [Azure API Management で API Inspector を使用して呼び出しをトレースする方法][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[管理ポータル]: https://manage.windowsazure.com/

[チュートリアル: サイト間クロスプレミス接続用の仮想ネットワークの作成]: ../virtual-networks-create-site-to-site-cross-premises-connectivity
[Azure API Management で API Inspector を使用して呼び出しをトレースする方法]: api-management-howto-api-inspector.md
 

<!---HONumber=Nov15_HO4-->