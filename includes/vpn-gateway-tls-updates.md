---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 383c4a53913333a3e25006980dd7533b9e243a4a
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236682"
---
2018 年 7 月 1 日以降、Azure VPN Gateway では TLS 1.0 および 1.1 のサポートが終了します。 VPN Gateway では、TLS 1.2 のみがサポートされるようになります。 TLS を使用する Windows 7 および Windows 8 ポイント対サイト クライアントに対する TLS サポートと接続を維持するには、次の更新プログラムをインストールすることをお勧めします。

•   [TLS の使用を可能にする Microsoft EAP 実装用の更新プログラム](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

•   [WinHTTP の既定の安全なプロトコルとして TLS 1.1 と TLS 1.2 を有効にする更新プログラム](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

次の TLS 用レガシ アルゴリズムも 2018 年 7 月 1 日に廃止される予定です。

* RC4 (Rivest Cipher 4)
* DES (データ暗号化アルゴリズム)
* 3DES (トリプル データ暗号化アルゴリズム)
* MD5 (Message Digest 5)
* SHA-1 (Secure Hash Algorithm 1)