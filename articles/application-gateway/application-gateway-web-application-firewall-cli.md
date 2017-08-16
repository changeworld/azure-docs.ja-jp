---
title: "Web アプリケーション ファイアウォールの構成 - Azure Application Gateway | Microsoft Docs"
description: "この記事では、既存または新規のアプリケーション ゲートウェイで Web アプリケーション ファイアウォールの使用を開始する方法について説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: ac6c629ceaf1a8036643f593ce3d7ef9ea096ef8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>Azure CLI を使用した、新規または既存の Application Gateway における Web アプリケーション ファイアウォールの構成

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

Web アプリケーション ファイアウォールが有効なアプリケーション ゲートウェイを作成する方法、または Web アプリケーション ファイアウォールを既存のアプリケーション ゲートウェイに追加する方法について説明します。

Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) は、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの一般的な Web ベースの攻撃から Web アプリケーションを保護します。

Azure Application Gateway はレイヤー 7 のロード バランサーです。 クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。 Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどの多くのアプリケーション配信コントローラー (ADC) 機能を備えています。 サポートされている機能の完全な一覧については、「[Application Gateway の概要](application-gateway-introduction.md)」を参照してください。

以下の記事では、[Web アプリケーション ファイアウォールを既存のアプリケーション ゲートウェイに追加する](#add-web-application-firewall-to-an-existing-application-gateway)方法と [Web アプリケーション ファイアウォールを使用するアプリケーション ゲートウェイを作成する](#create-an-application-gateway-with-web-application-firewall)方法について説明します。

![シナリオのイメージ][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>前提条件: Azure CLI 2.0 のインストール

この記事の手順を実行するには、[Mac、Linux、Windows 用の Azure コマンドライン インターフェイス (Azure CLI) をインストール](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)する必要があります。

## <a name="waf-configuration-differences"></a>WAF の構成上の相違点

[Azure CLI を使用した Application Gateway の作成](application-gateway-create-gateway-cli.md)に関するページをお読みであれば、アプリケーション ゲートウェイを作成するときに構成する SKU の設定はご存じのとおりです。 WAF には、アプリケーション ゲートウェイで SKU を構成するときに定義する追加の設定があります。 アプリケーション ゲートウェイ自体に加える変更はありません。

| **設定** | **詳細**
|---|---|
|**SKU** |WAF が追加されていない通常のアプリケーション ゲートウェイでは、**Standard\_Small**、**Standard\_Medium**、および **Standard\_Large** の各サイズがサポートされています。 WAF を導入すると、2 つの SKU、**WAF\_Medium** と **WAF\_Large** が追加されます。 小規模なアプリケーション ゲートウェイでは、WAF はサポートされていません。|
|**モード** | この設定は WAF のモードです。 使用できる値は **Detection** と **Prevention**です。 WAF を検出モードで設定すると、すべての脅威がログ ファイルに記録されます。 防止モードでは、検出モードと同様にイベントがログに記録されますが、攻撃者はアプリケーション ゲートウェイから "403 許可されていません" 応答を受信します。|

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>既存のアプリケーション ゲートウェイに Web アプリケーション ファイアウォールを追加する

次のコマンドは、既存の標準的なアプリケーション ゲートウェイを WAF 対応のアプリケーション ゲートウェイに変更します。

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

このコマンドを実行すると、Web アプリケーション ファイアウォールによってアプリケーション ゲートウェイが更新されます。 [Application Gateway の診断](application-gateway-diagnostics.md)に関する記事を参照して、アプリケーション ゲートウェイのログを表示する方法について理解してください。 WAF のセキュリティの性質により、ログを定期的に確認して Web アプリケーションのセキュリティ状況を把握する必要があります。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Web アプリケーション ファイアウォールのある Application Gateway を作成する

次のコマンドは、Web アプリケーション ファイアウォールのある Application Gateway を作成します。

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> 基本的な Web アプリケーション ファイアウォールの構成で作成されたアプリケーション ゲートウェイは、CRS 3.0 の保護で構成されています。

## <a name="get-application-gateway-dns-name"></a>アプリケーション ゲートウェイの DNS 名の取得

ゲートウェイを作成したら、次は通信用にフロントエンドを構成します。 パブリック IP を使用する場合、アプリケーション ゲートウェイには、動的に割り当てられたフレンドリではない DNS 名が必要です。 エンド ユーザーがアプリケーション ゲートウェイを確実に見つけられるように、CNAME レコードを使用して、アプリケーション ゲートウェイのパブリック エンドポイントを参照できます。 次に、[Azure でのカスタム ドメイン名を構成します](../cloud-services/cloud-services-custom-domain-name-portal.md)。 CNAME レコードを構成するには、アプリケーション ゲートウェイに接続されている PublicIPAddress 要素を使用して、アプリケーション ゲートウェイの詳細とそれに関連付けられている IP/DNS 名を取得します。 アプリケーション ゲートウェイの DNS 名を使用して、2 つの Web アプリケーションがこの DNS 名を指すように CNAME レコードを作成する必要があります。 アプリケーション ゲートウェイの再起動時に VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="next-steps"></a>次のステップ

「[Customize web application firewall rules through the Azure CLI 2.0 (Azure CLI 2.0 を通じて Web アプリケーション ファイアウォール ルールをカスタマイズする)](application-gateway-customize-waf-rules-cli.md)」を参照して、WAF ルールをカスタマイズする方法を学習します。

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png

