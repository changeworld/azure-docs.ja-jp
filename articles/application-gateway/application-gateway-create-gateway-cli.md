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
   ms.date="08/22/2016"
   ms.author="gwallace" />

# Azure CLI を使用してアプリケーション ゲートウェイを作成する

Azure Application Gateway はレイヤー 7 のロード バランサーです。クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどのアプリケーション配信機能を備えています。

> [AZURE.SELECTOR]
- [Azure ポータル](application-gateway-create-gateway-portal.md)
- [Azure Resource Manager の PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-gateway.md)
- [Azure Resource Manager テンプレート](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

<BR>

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

## リソース グループの作成

アプリケーション ゲートウェイを作成する前に、そのアプリケーション ゲートウェイの追加先となるリソース グループを作成します。次に示したのは、そのためのコマンドと出力例です。

    azure group create -n AdatumAppGatewayRG -l eastus

    info:    Executing command group create
    + Getting resource group AdatumAppGatewayRG
    + Creating resource group AdatumAppGatewayRG
    info:    Created resource group AdatumAppGatewayRG
    data:    Id:                  /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG
    data:    Name:                AdatumAppGatewayRG
    data:    Location:            eastus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

## 仮想ネットワークの作成

リソース グループを作成したら、アプリケーション ゲートウェイに使用する仮想ネットワークを作成します。次の例では、前回のシナリオで定義した 10.0.0.0/16 をアドレス空間として使用しています。

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

    info:    Executing command network vnet create
    + Looking up the virtual network "AdatumAppGatewayVNET"
    + Creating virtual network "AdatumAppGatewayVNET"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET
    data:    Name                            : AdatumAppGatewayVNET
    data:    Type                            : Microsoft.Network/virtualNetworks
    data:    Location                        : eastus
    data:    Provisioning state              : Succeeded
    data:    Address prefixes:
    data:      10.0.0.0/16
    info:    network vnet create command OK

## サブネットの作成

仮想ネットワークを作成したら、アプリケーション ゲートウェイ用のサブネットを追加します。アプリケーション ゲートウェイと同じ仮想ネットワークで Web アプリをホストし、アプリケーション ゲートウェイと一緒に使用する予定がある場合は、別のサブネットを確保できるだけの余裕を持たせてください。

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

    info:    Executing command network vnet subnet create
    verbose: Looking up the virtual network "AdatumAppGatewayVNET"
    verbose: Looking up the subnet "Appgatewaysubnet"
    verbose: Creating subnet "Appgatewaysubnet"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet
    data:    Name                            : Appgatewaysubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.0.0.0/28
    info:    network vnet subnet create command OK

## アプリケーション ゲートウェイの作成

仮想ネットワークとサブネットを作成すれば、アプリケーション ゲートウェイの前提条件は満たされたことになります。以下の手順では、それに加えて、あらかじめエクスポートしておいた .pfx 証明書とそのパスワードが必要となります。バックエンド用の IP アドレスは、ご使用のバックエンド サーバーの IP アドレスです。ご使用のバックエンド サーバーに該当する完全修飾ドメイン名か、パブリック IP、または仮想ネットワーク内のプライベート IP を指定してください。

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd

    info:    Executing command network application-gateway create
    + Looking up an application gateway "AdatumAppGateway"
    + Looking up the subnet "Appgatewaysubnet"
    warn:    Using default http listener protocol: https
    warn:    Using default gateway ip name: ipConfig01
    warn:    Using default sku name: Standard_Medium
    warn:    Using default sku tier: Standard
    warn:    Using default sku capacity: 2
    warn:    Using default frontend ip name: frontendIp01
    warn:    Using default frontend port name: frontendPort01
    warn:    Using default frontend port: 443
    warn:    Using default address pool name: pool01
    warn:    Using default http settings name: httpSettings01
    warn:    Using default http settings protocol: http
    warn:    Using default http settings port: 80
    warn:    Using default http settings cookie based affinity: Disabled
    warn:    Using default http listener name: listener01
    warn:    Using default request routing rule name: rule01
    warn:    Using default request routing rule type: Basic
    + Looking up the subnet "Appgatewaysubnet"
    + Creating configuration for an application gateway "AdatumAppGateway"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway
    data:    Name                            : AdatumAppGateway
    data:    Location                        : eastus
    data:    Provisioning state              : Succeeded
    data:    Sku                             : Standard_Medium
    data:    Resource Group                  : AdatumAppGatewayRG
    data:    Gateway IP configations         : [ipConfig01]
    data:    SSL cerificates                 : [cert01]
    data:    Frontend ip configurations      : [frontendIp01]
    data:    Frontend ports                  : [frontendPort01]
    data:    Backend address pools           : [pool01]
    data:    Backend http settings           : [httpSettings01]
    data:    Http listeners                  : [listener01]
    data:    Request routing rules           : [rule01]
    data:    Probes                          : []
    data:    Url Path Maps                   : []
    data:    GatewayIpConfigurationText      : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/gatewayIPConfigurations/ipConfig01",
            "subnet": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet"
            },
            "provisioningState": "Succeeded",
            "name": "ipConfig01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    SslCertificateText              : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/sslCertificates/cert01",
            "publicCertData": "MIIDHjCCAgagAwIBAgIQE8ZZuidyb59IscYNJPYTvDANBgkqhkiG9w0BAQsFADA4MTYwNAYDVQQDEy1NU0ZULUdXQUxMU1A0Lm5vcnRoYW1lcmljYS5jb3JwLm1pY3Jvc29mdC5jb20wHhcNMTYwNzI4MTUyNzIwWhcNMTcwNzI4MDAwMDAwWjA4MTYwNAYDVQQDEy1NU0ZULUdXQUxMU1A0Lm5vcnRoYW1lcmljYS5jb3JwLm1pY3Jvc29mdC5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDC+pkArA1y9C10AOPWISHEThbNqvuRA+MaVDFUOu15NB9M7+0PUx5pyGlbW+1MuXb9acia/KXF9WxpYUMbQt8t8p1S5HsV4oKGdEOpdR0d7dozyPrkNgBgYvqRzDQ6R5VuK/uLq9oWpjPkqNoQeYR1wr7f/SNsIA4YsaDqqAi62ET6cvg1wN/VRXbWyi9wLeon7g6fZiCrFZspTUiSyqrRQx7sO0e/bqV7nKgSWmaqo4jLoUAqJBBCUJryDaTNfkFO4VEdnsQLN+PSGO8HwSZPJOzG1V6+MynmaGCKaTJE2UCxtLIJQHBhmES+X/BoinrIsjNVxsKqWFMv/mV7M2GBAgMBAAGjJDAiMAsGA1UdDwQEAwIEMDATBgNVHSUEDDAKBggrBgEFBQcDATANBgkqhkiG9w0BAQsFAAOCAQEAa2XLrwQJwX2ZmVN0MR+/+jWTED134wgoIKw6Ni30ukF9U936FsuvFcEjPr4vBp82cjnz76BjLNhyw/MxAHP7tTaguxzHgUHP9X9fmtcsLEUD74/D5BPmnpl+4cJ/BZMdyzIsuyyPSsDxkVN/W70ykOVTJeAb1ycwfJCllgLgkZcLVgTcMMAJYSttfWn9e1dhTUIlTIYKzD669emFvdBHi+sdTT1HGrZenpkT5oK+H6/5wIV7/DW+C+pqvXCsK0XSeYWW7KuBk5MpD8829HeCvV0rBSf538nYLwUUVUUMNHuTp5QXzouAtHOWyvo00/xRi+aDeq0NfUvTv2iS2BS/Ow==",
            "provisioningState": "Succeeded",
            "name": "cert01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    FrontendIpConfigurationText     : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendIPConfigurations/frontendIp01",
            "privateIPAddress": "10.0.0.6",
            "privateIPAllocationMethod": "Dynamic",
            "subnet": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet"
            },
            "provisioningState": "Succeeded",
            "name": "frontendIp01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    FrontendPortText                : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendPorts/frontendPort01",
            "port": 443,
            "provisioningState": "Succeeded",
            "name": "frontendPort01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    BackendAddressPoolText          : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendAddressPools/pool01",
            "backendAddresses": [
                {
                    "ipAddress": "134.170.185.46"
                },
                {
                    "ipAddress": "134.170.188.221"
                },
                {
                    "ipAddress": "134.170.185.50"
                }
            ],
            "provisioningState": "Succeeded",
            "name": "pool01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    BackendHttpSettingsText         : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendHttpSettingsCollection/httpSettings01",
            "port": 80,
            "protocol": "Http",
            "cookieBasedAffinity": "Disabled",
            "requestTimeout": 30,
            "provisioningState": "Succeeded",
            "name": "httpSettings01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    HttpListenersText               : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/httpListeners/listener01",
            "frontendIPConfiguration": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendIPConfigurations/frontendIp01"
            },
            "frontendPort": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendPorts/frontendPort01"
            },
            "protocol": "Https",
            "sslCertificate": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/sslCertificates/cert01"
            },
            "requireServerNameIndication": false,
            "provisioningState": "Succeeded",
            "name": "listener01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    RequestRoutingRulesText         : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/requestRoutingRules/rule01",
            "ruleType": "Basic",
            "backendAddressPool": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendAddressPools/pool01"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendHttpSettingsCollection/httpSettings01"
            },
            "httpListener": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/httpListeners/listener01"
            },
            "provisioningState": "Succeeded",
            "name": "rule01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    SkuText                         : {
        "name": "Standard_Medium",
        "tier": "Standard",
        "capacity": 2
    }
    data:    ProbesText                      : []
    data:    UrlPathMapsText                 : []
    info:    network application-gateway create command OK

これで、リスナー、バックエンド プール、バックエンド http 設定、ルールの既定の設定を持つ基本的なアプリケーション ゲートウェイが作成されます。さらに、SSL オフロードの構成も行われます。プロビジョニングが成功したら、独自のデプロイに合わせて、これらの設定を変更することができます。前の手順で設定したバックエンド プールの IP アドレスに対して既に Web アプリケーションが定義されている場合、アプリケーション ゲートウェイをプロビジョニングして起動した後に、負荷分散が開始されます。

## 次のステップ

[カスタムの正常性プローブの作成](application-gateway-create-probe-portal.md)に関する記事を参照して、カスタムの正常性プローブを作成する方法を確認します。

[SSL オフロードの構成](application-gateway-ssl-arm.md)に関する記事を参照して、SSL オフロードを構成して Web サーバーでのコストのかかる SSL 暗号化解除を回避する方法を確認します。

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png

<!---HONumber=AcomDC_0831_2016-->