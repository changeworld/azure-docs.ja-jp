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
ms.openlocfilehash: 9a17f34333503436d3da340670abdde154e45ef6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38727532"
---
2018 年 7 月 1 日以降、Azure VPN Gateway では TLS 1.0 および 1.1 のサポートが終了します。 VPN Gateway では、TLS 1.2 のみがサポートされるようになります。 TLS を使用する Windows 7 および Windows 8 ポイント対サイト クライアントに対する TLS サポートと接続を維持するには、次の更新プログラムをインストールすることをお勧めします。

•   [TLS の使用を可能にする Microsoft EAP 実装用の更新プログラム](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

•   [WinHTTP の既定の安全なプロトコルとして TLS 1.1 と TLS 1.2 を有効にする更新プログラム](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

次の TLS 用レガシ アルゴリズムも 2018 年 7 月 1 日に廃止される予定です。

* RC4 (Rivest Cipher 4)
* DES (データ暗号化アルゴリズム)
* 3DES (トリプル データ暗号化アルゴリズム)
* MD5 (Message Digest 5)

### <a name="tls1"></a>どのように TLS 1.2 を Windows 7 および Windows 8.1 で有効化しますか？

1. コマンドプロンプトを右クリックし、管理者として実行を選択して、コマンドプロンプトを管理者権限で開きます。
2. 以下のコマンドをコマンドプロンプトで実行します。

  ```
  reg add HKLM\SYSTEM\CurrentControlSet\Services\RasMan\PPP\EAP\13 /v TlsVersion /t REG_DWORD /d 0xfc0
  reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DefaultSecureProtocols /t REG_DWORD /d 0xaa0
  if %PROCESSOR_ARCHITECTURE% EQU AMD64 reg add "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DefaultSecureProtocols /t REG_DWORD /d 0xaa0
  ```

3. 以下の更新プログラムをインストールします。

  * [KB3140245](https://www.catalog.update.microsoft.com/search.aspx?q=kb3140245)
  * [KB2977292](https://www.microsoft.com/en-us/download/details.aspx?id=44342)
  
4. コンピューターを再起動します。
5. VPN へ接続します。
