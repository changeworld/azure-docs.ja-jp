---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 482241deb1081ac8a5265a076eabbdc3fb6d659e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181103"
---
システム ルートを使用するとデプロイのトラフィックが自動的に促進されますが、仮想アプライアンスを通過するパケットのルーティングを自分で制御したい場合もあります。 特定のサブネット宛のパケットが代わりに仮想アプライアンスを通るように次ホップを指定するユーザー定義のルートを作成し、仮想アプライアンスとして実行する VM に対して IP 転送を有効にすることにより、これを実現できます。

仮想アプライアンスを使用できるのは次のような場合です。

* 侵入検出システム (ID) を使用したトラフィックの監視
* ファイアウォールを使用したトラフィックの制御

UDR および IP 転送の詳細については、「 [ユーザー定義のルートと IP 転送](../articles/virtual-network/virtual-networks-udr-overview.md)」を参照してください。

