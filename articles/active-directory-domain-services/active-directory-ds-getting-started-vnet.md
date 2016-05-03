<properties
	pageTitle="Azure AD ドメイン サービス: 仮想ネットワークの作成または選択 | Microsoft Azure"
	description="Azure Active Directory ドメイン サービス (プレビュー) の概要"
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
	ms.topic="get-started-article"
	ms.date="04/25/2016"
	ms.author="maheshu"/>

# Azure AD ドメイン サービス *(プレビュー)* - 仮想ネットワークの作成または選択

## Azure Virtual Network を選択するためのガイドライン
Azure AD ドメイン サービスで使用する仮想ネットワークは、次のガイドラインに従って選んでください。

- Azure AD ドメイン サービスでサポートされているリージョンの仮想ネットワークを選択します。Azure AD ドメイン サービスを使用できる Azure リージョンを確認するには、「[リージョン別のサービス](https://azure.microsoft.com/regions/#services/)」のページを参照してください。

- 既存の仮想ネットワークを使用する場合は、リージョン仮想ネットワークであることを確認してください。従来のアフィニティ グループ機構を使った仮想ネットワークは、Azure AD ドメイン サービスでは使用できません。[従来の仮想ネットワークはリージョン仮想ネットワークに移行](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)する必要があります。

- 既存の仮想ネットワークを使用する場合は、仮想ネットワーク用にカスタムの DNS サーバーが構成されていないことを確認します。Azure AD ドメイン サービスは、カスタム/独自の DNS サーバーをサポートしません。

- 既存の仮想ネットワークを使用する場合は、その仮想ネットワークで使用できるドメインと同じ名前のドメインがないことを確認します。たとえば、選択した仮想ネットワークで既に利用可能な "contoso.com" という名前のドメインがあると仮定します。その後、その仮想ネットワークでこれと同じドメイン名 (つまり "contoso.com") で、Azure AD ドメイン サービスの管理対象ドメインを有効にしようとします。Azure AD ドメイン サービスを有効にしようとすると、エラーが発生します。これは、仮想ネットワークのドメイン名で名前が競合しているためです。この場合、Azure AD ドメイン サービスの管理対象ドメインを設定するには、別の名前を使用する必要があります。または、既存のドメインのプロビジョニングを解除してから、Azure AD ドメイン サービスの有効化に進みます。

- Azure AD ドメイン サービスにアクセスする必要のある仮想マシンを現在ホストしている (または今後ホストする予定の) 仮想ネットワークを選択します。サービスを有効にした後は、異なる仮想ネットワークにドメイン サービスを移動できなくなります。

- Azure AD ドメイン サービスは、Azure リソース マネージャーを使用して作成した仮想ネットワークではサポートされていません。Azure リソース マネージャーを使用して作成された仮想ネットワークで Azure AD ドメイン サービスを使用するには、[ARM ベースの仮想ネットワークに従来の仮想ネットワークを接続](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)します。


## タスク 2: Azure 仮想ネットワークを作成する
次に、Azure AD ドメイン サービスが利用できる Azure 仮想ネットワークを作成します。希望する仮想ネットワークが既にある場合は、この手順を省略してかまいません。

> [AZURE.NOTE] Azure AD ドメイン サービス用に作成 (または選択) する Azure Virtual Network が、Azure AD ドメイン サービスによってサポートされている Azure リージョンに属していることを確認してください。Azure AD ドメイン サービスを使用できる Azure リージョンを確認するには、「[リージョン別のサービス](https://azure.microsoft.com/regions/#services/)」のページを参照してください。

仮想ネットワークの名前は書き留めておいてください。この後の構成手順で Azure AD ドメイン サービスを有効にする際、適切な仮想ネットワークを選択する必要があります。

Azure AD ドメイン サービスが利用できる Azure Virtual Network を作成するには、次の構成手順を実行します。

1. **Azure クラシック ポータル** ([https://manage.windowsazure.com](https://manage.windowsazure.com)) に移動します。

2. 左側のウィンドウで、**[ネットワーク]** ノードを選択します。

3. ページ下部の作業ウィンドウの **[新規]** をクリックします。

    ![[Virtual Networks] ノード](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. **[Network Services]** ノードの **[Virtual Network]** を選択します。

5. 仮想ネットワークを作成するには、**[簡易作成]** をクリックします。

    ![Virtual network - quick create](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. 仮想ネットワークの**名前**を指定します。このネットワークの **[アドレス空間]** や **[最大 VM 数]** を構成することもできます。DNS サーバーの設定は差し当たり、[なし] のままでかまいません。この設定は、Azure AD ドメイン サービスを有効にした後で更新されます。

7. **[場所]** ボックスの一覧で、サポート対象の Azure リージョンを確実に選択します。Azure AD ドメイン サービスを使用できる Azure リージョンを確認するには、「[リージョン別のサービス](https://azure.microsoft.com/regions/#services/)」のページを参照してください。これは重要な手順です。Azure AD ドメイン サービスでサポートされていない Azure リージョンの仮想ネットワークを選択した場合、その仮想ネットワークでドメイン サービスを有効にすることができません。

8. **[Virtual Network の作成]** ボタンをクリックして仮想ネットワークを作成します。

    ![Azure AD Domain Services 用の仮想ネットワークを作成します。](./media/active-directory-domain-services-getting-started/create-vnet.png)

<br>

## タスク 3: Azure AD ドメイン サービスを有効にする
次に、[Azure AD ドメイン サービスを有効](active-directory-ds-getting-started-enableaadds.md)にします。

<!---HONumber=AcomDC_0427_2016-->