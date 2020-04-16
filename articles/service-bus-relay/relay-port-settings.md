---
title: Azure リレー ポート設定 | Microsoft Docs
description: この記事には、Azure Relay のポート値に必要な構成を示す表が含まれています。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 532e3c297c42ea16b1f462a01699fc2fd71c6cce
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529106"
---
# <a name="azure-relay-port-settings"></a>Azure リレー ポート設定

Azure リレーのポート値に必要な構成を次の表に示します。

## <a name="hybrid-connections"></a>ハイブリッド接続

ハイブリッド接続は、基盤となるトランスポート構造として TLS を使用するポート 443 の WebSocket を採用し、**HTTPS** のみを使用します。 

## <a name="wcf-relays"></a>WCF リレー
  
|バインド|トランスポート セキュリティ|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (クライアント)|はい|HTTPS| 
|" |いいえ|HTTP|  
|[BasicHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (サービス)|接続前/接続後|9351/HTTP|  
|[NetEventRelayBinding クラス](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (クライアント)|はい|9351/HTTPS|  
|" |いいえ|9350/HTTP|  
|[NetEventRelayBinding クラス](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (サービス)|接続前/接続後|9351/HTTP|  
|[NetTcpRelayBinding クラス](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (クライアント/サービス)|接続前/接続後|5671/9352/HTTP (ハイブリッドを使用している場合は 9352/9353)|  
|[NetOnewayRelayBinding クラス](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (クライアント)|はい|9351/HTTPS|  
|" |いいえ|9350/HTTP|  
|[NetOnewayRelayBinding クラス](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (サービス)|接続前/接続後|9351/HTTP|  
|[WebHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (クライアント)|はい|HTTPS|  
|" |いいえ|HTTP|  
|[WebHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (サービス)|接続前/接続後|9351/HTTP|  
|[WS2007HttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (クライアント)|はい|HTTPS|  
|" |いいえ|HTTP|  
|[WS2007HttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (サービス)|接続前/接続後|9351/HTTP|

## <a name="next-steps"></a>次のステップ
Azure リレーの詳細については、次のリンク先を参照してください。
* [What is Azure Relay? (Azure Relay とは)](relay-what-is-it.md)
* [Relay に関する FAQ](relay-faq.md)