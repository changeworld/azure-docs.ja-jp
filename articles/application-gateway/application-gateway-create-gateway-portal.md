---
title: "ポータルを使用してアプリケーション ゲートウェイを作成する | Microsoft Docs"
description: "ポータルを使用してアプリケーション ゲートウェイを作成する方法について説明します。"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 2195eb4ce0e22c8c7c72ac0638ab927f13c4d454
ms.contentlocale: ja-jp
ms.lasthandoff: 05/02/2017


---
# <a name="create-an-application-gateway-by-using-the-portal"></a>ポータルを使用してアプリケーション ゲートウェイを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-gateway.md)
> * [Azure Resource Manager テンプレート](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

SSL オフロードを使用して Application Gateway を作成する方法について説明します。

![Scenario example][scenario]

Application Gateway は、アプリケーション配信コントローラー (ADC) をサービスとして提供する専用仮想アプライアンスで、さまざまなレイヤー 7 負荷分散機能をアプリケーションで利用できるようにします。

このシナリオで、以下の作業を行います。

1. SSL オフロードとそのサブネット内の 2 つのインスタンスを使用して、[中規模の Application Gateway を作成](#create-an-application-gateway)します。
1. [バックエンド プールにサーバーを追加](#add-servers-to-backend-pools)します。
1. [リソースをすべて削除する](#delete-all-resources)。 この演習で作成したいくつかのリソースは、プロビジョニングされている間、料金が発生します。 料金を最小限に抑えるために、演習を終えた後は必ず、このセクションの手順を実行し、作成したリソースを削除してください。



> [!IMPORTANT]
> カスタムの正常性プローブ、バックエンド プール アドレス、追加規則など、アプリケーション ゲートウェイの追加の構成は、初めてデプロイしている間ではなく、アプリケーション ゲートウェイが構成された後で構成されます。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

Application Gateway を作成するために必要な手順を次に示します。 Application Gateway には、専用のサブネットが必要です。 仮想ネットワークを作成する場合は、複数のサブネットを持つことができるように十分なアドレス空間を残しておいてください。 アプリケーション ゲートウェイをサブネットにデプロイすると、追加のアプリケーション ゲートウェイのみをそのサブネットにデプロイすることができます。

1. [Azure ポータル](https://portal.azure.com)にログインします。 まだアカウントを持っていない場合は、[1 か月間の無料試用版](https://azure.microsoft.com/free)にサインアップできます。
1. ポータルの [お気に入り] ウィンドウで **[新規]** をクリックします。
1. **[新規]** ブレードの **[ネットワーキング]** をクリックします。 **[ネットワーキング]** ブレードの **[Application Gateway]** をクリックします (下図)。

    ![Creating Application Gateway][1]

1. **[基本]** ブレードが表示されたら、以下の値を入力し、**[OK]** をクリックします。

   | **設定** | **値** | **詳細**
   |---|---|---|
   |**名前**|AdatumAppGateway|Application Gateway の名前。|
   |**レベル**|Standard|使用できる値は Standard と WAF です。 WAF の詳細については、「[Web アプリケーション ファイアウォール](application-gateway-web-application-firewall-overview.md)」を参照してください。|
   |**SKU サイズ**|中|Standard レベルを選択した場合の選択肢は、Small、Medium、Large です。 WAF レベルを選択した場合のオプションは、Medium と Large のみです。|
   |**インスタンス数**|2|高可用性のための Application Gateway のインスタンス数。 インスタンス数 1 はテスト目的でのみ使用してください。|
   |**サブスクリプション**|<該当するサブスクリプション>|アプリケーション ゲートウェイの作成先となるサブスクリプションを選択します。|
   |**[リソース グループ]**|**[新規作成]:** AdatumAppGatewayRG|リソース グループを作成します。 選択したサブスクリプション内で一意となるリソース グループ名を使用してください。 リソース グループについて詳しくは、[Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) の概要に関する記事をご覧ください。|
   |**場所**|米国西部||

1. **[仮想ネットワーク]**の下に表示される **[設定]** ブレードで、**[仮想ネットワークの選択]** をクリックします。 これで、**[仮想ネットワークの選択]** ブレードが開きます。  **[新規作成]** をクリックして **[仮想ネットワークの作成]** ブレードを開きます。

 ![仮想ネットワークの選択][2]

1. **[仮想ネットワークの作成]** ブレードで、次の値を入力して **[OK]** をクリックします。 これで、**[仮想ネットワークの作成]** ブレードと **[仮想ネットワークの選択]**ブレードが閉じます。 また、**[設定]** ブレードの **[サブネット]** フィールドに値が入力されます。

   |**設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|AdatumAppGatewayVNET|Application Gateway の名前。|
   |**アドレス空間**|10.0.0.0/16| これは仮想ネットワークのアドレス空間です。|
   |**サブネット名**|AppGatewaySubnet|Application Gateway のサブネット名。|
   |**サブネットのアドレス範囲**|10.0.0.0/28| このサブネットを使用すると、バックエンド プール メンバーのために仮想ネットワーク内にさらにサブネットを作成できます。|

1. **[フロントエンド IP 構成]**の **[設定]** ブレードで、**[IP アドレスの種類]** として **[パブリック]** を選択します。

1. **[パブリック IP アドアレス]**の下の **[設定]** ブレードで、**[パブリック IP アドレスの選択]** をクリックすると、**[パブリック IP アドレスの選択]** ブレードが開きます。ここで、**[新規作成]** をクリックします。

 ![パブリック IP の選択][3]

1. **[パブリック IP アドレスの作成]** ブレードで、既定値を受け入れて、**[OK]** をクリックします。 これにより、**[パブリック IP アドレスの選択]** ブレードと **[パブリック IP アドレスの作成]** ブレードが閉じ、**[パブリック IP アドレス]** に選択したパブリック IP アドレスが入力されます。

1. **[リスナー構成]**の下の **[設定]** ブレードで、**[プロトコル]**の下の **[HTTPS]** をクリックします。 これでフィールドが追加されます。 **[PFX 証明書のアップロード]** フィールドのフォルダー アイコンをクリックして、適切な .pfx 証明書を選択します。 追加の **[リスナー構成]** フィールドに次の情報を入力します。

   |**設定** | **値** | **詳細** |
   |---|---|---|
   |名前|証明書の名前|この値は、証明書を参照するために使用するフレンドリ名です。|
   |パスワード|.pfx のパスワード| これは秘密キーに使用されるパスワードです。|

1. **[設定]** ブレードで **[OK]** をクリックして続行します。

1. **[概要]** ブレードで設定を確認します。**[OK]** をクリックすると、Application Gateway の作成が開始します。 Application Gateway の作成は実行時間が長いタスクです。完了するまでに時間がかかります。

## <a name="add-servers-to-backend-pools"></a>バックエンド プールヘのサーバーの追加

アプリケーション ゲートウェイが作成されたら、負荷分散するアプリケーションをホストするシステムを、引き続きアプリケーション ゲートウェイに追加する必要があります。 これらのサーバーの IP アドレス、FQDN、または NIC は、バックエンド アドレス プールに追加されます。

### <a name="ip-address-or-fqdn"></a>IP アドレスまたは FQDN

1. Application Gateway が作成されたら、Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** をクリックします。 [すべてのリソース] ブレードで **AdatumAppGateway** Application Gateway をクリックします。 選択したサブスクリプションに既存のリソースがいくつもある場合は、**[名前でフィルター]** ボックスに「**AdatumAppGateway**」と入力すると 目的のアプリケーション ゲートウェイがすぐに見つかります。

1. 作成した Application Gateway が表示されます。 **[バックエンド プール]** をクリックして、現在のバックエンド プール **appGatewayBackendPool** を選択すると、**[appGatewayBackendPool]** ブレードが開きます。

   ![Application Gateway のバックエンド プール][4]

1. **[ターゲットの追加]** をクリックして FQDN 値の IP アドレスを追加します。 **[IP アドレスまたは FQDN]** を **[タイプ]** として選択し、フィールドに IP アドレスまたは FQDN を入力します。 その他のバックエンド プール メンバーに対してこの手順を繰り返します。 完了したら、**[保存]** をクリックします。

### <a name="virtual-machine-and-nic"></a>仮想マシンと NIC

バックエンド プールのメンバーとして、仮想マシンの NIC を追加することもできます。 Application Gateway と同じ仮想ネットワーク内の仮想マシンのみ、ドロップダウン リストで利用可能です。

1. Application Gateway が作成されたら、Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** をクリックします。 [すべてのリソース] ブレードで **AdatumAppGateway** Application Gateway をクリックします。 選択したサブスクリプションに既存のリソースがいくつもある場合は、**[名前でフィルター]** ボックスに「**AdatumAppGateway**」と入力すると 目的のアプリケーション ゲートウェイがすぐに見つかります。

1. 作成した Application Gateway が表示されます。 **[バックエンド プール]** をクリックして、現在のバックエンド プール **appGatewayBackendPool** を選択すると、**[appGatewayBackendPool]** ブレードが開きます。

   ![Application Gateway のバックエンド プール][5]

1. **[ターゲットの追加]** をクリックして FQDN 値の IP アドレスを追加します。 **[仮想マシン]** を **[タイプ]** として選択し、使用する仮想マシンと NIC を選択します。 完了したら、**[保存]** をクリックします。

   > [!NOTE]
   > ドロップダウン リストで選択できるのは、Application Gateway と同じ仮想ネットワーク内の仮想マシンのみです。

## <a name="delete-all-resources"></a>すべてのリソースの削除

この記事で作成したリソースをすべて削除するには、次の手順を実行します。

1. Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** をクリックします。 [すべてのリソース] ブレードで **AdatumAppGatewayRG** リソース グループをクリックします。 選択したサブスクリプションに既存のリソースがいくつもある場合は、**[名前でフィルター]** ボックスに「**AdatumAppGatewayRG**」と入力すると 目的のリソース グループがすぐに見つかります。
1. **[AdatumAppGatewayRG]** ブレードで **[削除]** ボタンをクリックします。
1. 削除の意図を確認するために、リソース グループの名前を入力するよう求められます。 **[削除]** をクリックし、リソース グループの名前に「AdatumAppGateway」と入力して、[**削除**] をクリックします。 リソース グループを削除すると、そこに含まれているリソースもすべて削除されます。削除する前に、リソース グループの内容を必ず確認してください。 まずリソース グループに含まれているすべてのリソースが削除された後、リソース グループそのものが削除されます。 このプロセスには数分かかります。

## <a name="next-steps"></a>次のステップ

このシナリオでは、既定のアプリケーション ゲートウェイを作成します。 次の手順として、設定の変更、およびゲートウェイ内のルールの調整を実行して、アプリケーション ゲートウェイを構成します。 これらの手順について詳しくは、次の記事をご覧ください。

[カスタムの正常性プローブの作成](application-gateway-create-probe-portal.md)

[SSL オフロードの構成](application-gateway-ssl-portal.md)

アプリケーション ゲートウェイの機能の 1 つである [Web アプリケーション ファイアウォール](application-gateway-webapplicationfirewall-overview.md)を使用してアプリケーションを保護する方法について学びます。

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

