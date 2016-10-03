<properties
   pageTitle="ポータルを使用してアプリケーション ゲートウェイを作成する | Microsoft Azure"
   description="ポータルを使用してアプリケーション ゲートウェイを作成する方法について説明します。"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace" />

# ポータルを使用してアプリケーション ゲートウェイを作成する

Azure Application Gateway はレイヤー 7 のロード バランサーです。クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどの多くのアプリケーション配信コントローラー (ADC) 機能を備えています。サポートされている機能の完全な一覧については、「[Application Gateway の概要](application-gateway-introduction.md)」を参照してください。

> [AZURE.SELECTOR]
- [Azure ポータル](application-gateway-create-gateway-portal.md)
- [Azure Resource Manager の PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-gateway.md)
- [Azure Resource Manager テンプレート](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

## シナリオ

このシナリオでは、Azure ポータルを使用してアプリケーション ゲートウェイを作成する方法について説明します。

このシナリオで、以下の作業を行います。

- 2 つのインスタンスを持つ中規模のアプリケーション ゲートウェイを作成します。
- 予約済み CIDR ブロック 10.0.0.0/16 を持つ、AdatumAppGatewayVNET という名前の仮想ネットワークを作成します。
- CIDR ブロックとして 10.0.0.0/28 を使用する Appgatewaysubnet という名前のサブネットを作成します。
- SSL オフロード用の証明書を構成します。

![Scenario example][scenario]

>[AZURE.NOTE] カスタムの正常性プローブ、バックエンド プール アドレス、追加規則など、アプリケーション ゲートウェイの追加の構成は、初めてデプロイしている間ではなく、アプリケーション ゲートウェイが構成された後で構成されます。

## 開始する前に

Azure Application Gateway には、専用のサブネットが必要です。仮想ネットワークを作成する場合は、複数のサブネットを持つことができるように十分なアドレス空間を残しておいてください。アプリケーション ゲートウェイをサブネットにデプロイすると、追加のアプリケーション ゲートウェイのみをそのサブネットにデプロイすることができます。

## アプリケーション ゲートウェイの作成

### 手順 1

Azure ポータルに移動し、**[新規]**、**[ネットワーキング]**、**[Application Gateway]** の順にクリックします。

![Creating Application Gateway][1]

### 手順 2.

次に、アプリケーション ゲートウェイに関する基本情報を入力します。完了したら、**[OK]** をクリックします。

基本設定に必要な情報は次のとおりです。

- **[名前]** - アプリケーション ゲートウェイの名前。
- **[SKU サイズ]** - アプリケーション ゲートウェイのサイズ。S、M、L のオプションを使用できます。
- **[インスタンス数]** - インスタンスの数。この値は、2 ～ 10 の数値で指定する必要があります。
- **[リソース グループ]** - アプリケーション ゲートウェイを保持するリソース グループ。既存のリソース グループを指定することも、新しいグループを指定することもできます。
- **[場所]** - アプリケーション ゲートウェイのリージョン。リソース グループと同じ場所にします。"*この場所は、仮想ネットワークとパブリック IP はゲートウェイと同じ場所に存在する必要があるため、重要です。*"

![blade showing basic settings][2]

>[AZURE.NOTE] テスト目的では、インスタンス数として 1 を選択できます。重要なのは、2 より小さいインスタンス数は SLA の対象外のため、推奨されていないことを把握しておくことです。小規模のゲートウェイは開発テスト用であり、運用目的ではありません。

### 手順 3.

基本設定を定義したら、次の手順では、使用する仮想ネットワークを定義します。仮想ネットワークは、アプリケーション ゲートウェイによる負荷分散の対象となるアプリケーションを収容します。

仮想ネットワークを構成するには、**[仮想ネットワークの選択]** をクリックします。

![blade showing settings for application gateway][3]

### 手順 4.

*[仮想ネットワークの選択]* ブレードで、**[新規作成]** をクリックします。

このシナリオでは説明しませんが、この時点で既存の仮想ネットワークを選択することもできます。既存の仮想ネットワークを使用する場合、仮想ネットワークで使用する空のサブネットまたはアプリケーション ゲートウェイ リソースのみのサブネットが必要です。

![choose virtual network blade][4]

### 手順 5.

**[仮想ネットワークの作成]** ブレードで、前述の「[シナリオ](#scenario)」で説明したように、ネットワーク情報を入力します。

![Create virtual network blade with information entered][5]

### 手順 6.

仮想ネットワークが作成されたら、次の手順ではアプリケーション ゲートウェイのフロントエンド IP を定義します。現時点では、フロントエンドにはパブリック IP アドレスかプライベート IP アドレスを選択できます。どちらを選択するかは、アプリケーションがインターネットに接続しているか、内部使用のみであるかによって決まります。このシナリオでは、パブリック IP アドレスを使用することを前提としています。プライベート IP アドレスを選択するには、**[プライベート]** ボタンをクリックします。自動的に割り当てられた IP アドレスが選択されます。または、**[特定のプライベート IP アドレスの選択]** チェック ボックスをオンにして、手動で入力することもできます。

### 手順 7.

**[パブリック IP アドレスの選択]** をクリックします。既存のパブリック IP アドレスが使用可能な場合は、この時点で選択できます。このシナリオでは、新しいパブリック IP アドレスを作成します。**[新規作成]** をクリックします。

![Choose public IP address blade][6]

### 手順 8.

次に、パブリック IP アドレスにフレンドリ名を付け、**[OK]** をクリックします。

![Create public IP Address blade][7]

### 手順 9.

アプリケーション ゲートウェイを作成するときに構成する最後の設定は、リスナー構成です。**http** が使用される場合は、何も構成する必要がないため、**[OK]** をクリックします。**https** を利用する場合は、さらに構成が必要になります。

**https** を使用するには、証明書が必要です。証明書の秘密キーが必要であるため、証明書の .pfx エクスポートとファイルのパスワードを指定する必要があります。


![blank Listener Configuration section on Settings blade][8]

### 手順 10.

**[HTTPS]** をクリックし、**[PFX 証明書のアップロード]** テキスト ボックスの横にある**フォルダー** アイコンをクリックします。ファイル システム上の .pfx 証明書ファイルに移動します。そのファイルを選択したら、証明書にフレンドリ名を付け、.pfx ファイルのパスワードを入力します。

完了したら **[OK]** をクリックし、アプリケーション ゲートウェイの設定を確認します。

![Listener Configuration section on Settings blade][9]

### 手順 11.

[概要] ページを確認し、**[OK]** をクリックします。これで、アプリケーション ゲートウェイがキューに登録され、作成されます。

### 手順 12.

アプリケーション ゲートウェイが作成されたら、ポータルでそのゲートウェイに移動し、アプリケーション ゲートウェイの構成を続けます。

![Application Gateway resource view][10]

これで、リスナー、バックエンド プール、バックエンド http 設定、ルールの既定の設定を持つ基本的なアプリケーション ゲートウェイが作成されます。プロビジョニングが成功したら、独自のデプロイに合わせて、これらの設定を変更することができます。

## 次のステップ

[カスタムの正常性プローブの作成](application-gateway-create-probe-portal.md)に関する記事を参照して、カスタムの正常性プローブを作成する方法を確認します。

[SSL オフロードの構成](application-gateway-ssl-portal.md)に関する記事を参照して、SSL オフロードを構成して Web サーバーでのコストのかかる SSL 暗号化解除を回避する方法を確認します。

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[6]: ./media/application-gateway-create-gateway-portal/figure6.png
[7]: ./media/application-gateway-create-gateway-portal/figure7.png
[8]: ./media/application-gateway-create-gateway-portal/figure8.png
[9]: ./media/application-gateway-create-gateway-portal/figure9.png
[10]: ./media/application-gateway-create-gateway-portal/figure10.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

<!---HONumber=AcomDC_0921_2016-->