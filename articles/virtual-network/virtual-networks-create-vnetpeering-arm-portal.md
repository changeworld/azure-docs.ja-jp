<properties
   pageTitle="Azure ポータルを使用した VNet ピアリングの作成 | Microsoft Azure"
   description="Resource Manager で Azure ポータルを使用して仮想ネットワークを作成する方法を説明します。"
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/02/2016"
   ms.author="narayanannamalai"/>

# Azure ポータルを使用した仮想ネットワーク ピアリングの作成

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

上記のシナリオに基づいた VNet ピアリングを Azure ポータルを使用して作成するには、次の手順に従います。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. VNET ピアリングを確立するには、2 つの VNet 間で 2 つのリンク (各方向につき 1 つ) を作成する必要があります。まず、VNET1 から VNET2 への VNET ピアリング リンクを作成しましょう。ポータルで **[参照]** をクリックし、**[仮想ネットワーク]** を選択します。

	![Create VNet peering in Azure portal](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)

3. [仮想ネットワーク] ブレードで [VNET1] を選択し、[ピアリング]、[追加] の順にクリックします。

	![ピアリングの選択](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)

4. [ピアリングの追加] ブレードで、ピアリング リンクに「LinkToVnet2」という名前を付け、サブスクリプションとピア仮想ネットワーク (VNET2) を選択して、[OK] をクリックします。

	![VNet へのリンク](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)

5. この VNET ピアリング リンクが作成されると、リンクの状態は次のように表示されます。

	![リンクの状態](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)

6. 次に VNET2 から VNET1 への VNWT ピアリング リンクを作成します。[仮想ネットワーク] ブレードで [VNET2] を選択し、[ピアリング]、[追加] の順にクリックします。

	![他の VNet からのピア](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)

7. [ピアリングの追加] ブレードで、ピアリング リンクに「LinkToVnet1」という名前を付け、サブスクリプションとピア仮想ネットワークを選択して、[OK] をクリックします。

	![仮想ネットワーク タイルの作成](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)

8. この VNET ピアリング リンクが作成されると、リンクの状態は次のように表示されます。

	![最終的なリンクの状態](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)

9. LinkToVnet2 の状態を確認すると、こちらも "接続済み" に変わっています。

	![Final link state 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

10. 注: VNET ピアリングは両方のリンクが接続されている場合にのみ確立されます。

それぞれのリンクには、構成可能なプロパティがいくつかあります。

|オプション|Description|既定値|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|ピア VNet のアドレス空間を Virtual\_network タグの一部として含めるかどうかを選択します。|はい|
|AllowForwardedTraffic|ピアリングされた VNet 以外の送信元のトラフィックを許可するか破棄するかを選択します。|いいえ|
|AllowGatewayTransit|VNet ゲートウェイの使用をピア VNet に許可するかどうかを選択します。|いいえ|
|UseRemoteGateways|ピアの VNet ゲートウェイを使用します。ピア VNet でゲートウェイが構成され、かつ AllowGatewayTransit が選択されている必要があります。ゲートウェイをローカルで構成した場合、このオプションは使用できません。|いいえ|

上記の一連のプロパティは、VNet ピアリングの各リンクに存在します。ポータルから [VNet Peering Link (VNet ピアリング リンク)] をクリックし、利用可能なオプションに変更を加えた後、[保存] をクリックするとその変更内容が反映されます。

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. この例では、2 つのサブスクリプション (A と B) と、それぞれのサブスクリプションにおいて特権が与えられた 2 人のユーザー (UserA と UserB) を使用します。
3. ポータルで [参照] をクリックし、[仮想ネットワーク] を選択します。[VNET] をクリックし、[追加] をクリックします。

    ![Scenario 2 Browse](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)

4. [アクセスを追加] ブレードで、ロールを選択し、[ネットワーク作成協力者] を選択して、[ユーザーの追加] をクリックし、UserB のサインイン名を入力して [OK] をクリックします。

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

    これは必須ではありません。ユーザーが個々の VNet に対して個別にピアリング要求を行った場合でも、双方の要求が合致すればピアリングは確立されます。相手側 VNet の特権ユーザーをローカル VNet のユーザーとして追加すると、ポータルでの設定が簡単になります。

5. 次に、SubscriptionB の特権ユーザーである UserB で Azure ポータルにログインします。前述の手順に従って UserA をネットワーク作成協力者として追加します。

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    注: 承認が問題なく有効になっていることを確認するために、ブラウザーで両方のユーザー セッションから一度ログオフし、その後ログオンしてください。

6. UserA としてポータルにログインし、VNET3 ブレードに移動して [ピアリング] をクリックし、[リソース ID を知っている] チェック ボックスをオンにして、VNET5 のリソース ID を以下の形式で入力します。

    /subscriptions/<サブスクリプション ID>/resourceGroups/<リソース グループ名>/providers/Microsoft.Network/VirtualNetwork/<VNET 名>

    ![Resource ID](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)

7. UserB としてポータルにログインし、前述の手順に従って VNET5 から VNet3 へのピアリング リンクを作成します。

    ![Resource ID 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)

8. ピアリングが確立され、VNet3 内の仮想マシンが VNet5 内の仮想マシンと通信できるようになります。

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. まず、HubVnet から VNET1 への VNet ピアリング リンクを作成します。このリンクでは [転送されたトラフィックを許可する] チェック ボックスがオフになっていることに注目してください。

    ![Basic Peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)

2. 次に、VNET1 から HubVnet へのピアリング リンクを作成します。[転送されたトラフィックを許可する] チェック ボックスがオンになっていることに注目してください。

    ![Basic Peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)

3. ピアリングが確立されたら、こちらの[記事](virtual-network-create-udr-arm-ps.md)を参照してください。VNet1 トラフィックを仮想アプライアンス経由でリダイレクトするようにユーザー定義ルート (UDR) を設定することで、その機能を利用することができます。ルートの次ホップ アドレスを指定するときは、ピア VNet (HubVNet) に存在する仮想アプライアンスの IP アドレスを設定します。

## VNet ピアリングの削除

1.	ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2.	仮想ネットワーク ブレードにアクセスして [ピアリング] をクリックし、削除するリンクをクリックして [削除] ボタンをクリックします。

    ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)

3. VNet ピアリングのいずれかのリンクを削除すると、ピアのリンク状態が "切断" に変わります。

    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)

4. この状態になると、ピア リンク状態が "開始済み" に変化するまではリンクを再作成できません。両方のリンクを削除してから、VNet ピアリングを作成し直すことをお勧めします。

<!---HONumber=AcomDC_0824_2016-->