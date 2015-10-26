<properties 
   pageTitle="プレビュー ポータルを使用して ARM モードで静的プライベート IP を設定する方法 | Microsoft Azure"
   description="プライベート IP (DIP) とそれらをプレビュー ポータルを使用して ARM モードで管理する方法を理解します。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/08/2015"
   ms.author="telmos" />

# プレビュー ポータルでの静的プライベート IP アドレスの設定方法

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]この記事では、リソース マネージャーのデプロイメント モデルについて説明します。[クラシック デプロイメント モデルで静的プライベート IP アドレスを管理する](virtual-networks-static-private-ip-classic-pportal.md)こともできます。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

次のサンプルの手順では、単純な環境が既に作成されていると想定します。このドキュメントに表示されている手順を実行する場合は、まず、[vnet の作成](virtual-networks-create-vnet-arm-pportal.md)に関する記事に示されているテスト環境を構築します。

## 静的プライベート IP アドレスをテストするために VM を作成する方法

プレビュー ポータルを使用して、リソース マネージャーのデプロイメント モードで VM を作成する際に、静的プライベート IP アドレスを設定することはできません。まず、VM を作成してから、そのプライベート IP が静的になるように設定する必要があります。

*TestVNet* という名前の VNet の *FrontEnd* サブネットで *DNS01* という名前の VM を作成するには、以下の手順に従います。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. 次の図に示すように、**[新規]**、**[Compute]**、**[Windows Server 2012 R2 Datacenter]** の順にクリックし、**[デプロイ モデルの選択**] の一覧に既に **[リソース マネージャー]** と表示されているのを確認してから [**作成**] をクリックします。

	![プレビュー ポータルでの VM の作成](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. 次の図に示すように、**[基本]** ブレードで、作成する VM の名前 (ここでは *DNS01*)、ローカル管理者のアカウント、およびパスワードを入力します。

	![[基本] ブレード](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. 選択されている **[場所]** が *[米国中央部]* であることを確認してから、**[リソース グループ]** の下にある **[既存の選択]** をクリックし、**[リソース グループ]** を再度クリックして、*[TestRG]*、**[OK]** の順にクリックします。

	![[基本] ブレード](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. **[サイズの選択]** ブレードで、**[A1 標準]** を選択してから **[選択]** をクリックします。

	![[サイズの選択] ブレード](./media/virtual-networks-static-ip-arm-pportal/figure04.png)

6. **[設定]** ブレードで、次のプロパティが以下の値に設定されていることを確認してから **[OK]** をクリックします。

	- **ストレージ アカウント**: *vnetstorage* - **ネットワーク**: *TestVNet* - **サブネット**: *FrontEnd*

	![[サイズの選択] ブレード](./media/virtual-networks-static-ip-arm-pportal/figure05.png)

7. **[概要]** ブレードで、**[OK]** をクリックします。ダッシュ ボードに以下のタイルが表示されることを確認します。

	![プレビュー ポータルでの VM の作成](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## VM 用の静的プライベート IP アドレス情報を取得する方法

上記の手順で作成された VM の静的プライベート IP アドレス情報を表示するには、次の手順を実行します。

1. Azure プレビュー ポータルで、**[すべて参照]**、**[仮想マシン]**、**[DNS01]**、**[すべての設定]**、**[ネットワーク インターフェイス]** の順にクリックしてから、リストされているネットワーク インターフェイスのみをクリックします。

	![VM のデプロイ タイル](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. **[ネットワーク インターフェイス]** ブレードで、**[すべての設定]**、**[IP アドレス]** の順にクリックして、**[割り当て]** と **[IP アドレス]** の値を確認します。

	![VM のデプロイ タイル](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## 既存の VM に静的プライベート IP アドレスを追加する方法
上記の手順を使用して作成した VM に静的プライベート IP アドレスを追加するには、次の手順に従います。

1. 上記の **[IP アドレス]** ブレードで、**[割り当て]** の下にある **[静的]** をクリックします。
2. **[IP アドレス]** に「*192.168.1.101*」と入力してから、**[保存]** をクリックします。

	![プレビュー ポータルでの VM の作成](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE]**[保存]** をクリックした後、割り当てがまだ **[動的]** に設定されている場合は、入力した IP アドレスが既に使用されていることを意味します。別の IP アドレスを試してください。

## VM から静的プライベート IP アドレスを削除する方法
上記で作成した VM から静的プライベート IP アドレスを削除するには、以下の手順に従います。
	
1. 上記の **[IP アドレス]** ブレードで、**[割り当て]** の下にある **[動的]** をクリックしてから、**[保存]** をクリックします。

## 次のステップ

- [予約済みパブリック IP](../virtual-networks-reserved-public-ip) アドレスについて理解する。
- [インスタンス レベル パブリック IP (ILPIP)](../virtual-networks-instance-level-public-ip) アドレスについて理解する。
- [予約済み IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx) を確認する。

<!---HONumber=Oct15_HO3-->