<properties
	pageTitle="Azure AD Domain Services: ネットワーク ガイドライン | Microsoft Azure"
	description="Azure Active Directory Domain Services のネットワークに関する考慮事項"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="maheshu"/>

# Azure AD Domain Services のネットワークに関する考慮事項

## Azure 仮想ネットワークを選択する方法
次のガイドラインは、Azure AD Domain Services で使用する仮想ネットワークを選択する際に役立ちます。

### Azure 仮想ネットワークの種類

- クラシック Azure 仮想ネットワークで Azure AD Domain Services を有効にすることができます。

- Azure AD Domain Services は、**Azure Resource Manager を使用して作成された仮想ネットワークでは有効にできません**。

- Resource Manager ベースの仮想ネットワークを、Azure AD Domain Services が有効になっているクラシック仮想ネットワークに接続できます。その後、Resource Manager ベースの仮想ネットワークでは Azure AD Domain Services を使用できます。

- **リージョン仮想ネットワーク**: 既存の仮想ネットワークを使用する予定がある場合は、リージョン仮想ネットワークであることを確認してください。

    - 従来のアフィニティ グループ機構を使った仮想ネットワークは、Azure AD ドメイン サービスでは使用できません。

	- Azure AD Domain Services を使用するには、[従来の仮想ネットワークをリージョン仮想ネットワークに移行してください](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)。


### 仮想ネットワークの Azure リージョン

- Azure AD Domain Services の管理対象ドメインは、サービスを有効にすることを選択した仮想ネットワークと同じ Azure リージョンにデプロイされます。

- Azure AD Domain Services でサポートされている Azure リージョンの仮想ネットワークを選択します。

- Azure AD Domain Services を使用できる Azure リージョンを確認するには、[リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services/)に関するページを参照してください。


### 仮想ネットワークの要件

- **Azure ワークロードへの近さ**: Azure AD Domain Services にアクセスする必要のある仮想マシンを現在ホストしている (または今後ホストする予定の) 仮想ネットワークを選択します。

- **カスタムまたは持ち込みの DNS サーバー**: 仮想ネットワーク用にカスタムの DNS サーバーが構成されていないことを確認します。

- **同じドメイン名を持つ既存のドメイン**: 仮想ネットワークで使用できるドメインと同じ名前の既存のドメインがないことを確認します。たとえば、選択した仮想ネットワークで既に利用可能な "contoso.com" という名前のドメインがあると仮定します。その後、その仮想ネットワークでこれと同じドメイン名 (つまり "contoso.com") で、Azure AD Domain Services の管理対象ドメインを有効にしようとします。Azure AD Domain Services を有効にしようとすると、エラーが発生します。このエラーの原因は、仮想ネットワークのドメイン名で名前が競合していることにあります。この場合、Azure AD ドメイン サービスの管理対象ドメインを設定するには、別の名前を使用する必要があります。または、既存のドメインのプロビジョニングを解除してから、Azure AD ドメイン サービスの有効化に進みます。

> [AZURE.WARNING] Domain Services は、有効にした後、別の仮想ネットワークに移動できません。


## ネットワーク セキュリティ グループとサブネットの設計
[ネットワーク セキュリティ グループ (NSG)](../virtual-network/virtual-networks-nsg.md) には、仮想ネットワークの VM インスタンスに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) 規則が含まれています。NSG は、サブネットまたはそのサブネット内の個々の VM インスタンスと関連付けることができます。NSG がサブネットに関連付けられている場合、ACL 規則はそのサブネット内のすべての VM インスタンスに適用されます。また、NSG を直接 VM に関連付けることにより、その個々の VM に対するトラフィックをさらに制限できます。

> [AZURE.NOTE] **Azure AD Domain Services は、Azure 仮想ネットワーク内の別の専用サブネットにデプロイします。この専用サブネットに NSG を適用しないでください。仮想ネットワークのゲートウェイ サブネットでは Azure AD Domain Services を有効にしないでください。**

![Recommended subnet design](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

> [AZURE.WARNING] Azure AD Domain Services が有効になっているサブネットに NSG を関連付けると、Microsoft によるドメインのサービス提供および管理機能が中断される可能性があります。さらに、Azure AD テナントと管理対象ドメインの間の同期が中断されます。**SLA は、Azure AD Domain Services が有効になっているサブネットに NSG が適用されているデプロイには適用されません。**


## ネットワーク接続
Azure AD Domain Services の管理対象ドメインは、Azure の 1 つのクラシック仮想ネットワーク内のみで有効にすることができます。Azure Resource Manager を使用して作成された仮想ネットワークはサポートされていません。

### Azure ネットワークを接続するためのシナリオ
次のいずれかのデプロイ シナリオで Azure 仮想ネットワークを接続して管理対象ドメインを使用します。

#### 複数の Azure クラシック仮想ネットワークで管理対象ドメインを使用する
Azure AD Domain Services を有効にした Azure クラシック仮想ネットワークには他の Azure クラシック仮想ネットワークを接続できます。この接続により、ワークロードが他の仮想ネットワークにデプロイされている管理対象ドメインを使用できます。

![Classic virtual network connectivity](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### Resource Manager ベースの仮想ネットワークで管理対象ドメインを使用する
Resource Manager ベースの仮想ネットワークを、Azure AD Domain Services を有効にした Azure クラシック仮想ネットワークに接続できます。この接続により、ワークロードが Resource Manager ベースの仮想ネットワークにデプロイされている管理対象ドメインを使用できます。

![Resource Manager to classic virtual network connectivity](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)


### ネットワーク接続オプション

- **サイト間 VPN 接続を使用した VNet 間接続**: 仮想ネットワーク間 (VNet 間) の接続は、仮想ネットワークをオンプレミスのサイトの場所に接続することと似ています。どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。

	![Virtual network connectivity using VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [関連情報 - VPN ゲートウェイを使用した仮想ネットワークの接続](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **仮想ネットワーク ピアリングを使用した VNet 間接続**: 仮想ネットワーク ピアリングとは、同じリージョンに存在する 2 つの仮想ネットワークを Azure のバックボーン ネットワークを介して接続する機構です。ピアリングされた 2 つの仮想ネットワークは、あらゆる接続において、見かけ上 1 つのネットワークとして機能します。これらの仮想ネットワークはあくまで個別のリソースとして管理されますが、そこに存在する仮想マシンは互いに、プライベート IP アドレスを使用して直接通信を行うことができます。

    ![Virtual network connectivity using peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

	[関連情報 - 仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)



<br>

## 関連コンテンツ

- [Azure 仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)

- [クラシック デプロイ モデルで VNet 対 VNet 接続を構成する](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Azure ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)

<!---HONumber=AcomDC_0921_2016-->