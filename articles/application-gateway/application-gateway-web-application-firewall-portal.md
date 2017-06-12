---
title: "Web アプリケーション ファイアウォールのある Azure Application Gateway の作成または更新 | Microsoft Docs"
description: "ポータルを使用して Web アプリケーション ファイアウォールのある Application Gateway を作成する方法について説明します。"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 224aa0db2feb7a83bec5b4ec46140046d10f012e
ms.contentlocale: ja-jp
ms.lasthandoff: 05/04/2017


---

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>ポータルを使用した、Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイの作成

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-web-application-firewall-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-web-application-firewall-powershell.md)

Web アプリケーション ファイアウォールが有効なアプリケーション ゲートウェイを作成する方法について説明します。

Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) は、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの一般的な Web ベースの攻撃から Web アプリケーションを保護します。 Web アプリケーションは、OWASP の上位 10 件の一般的な Web 脆弱性の多くを防ぎます。

## <a name="scenarios"></a>シナリオ

この記事では、次の 2 つのシナリオを扱います。

最初のシナリオでは、[Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する](#create-an-application-gateway-with-web-application-firewall)方法について説明します。

2 番目のシナリオでは、[既存のアプリケーション ゲートウェイに Web アプリケーション ファイアウォールを追加する](#add-web-application-firewall-to-an-existing-application-gateway)方法について説明します。

![Scenario example][scenario]

> [!NOTE]
> カスタムの正常性プローブ、バックエンド プール アドレス、追加規則など、アプリケーション ゲートウェイの追加の構成は、初めてデプロイしている間ではなく、アプリケーション ゲートウェイが構成された後で構成されます。

## <a name="before-you-begin"></a>開始する前に

Azure Application Gateway には、専用のサブネットが必要です。 仮想ネットワークを作成する場合は、複数のサブネットを持つことができるように十分なアドレス空間を残しておいてください。 アプリケーション ゲートウェイをサブネットにデプロイすると、追加のアプリケーション ゲートウェイのみをそのサブネットにデプロイすることができます。

##<a name="add-web-application-firewall-to-an-existing-application-gateway"></a> 既存のアプリケーション ゲートウェイに Web アプリケーション ファイアウォールを追加する

この例では、防止モードで Web アプリケーション ファイアウォールをサポートするように、既存のアプリケーション ゲートウェイを更新します。

1. Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** をクリックします。 **[すべてのリソース]** ブレードで Application Gateway をクリックします。 選択したサブスクリプションに既存のリソースがいくつもある場合は、**[名前でフィルター]** ボックスに名前と入力すると、 目的の DNS ゾーンがすぐに見つかります。

   ![Creating Application Gateway][1]

1. **[Web アプリケーション ファイアウォール]** をクリックして、アプリケーション ゲートウェイの設定を更新します。 完了したら、 **[保存]**

    Web アプリケーション ファイアウォールをサポートするように既存のアプリケーション ゲートウェイを更新する設定は次のとおりです。

   | **設定** | **値** | **詳細**
   |---|---|---|
   |**[WAF 層へのアップグレード]**| オン | アプリケーション ゲートウェイのレベルを WAF 層に設定します。|
   |**[ファイアウォールの状態]**| 有効 | 有効 | この設定では、WAF 上のファイアウォールが有効です。|
   |**[ファイアウォール モード]** | 防止 | Web アプリケーション ファイアウォールが悪意のあるトラフィックを処理する方法を指定します。 **[検出]** モードではイベントの記録のみを行い、**[防止]** モードではイベントをログに記録し、悪意のあるトラフィックを停止します。|
   |**[ルール セット]**|3.0|バックエンド プールのメンバーの保護に使用される[コア ルール セット](application-gateway-web-application-firewall-overview.md#core-rule-sets)を決定します。|
   |**[無効化されたルールを構成する]**|多様|特定の[ルールとルール グループ](application-gateway-crs-rulegroups-rules.md)を無効にして、起こりうる誤検知を防ぎます。|

    >[!NOTE]
    > 既存のアプリケーション ゲートウェイを WAF SKU にアップグレードするときに、SKU サイズは **[中]** に変更されます。 これは構成が完了した後に再設定できます。

    ![blade showing basic settings][2-1]

    > [!NOTE]
    > Web アプリケーション ファイアウォール ログを表示するには、診断を有効にし、ApplicationGatewayFirewallLog を選択している必要があります。 テスト目的では、インスタンス数として 1 を選択できます。 重要なのは、2 より小さいインスタンス数は SLA の対象外のため、推奨されていないことを把握しておくことです。 Web アプリケーション ファイアウォールを使用する場合、小規模のゲートウェイを利用できません。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する

このシナリオで、以下の作業を行います。

* 2 つのインスタンスを持つ中規模の、Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成します。
* 予約済み CIDR ブロック 10.0.0.0/16 を持つ、AdatumAppGatewayVNET という名前の仮想ネットワークを作成します。
* CIDR ブロックとして 10.0.0.0/28 を使用する Appgatewaysubnet という名前のサブネットを作成します。
* SSL オフロード用の証明書を構成します。

1. [Azure ポータル](https://portal.azure.com)にログインします。 まだアカウントを持っていない場合は、[1 か月間の無料試用版](https://azure.microsoft.com/free)にサインアップできます。
1. ポータルの [お気に入り] ウィンドウで **[新規]** をクリックします。
1. **[新規]** ブレードの **[ネットワーキング]** をクリックします。 **[ネットワーキング]** ブレードの **[Application Gateway]** をクリックします (下図)。
1. Azure ポータルに移動し、**[新規]**、 > **[ネットワーク]** > 、**[Application Gateway]** の順にクリックします。

    ![Creating Application Gateway][1]

1. **[基本]** ブレードが表示されたら、以下の値を入力し、**[OK]** をクリックします。

   | **設定** | **値** | **詳細**
   |---|---|---|
   |**名前**|AdatumAppGateway|Application Gateway の名前。|
   |**レベル**|WAF|使用できる値は Standard と WAF です。 WAF の詳細については、「[Web アプリケーション ファイアウォール](application-gateway-web-application-firewall-overview.md)」を参照してください。|
   |**SKU サイズ**|中|Standard レベルを選択した場合の選択肢は、Small、Medium、Large です。 WAF レベルを選択した場合のオプションは、Medium と Large のみです。|
   |**インスタンス数**|2|高可用性のための Application Gateway のインスタンス数。 インスタンス数 1 はテスト目的でのみ使用してください。|
   |**サブスクリプション**|<該当するサブスクリプション>|アプリケーション ゲートウェイの作成先となるサブスクリプションを選択します。|
   |**[リソース グループ]**|**[新規作成]:** AdatumAppGatewayRG|リソース グループを作成します。 選択したサブスクリプション内で一意となるリソース グループ名を使用してください。 リソース グループについて詳しくは、[Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) の概要に関する記事をご覧ください。|
   |**場所**|米国西部||

   ![blade showing basic settings][2-2]

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

1. **[リスナー構成]**の下の **[設定]** ブレードで、**[プロトコル]**の下の **[HTTP]** をクリックします。 **https**を使用するには、証明書が必要です。 証明書の秘密キーが必要であるため、証明書の .pfx エクスポートとファイルのパスワードを指定する必要があります。

1. **WAF** 固有の設定を構成します。

   |**設定** | **値** | **詳細** |
   |---|---|---|
   |**[ファイアウォールの状態]**| 有効| WAF のオンとオフを切り替えます。|
   |**[ファイアウォール モード]** | 防止| 悪意のあるトラフィックに対処するときの WAF のアクションを決定します。 **[検出]** を選択すると、トラフィックがログに記録されます。  **[防止]** を選択すると、トラフィックがログに記録され、"403 許可されていません" という応答で停止します。|


1. [概要] ページを確認し、 **[OK]**をクリックします。  これで、アプリケーション ゲートウェイがキューに登録され、作成されます。

1. アプリケーション ゲートウェイが作成されたら、ポータルでそのゲートウェイに移動し、アプリケーション ゲートウェイの構成を続けます。

    ![Application Gateway resource view][10]

これで、リスナー、バックエンド プール、バックエンド http 設定、ルールの既定の設定を持つ基本的なアプリケーション ゲートウェイが作成されます。 プロビジョニングが成功したら、独自のデプロイに合わせて、これらの設定を変更することができます。

> [!NOTE]
> 基本的な Web アプリケーション ファイアウォールの構成で作成されたアプリケーション ゲートウェイは、CRS 3.0 の保護で構成されています。

## <a name="next-steps"></a>次のステップ

[Application Gateway の診断](application-gateway-diagnostics.md)に関するページにアクセスして、診断ログを構成する方法と、Web アプリケーション ファイアウォールで検出または防止されたイベントを記録する方法を確認します。

[カスタムの正常性プローブの作成](application-gateway-create-probe-portal.md)

[SSL オフロードの構成](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png

