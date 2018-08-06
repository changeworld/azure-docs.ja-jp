---
title: Azure Application Gateway URL ベースのコンテンツ ルーティングの概要
description: この記事では、Application Gateway URL ベースのコンテンツ ルーティング、UrlPathMap 構成、および PathBasedRouting 規則の概要について説明します。
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/23/2018
ms.author: victorh
ms.openlocfilehash: f706e267675dbb3d5465977157f40824efc96e48
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389244"
---
# <a name="azure-application-gatewaty-url-path-based-routing-overview"></a>Azure Application Gatewaty URL パス ベースのルーティングの概要

URL パス ベースのルーティングを使用すると、要求の URL パスに基づいてバックエンド サーバー プールにトラフィックをルーティングできます。 

1 つのシナリオとして、異なる種類のコンテンツの要求を、異なるバックエンド サーバー プールにルーティングします。

次の例では、Application Gateway は 3 つのバックエンド サーバー プール (VideoServerPool、ImageServerPool、DefaultServerPool など) からの contoso.com のトラフィックを処理します。

![imageURLroute](./media/url-route-overview/figure1.png)

http://contoso.com/video/ * の要求は、VideoServerPool にルーティングされ、http://contoso.com/images/* は ImageServerPool にルーティングされます。 一致するパス パターンがない場合は、DefaultServerPool が選択されます。

> [!IMPORTANT]
> 規則は、ポータルにおける表示順に処理されます。 基本リスナーを構成する前に、まずマルチサイト リスナーを構成することを強くお勧めします。  そうすることで、トラフィックが確実に適切なバックエンドにルーティングされます。 基本リスナーが先に表示されていて、なおかつ受信要求と一致した場合、そのリスナーによって要求が処理されます。

## <a name="urlpathmap-configuration-element"></a>UrlPathMap 構成要素

urlPathMap 要素は、パス パターンのバックエンド サーバー プールのマッピングへの指定に使用します。 次のコード例は、テンプレート ファイルの urlPathMap 要素のスニペットです。

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

> [!NOTE]
> PathPattern: この設定は、照合するパス パターンの一覧です。 それぞれ / で始まる必要があり、"*" が許可されるのは末尾の "/" の後だけです。 パス照合に提供する文字列には最初の ? または # の後にテキストを含めません (これらの文字は、ここでは許可されません)。

詳しくは、「 [Resource Manager template using URL-based routing (URL ベースのルーティングを使用した Resource Manager テンプレート)](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) 」をご覧ください。

## <a name="pathbasedrouting-rule"></a>PathBasedRouting ルール

タイプ PathBasedRouting の RequestRoutingRule は、リスナーを urlPathMap にバインドする際に使用します。 このリスナーで受け取られるすべての要求は、urlPathMap に指定されたポリシーに基づいてルーティングされます。
PathBasedRouting ルールのスニペット:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## <a name="next-steps"></a>次の手順

URL ベースのコンテンツ ルーティングについて理解したら、 [URL ベースのルーティングを使用した Application Gateway の作成](tutorial-url-route-powershell.md) に関するセクションに移動して、URL ルーティング ルールを使ってアプリケーション ゲートウェイを作成します。
