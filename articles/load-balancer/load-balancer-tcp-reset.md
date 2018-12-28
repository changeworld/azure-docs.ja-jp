---
title: アイドル時のロード バランサーの TCP リセット
titlesuffix: Azure Load Balancer
description: アイドル タイムアウト時に双方向 TCP RST パケットを使用するロード バランサー
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2018
ms.author: kumud
ms.openlocfilehash: 0b3e8fc72eb22a67c0672be19f60d4956d3377b7
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257298"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>アイドル時に TCP リセットを使用するロード バランサー (パブリック プレビュー)

[Standard Load Balancer](load-balancer-standard-overview.md) を使用して、各構成可能なアイドル タイムアウトに対して双方向 TCP リセット (TCP RST パケット) を使用することで、シナリオに合わせたさらに予測可能なアプリケーションの動作を作成できます。  ロード バランサーの既定の動作では、フローのアイドル タイムアウトに達したときに、警告なしでフローを削除します。

![ロード バランサー TCP リセット](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>アイドル タイムアウト時の TCP リセットを使用するロード バランサーは、現時点ではパブリック プレビューとして、限定された一連の[リージョン](#regions)で使用できます。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
 
この既定の動作は変更でき、受信 NAT 規則、負荷分散規則、[送信規則](https://aka.ms/lboutboundrules)に基づいて、アイドル タイムアウト時の TCP リセットの送信を有効にできます。  規則ごとに有効にすると、ロード バランサーは双方向 TCP リセット (TCP RST パケット) を、クライアントとサーバーの両方のエンドポイントに対して、一致するすべてのフローのアイドル タイムアウト時に送信します。

TCP RST パケットを受信するエンドポイントでは、対応するソケットをすぐに閉じます。 これは、接続のリリースが行われ、今後同じ TCP 接続での通信は失敗するという即時通知をエンドポイントに提供します。  必要に応じてソケットが閉じられ接続が再確立されるときに、最終的にTCP 接続のタイムアウトまで待つことなく、アプリケーションは接続を削除できます。

多くのシナリオでは、フローのアイドル タイムアウトを更新するために、TCP (またはアプリケーション層) キープアライブを送信する必要を減らすことができます。 

アイドル状態の期間が構成で許可されている期間を超えた場合、または TCP リセットが有効になっているアプリケーション望ましくない動作を示す場合、TCP 接続の存続性を監視するために、TCP キープアライブ (またはアプリケーション層キープアライブ) を引き続き使用することが必要になる場合があります。  さらに、キープアライブは、接続がパスのどこかでプロキシされている場合にも引き続き役立ちます (特にアプリケーション レイヤー キープアライブ)。  

エンド ツー エンド シナリオ全体を注意深く調べて、TCP リセットの有効化とアイドル タイムアウトの調整にメリットがあるかどうか、および目的のアプリケーション動作を確保するために追加の手順が必要かどうかを判断します。

## <a name="enabling-tcp-reset-on-idle-timeout"></a>アイドル タイムアウト時に TCP リセットを有効にする

API バージョン 2018-07-01 を使用して、規則ごとにアイドル タイムアウト時の双方向 TCP リセットの送信を有効にできます。

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="regions"></a>利用可能なリージョン。

このパラメーターは、以下のリージョンで現在有効です。  ここに記載されていないリージョンでは、このパラメーターには効果がありません。

| リージョン |
|---|
| 東南アジア |
| ブラジル南部 |
| カナダ中部 |
| 西ヨーロッパ |
| インド中部 |
| インド西部 |
| 西日本 |
| 韓国中部 |
| 韓国南部 |
| 英国北部 |
| 英国南部 2 |
| 米国東部 |
| 米国東部 2 |
| 英国北部 |
| 米国西部 |

プレビューが他のリージョンにも展開されると、この表は更新されます。  

## <a name="limitations"></a>制限事項

- [利用可能なリージョン](#regions)は限定されています。
- ポータルは、TCP リセットを構成または表示するためには使用できません。  代わりに、テンプレート、REST API、Az CLI 2.0、または PowerShell を使用します。

## <a name="next-steps"></a>次の手順

- [Standard Load Balancer](load-balancer-standard-overview.md) を参照します。
- [送信の規則](load-balancer-outbound-rules-overview.md)を参照します。
