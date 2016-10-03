<properties
   pageTitle="Resource Manager で Azure CLI を使用してアプリケーション ゲートウェイを作成する | Microsoft Azure"
   description="Resource Manager で Azure CLI を使用してアプリケーション ゲートウェイを作成する方法について説明します。"
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

# Azure CLI を使用してアプリケーション ゲートウェイを作成する

Azure Application Gateway はレイヤー 7 のロード バランサーです。クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどのアプリケーション配信機能を備えています。

> [AZURE.SELECTOR]
- [Azure ポータル](application-gateway-create-gateway-portal.md)
- [Azure Resource Manager の PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-gateway.md)
- [Azure Resource Manager テンプレート](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

## 前提条件: Azure CLI のインストール

この記事の手順を実行するには、[Mac、Linux、および Windows 用の Azure コマンド ライン インターフェイス (Azure CLI) をインストールし](../xplat-cli-install.md)、[Azure にログオン](../xplat-cli-connect.md)する必要があります。

> [AZURE.NOTE] Azure アカウントをお持ちでない場合は、取得する必要があります。[ここで無料試用版](../active-directory/sign-up-organization.md)にサインアップしてください。

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

## Azure へのログイン

**Microsoft Azure コマンド プロンプト**を開き、ログインします。

    azure login

上記の例に従って入力すると、コードが表示されます。ブラウザーで https://aka.ms/devicelogin に移動して、ログイン プロセスを続行します。

![cmd showing device login][1]

ブラウザーで、受け取ったコードを入力します。サインイン ページにリダイレクトされます。

![browser to enter code][2]

コードを入力してサインインした後、ブラウザーを閉じてこのシナリオに基づいて操作を続行します。

![successfully signed in][3]

## Resource Manager モードへの切り替え

    azure config mode arm

## リソース グループの作成

アプリケーション ゲートウェイを作成する前に、そのアプリケーション ゲートウェイの追加先となるリソース グループを作成します。コマンドを次に示します。

    azure group create -n AdatumAppGatewayRG -l eastus

## 仮想ネットワークの作成

リソース グループを作成したら、アプリケーション ゲートウェイに使用する仮想ネットワークを作成します。次の例では、前回のシナリオで定義した 10.0.0.0/16 をアドレス空間として使用しています。

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## サブネットの作成

仮想ネットワークを作成したら、アプリケーション ゲートウェイ用のサブネットを追加します。アプリケーション ゲートウェイと同じ仮想ネットワークで Web アプリをホストし、アプリケーション ゲートウェイと一緒に使用する予定がある場合は、別のサブネットを確保できるだけの余裕を持たせてください。

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## アプリケーション ゲートウェイの作成

仮想ネットワークとサブネットを作成すれば、アプリケーション ゲートウェイの前提条件は満たされたことになります。以下の手順では、それに加えて、あらかじめエクスポートしておいた .pfx 証明書とそのパスワードが必要となります。バックエンド用の IP アドレスは、ご使用のバックエンド サーバーの IP アドレスです。バックエンド サーバーに該当する仮想ネットワーク内のプライベート IP、パブリック IP、または完全修飾ドメイン名を指定してください。

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard



この例では、リスナー、バックエンド プール、バックエンド http 設定、規則の既定の設定を持つ基本的なアプリケーション ゲートウェイを作成しています。さらに、SSL オフロードの構成も行われます。プロビジョニングが成功したら、独自のデプロイに合わせて、これらの設定を変更することができます。前の手順でバックエンド プールに対して既に Web アプリケーションを定義している場合、アプリケーション ゲートウェイを作成すると負荷分散が開始されます。

## 次のステップ

[カスタムの正常性プローブの作成](application-gateway-create-probe-portal.md)に関する記事を参照して、カスタムの正常性プローブを作成する方法を確認します。

[SSL オフロードの構成](application-gateway-ssl-arm.md)に関する記事を参照して、SSL オフロードを構成して Web サーバーでのコストのかかる SSL 暗号化解除を回避する方法を確認します。

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png

<!---HONumber=AcomDC_0921_2016-->