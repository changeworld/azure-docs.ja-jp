---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "67180999"
---
>[!NOTE]
>2018 年 7 月 1 日以降、Azure VPN Gateway では TLS 1.0 および 1.1 のサポートが終了します。 VPN Gateway では、TLS 1.2 のみがサポートされるようになります。 サポートを維持するには、[TLS1.2 のサポートを有効にする更新プログラム](#tls1)に関するページを参照してください。

さらに、次の TLS 用レガシ アルゴリズムも 2018 年 7 月 1 日に廃止される予定です。

* RC4 (Rivest Cipher 4)
* DES (データ暗号化アルゴリズム)
* 3DES (トリプル データ暗号化アルゴリズム)
* MD5 (Message Digest 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-7-and-windows-81"></a><a name="tls1"></a>Windows 7 および Windows 8.1 で TLS 1.2 のサポートを有効にするにはどうすればよいですか。

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
