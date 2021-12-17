---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/12/2021
ms.author: cherylmc
ms.openlocfilehash: b676e4c801b447291288fdd07ff64177f1201e6c
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732690"
---
バージョンがクライアントのアーキテクチャと一致する限り、各 Windows クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。 サポートされているクライアント オペレーティング システムの一覧については、「[VPN Gateway に関する FAQ](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S)」のポイント対サイトに関するセクションを参照してください。

>[!NOTE]
>接続元の Windows クライアント コンピューターの管理者権限が必要です。
>

### <a name="install-the-configuration-files"></a>構成ファイルをインストールする

1. Windows コンピューターのアーキテクチャに対応する VPN クライアント構成ファイルを選択します。 64 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupAmd64" インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupX86" インストーラー パッケージを選択します。 
1. パッケージをダブルクリックしてインストールします。 SmartScreen ポップアップが表示された場合は、 **[詳細]** 、 **[実行]** の順にクリックしてください。

### <a name="verify-and-connect"></a>確認して接続する

1. クライアント コンピューターにクライアント証明書をインストール済みであることを確認します。 ネイティブ Azure 証明書の認証タイプを使用する場合、認証にはクライアント証明書が必要です。 クライアント証明書を表示するには、 **[ユーザー証明書の管理]** を開きます。 クライアント証明書は、**現在のユーザー\Personal\Certificates** にインストールされます。
1. 接続するには、 **[ネットワークの設定]** に移動し、 **[VPN]** をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。
