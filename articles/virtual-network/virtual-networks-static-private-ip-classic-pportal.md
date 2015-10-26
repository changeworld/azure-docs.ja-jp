<properties 
   pageTitle="プレビュー ポータルを使用してクラシック モードで静的プライベート IP を設定する方法 | Microsoft Azure"
   description="静的プライベート IP (DIP) とそれらをポータルのクラシック モードで管理する方法を理解します。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/08/2015"
   ms.author="telmos" />

# プレビュー ポータルでの静的プライベート IP アドレス (クラシック) の設定方法

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]この記事では、クラシック デプロイメント モデルについて説明します。[リソース マネージャーのデプロイメント モデルで静的プライベート IP アドレスを管理する](virtual-networks-static-private-ip-arm-pportal.md)こともできます。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

次のサンプルの手順では、単純な環境が既に作成されていると想定します。このドキュメントに表示されている手順を実行する場合は、まず、[vnet の作成](virtual-networks-create-vnet-classic-pportal.md)に関する記事に示されているテスト環境を構築します。

## VM 作成時に静的プライベート IP アドレスを指定する方法
静的プライベート IP *192.168.1.101* を使用して、*TestVNet* という名前の VNet の *FrontEnd* サブネットで *DNS01* という名前の VM を作成するには、以下の手順に従います。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. **[新規]**、**[Compute]**、**[Windows Server 2012 R2 Datacenter]** の順にクリックし、**[デプロイ モデルの選択]** の一覧に既に **[クラシック]** と表示されているのを確認してから **[作成]** をクリックします。

	![プレビュー ポータルでの VM の作成](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. **[VM の作成]** ブレードで、作成する VM の名前 (ここでは *DNS01*)、ローカル管理者のアカウント、およびパスワードを入力します。

	![プレビュー ポータルでの VM の作成](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. **[オプションの構成]**、**[ネットワーク]**、**[Virtual Network]** の順にクリックし、**[TestVNet]** をクリックします。**[TestVNet]** を使用できない場合は、場所として *[米国中央部]* を使用しており、この記事の冒頭で説明されているテスト環境が作成済みであることを確認してください。

	![プレビュー ポータルでの VM の作成](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. **[ネットワーク]** ブレードで、以下のように、現在選択されているサブネットが *FrontEnd* であることを確認し、**[IP アドレス]** をクリックしてから、**[IP アドレスの割り当て]** の下にある **[静的]** をクリックして、**[IP アドレス]** に 「*192.168.1.101*」と入力します。

	![プレビュー ポータルでの VM の作成](./media/virtual-networks-static-ip-classic-pportal/figure04.png)

6. **[IP アドレス]** ブレードで **[OK]** をクリックし、**[ネットワーク]** ブレードで **[OK]** をクリックしてから、**[オプションの構成]** ブレードで **[OK]** をクリックします。
7. **[VM の作成]** ブレードで、**[作成]** をクリックします。ダッシュ ボードに以下のタイルが表示されることを確認します。

	![プレビュー ポータルでの VM の作成](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## VM 用の静的プライベート IP アドレス情報を取得する方法

上記の手順で作成された VM の静的プライベート IP アドレス情報を表示するには、次の手順を実行します。

1. Azure プレビュー ポータルで、**[すべて参照]**、**[仮想マシン (クラシック)]**、**[DNS01]**、**[すべての設定]**、**[IP アドレス]** の順にクリックし、以下の IP アドレスの割り当てと IP アドレスを確認します。

	![プレビュー ポータルでの VM の作成](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## VM から静的プライベート IP アドレスを削除する方法
上記で作成した VM から静的プライベート IP アドレスを削除するには、以下の手順に従います。
	
1. 上記の **[IP アドレス]** ブレードで、**[IP アドレスの割り当て]** の右にある **[動的]** をクリックしてから、**[保存]**、**[はい]** の順にクリックします。

	![プレビュー ポータルでの VM の作成](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## 既存の VM に静的プライベート IP アドレスを追加する方法
上記の手順を使用して作成した VM に静的プライベート IP アドレスを追加するには、次の手順に従います。

1. 上記の **[IP アドレス]** ブレードで、**[IP アドレスの割り当て]** の右にある **[静的]** をクリックします。
2. **[IP アドレス]** に「*192.168.1.101*」と入力してから、**[保存]**、**[はい]** の順にクリックします。

## 次のステップ

- [予約済みパブリック IP](../virtual-networks-reserved-public-ip) アドレスについて理解する。
- [インスタンス レベル パブリック IP (ILPIP)](../virtual-networks-instance-level-public-ip) アドレスについて理解する。
- [予約済み IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx) を確認する。

<!---HONumber=Oct15_HO3-->