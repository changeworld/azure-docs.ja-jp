---
title: アイドル時のロード バランサーの TCP リセット
titleSuffix: Azure Load Balancer
description: この記事では、アイドル タイムアウト時に双方向 TCP RST パケットを使用する Azure Load Balancer について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: eac7dc3b7188131685ef630c0dc01d248e1d6a6a
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134778"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>アイドル時に TCP リセットを使用するロード バランサー

[Standard Load Balancer](load-balancer-standard-overview.md) を使用して、特定の規則のアイドル時の TCP リセットを有効にすることにより、シナリオのより予測可能なアプリケーション動作を作成します。 ロード バランサーの既定の動作では、フローのアイドル タイムアウトに達したときに、警告なしでフローを削除します。  この機能を有効にすると、ロード バランサーがアイドル タイムアウト時に双方向 TCP リセット (TCP RST パケット) を送信します。  これは、接続がタイムアウトしたため、使用できなくなったことをアプリケーション エンドポイントに通知します。  エンドポイントは、必要に応じて直ちに新しい接続を確立できます。

![ロード バランサー TCP リセット](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
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

すべてのリージョンで利用できます。

## <a name="limitations"></a>制限事項

- TCP RST が送信されるのは、ESTABLISHED 状態の TCP 接続時のみです。

## <a name="next-steps"></a>次のステップ

- [Standard Load Balancer](load-balancer-standard-overview.md) を参照します。
- [送信の規則](load-balancer-outbound-rules-overview.md)を参照します。
