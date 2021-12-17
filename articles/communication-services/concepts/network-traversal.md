---
title: Azure Communication Services の概念に関するドキュメント - Network Traversal
titleSuffix: An Azure Communication Services Concept Document
description: Azure Communication Services Network Traversal の SDK と REST API の詳細について説明します。
author: rahulva
manager: shahen
services: azure-communication-services
ms.author: rahulva
ms.date: 09/20/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 02f52069cc9dea270cc8e6a34096af5bd80159d7
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894897"
---
#  <a name="network-traversal-concepts"></a>Network Traversal の概念   

リアルタイム リレーは、ピアツーピア (P2P) 接続の NAT (ネットワーク アドレス変換) トラバーサルの問題を解決します。 現在、インターネット上のほとんどのデバイスには、内部 LAN トラフィック (ホームまたは企業ネットワーク)、または外部から参照できるアドレス (ルーターまたは NAT ゲートウェイ) に使用される IP アドレスがあります。 インターネット上の 2 台のデバイスを接続するには、外部アドレスが必要ですが、通常は NAT ゲートウェイの内側にあるデバイスからは使用できません。 接続の問題を解決するには、次のプロトコルを使用します。

  STUN (Session Traversal Utilities for NAT) は、デバイスがインターネット上の外部 IP をやり取りできるようにするプロトコルを提供します。 クライアントが相互に認識できる場合は、接続をピアツーピアにすることができるため、通常は TURN サービスを介したリレーは必要ありません。 STUN サーバーのジョブは、デバイスの外部 IP に対する要求に応答します。
  
  TURN (Traversal Using Relays around NAT) は、相互に参照できるサーバーを介して 2 つのエンドポイント間でデータのリレーも行う、STUN プロトコルの拡張機能です。
        
## <a name="acs-network-traversal-overview"></a>ACS Network Traversal の概要   

WebRTC (Web Real-Time テクノロジ) を使用すると、Web ブラウザーは、中央にゲートウェイがなくても、デバイス間でオーディオ、ビデオ、データをストリーミングできます。 ここでの一般的な使用例として、音声、ビデオ、ブロードキャスト、画面共有があります。 インターネット上の 2 つのエンドポイントを接続するには、その外部 IP アドレスが必要です。 外部 IP は、通常、企業のファイアウォールの内側にあるデバイスでは使用できません。 STUN (Session Traversal Utilities for NAT) や TURN (Traversal Using Relays around NAT) のようなプロトコルを使用して、エンドポイントで通信できるようにします。

Azure Communication Service では、リアルタイム通信のシナリオで、高い帯域幅、低待機時間のピア間接続を提供します。 ACS Network Traversal Service では、NAT シナリオで使用する TURN サーバーをホストします。 Azure Real-Time Relay Service では、既存の STUN/TURN インフラストラクチャをサービスとしてのプラットフォーム (PaaS) Azure オファリングとして公開します。 サービスは、低レベルの STUN および TURN サービスを提供します。  その後、ユーザーは、リレーされるデータ量に比例して課金されます。 


## <a name="next-steps"></a>次のステップ:

* 認証の概要については、「[Azure Communication Services に対する認証](./authentication.md)」を参照してください。
* リレーの候補の取得については、「[アクセス トークンを作成して管理する](../quickstarts/relay-token.md)」を参照してください。
