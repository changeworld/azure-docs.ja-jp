---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/15/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b77128033cbe0f99023dbee8a5a15e06820399b1
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112255102"
---
>[!NOTE]
>2018 年 7 月 1 日以降、Azure VPN Gateway では TLS 1.0 および 1.1 のサポートが終了します。 VPN Gateway では、TLS 1.2 のみがサポートされるようになります。 サポートを維持するには、[TLS1.2 のサポートを有効にする更新プログラム](#tls1)に関するページを参照してください。

さらに、次の TLS 用レガシ アルゴリズムも 2018 年 7 月 1 日に廃止される予定です。

* RC4 (Rivest Cipher 4)
* DES (データ暗号化アルゴリズム)
* 3DES (トリプル データ暗号化アルゴリズム)
* MD5 (Message Digest 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-81"></a><a name="tls1"></a>Windows 8.1 で TLS 1.2 のサポートを有効にするにはどうすればよいですか。

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
