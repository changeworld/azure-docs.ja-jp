---
title: App Service Environment の管理アドレス - Azure
description: App Service Environment を管理するために使用される管理アドレスを一覧表示します。
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7fb39886b19a2229188821eb39d4fb8a5928bb43
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276691"
---
# <a name="app-service-environment-management-addresses"></a>App Service Environment の管理アドレス

App Service Environment (ASE) は、ユーザーの Azure Virtual Network (VNet) 内のサブネットに Azure App Service をデプロイしたものです。  ASE には、Azure App Service によって使用される管理プランからアクセスできる必要があります。  この ASE 管理トラフィックでは、ユーザーによって制御されるネットワークを走査します。 このトラフィックがブロックまたは誤ってルーティングされていると、ASE は中断されます。 ASE ネットワークの依存関係の読み取りの詳細については、「[App Service Environment のネットワークの考慮事項][networking]」を参照してください。 最初に参照できる ASE に関する一般情報については、「[App Service 環境の概要][intro]」を参照してください。

このドキュメントは、ASE に対する管理トラフィックの App Service ソース アドレスの一覧を示しており、2 つの重要な目的のために役立ちます。  

1. こうしたアドレスを使用して、着信トラフィックをロックするネットワーク セキュリティ グループを作成できます。  
2. こうしたアドレスを使用してルートを作成し、強制トンネリングのデプロイをサポートできます。 送信トラフィックがオンプレミスで送信される環境内で運用するために ASE を構成する方法の詳細については、「[強制トンネリングを使用した App Service Environment の構成][forcedtunnel]」を参照してください。

すべての ASE に、管理トラフィックの送信先パブリック VIP があります。 こうしたアドレスからの着信管理トラフィックは、ASE のパブリック VIP のポート 454 および 455 に送信されます。  

## <a name="list-of-management-addresses"></a>管理アドレスの一覧 ##

| リージョン | アドレス |
|--------|-----------|
| すべてのパブリック リージョン | 70.37.57.58, 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246 |
| Microsoft Azure Government (Fairfax または MAG) | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="get-your-management-addresses-from-api"></a>API から管理アドレスを取得する ##

次の API 呼び出しで、ご自身の ASE に一致する管理アドレスを一覧表示できます。

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

API によって、ご自身の ASE に対するすべての受信アドレスが含まれる JSON ドキュメントが返されます。 アドレスの一覧には、管理アドレス、ご自身の ASE によって使用される VIP、および ASE サブネットのアドレス範囲自体が含まれています。  

[armclient](https://github.com/projectkudu/ARMClient) を使用して API を呼び出すには、次のコマンドを、サブスクリプション ID、リソース グループ、および ASE 名を置き換えて使用します。  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
