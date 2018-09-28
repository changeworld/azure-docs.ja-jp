---
title: Azure VPN Gateway 用に OpenVPN クライアントを構成する方法 | Microsoft Docs
description: Azure VPN Gateway 用に OpenVPN クライアントを構成する手順
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 141f29ea1c7ebdd8208dda6bc39a60f7873dab7d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977844"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>Azure VPN Gateway 用に OpenVPN クライアントを構成する (プレビュー)

この記事では、OpenVPN クライアントの構成方法について説明します。

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

## <a name="before-you-begin"></a>開始する前に

VPN ゲートウェイ用に OpenVPN を構成する手順を完了していることを確認します。 詳細については、[Azure VPN Gateway 用に OpenVPN を構成する方法](vpn-gateway-howto-openvpn.md)に関するページを参照してください。

## <a name="windows"></a>Windows クライアント

1. 公式の [OpenVPN Web サイト](https://openvpn.net/index.php/open-source/downloads.html)から OpenVPN クライアントをダウンロードしてインストールします。
2. ゲートウェイの VPN プロファイルをダウンロードします。 この操作は、Azure portal の [ポイント対サイト構成] タブまたは PowerShell の 'New-AzureRmVpnClientConfiguration' で実行できます。
3. プロファイルを展開します。 次にメモ帳で OpenVPN フォルダーの vpnconfig.ovpn 構成ファイルを開きます。
4. P2S クライアント証明書セクションに、base64 の P2S クライアント証明書の公開キーを指定します。 PEM 形式の証明書の場合、.cer ファイルを開き、証明書ヘッダー間にある base64 キーを上書きしてコピーします。 証明書をエクスポートしてエンコードされた公開キーを取得する方法については、こちらを参照してください。
5. 秘密キー セクションに、base64 の P2S クライアント証明書の秘密キーを指定します。 秘密キーの抽出方法については、[キーのエクスポート](vpn-gateway-certificates-point-to-site.md#clientexport)に関するページを参照してください。
6. その他のフィールドは変更しないでください。 クライアント入力に入力された構成を使用して VPN に接続します。
7. vpnconfig.ovpn ファイルを C:\Program Files\OpenVPN\config フォルダーにコピーします。
8. システム トレイの OpenVPN アイコンを右クリックし、[接続] をクリックします。

## <a name="mac"></a>Mac クライアント

1. [TunnelBlik](https://tunnelblick.net/downloads.html) などの OpenVPN クライアントをダウンロードしてインストールします。 
2. ゲートウェイの VPN プロファイルをダウンロードします。 この操作は、Azure portal の [ポイント対サイト構成] タブから、または PowerShell の 'New-AzureRmVpnClientConfiguration' を使用して実行できます。
3. プロファイルを展開します。 メモ帳で OpenVPN フォルダーの vpnconfig.ovpn 構成ファイルを開きます。
4. P2S クライアント証明書セクションに、base64 の P2S クライアント証明書の公開キーを指定します。 PEM 形式の証明書の場合、.cer ファイルを開き、証明書ヘッダー間にある base64 キーを上書きしてコピーします。 エンコードされた公開キーを取得する証明書をエクスポートする方法については、[公開キーのエクスポート](vpn-gateway-certificates-point-to-site.md#cer)に関するページを参照してください。
5. 秘密キー セクションに、base64 の P2S クライアント証明書の秘密キーを指定します。 秘密キーの抽出方法については、[秘密キーのエクスポート](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/)に関するページを参照してください。
6. その他のフィールドは変更しないでください。 クライアント入力に入力された構成を使用して VPN に接続します。
7. プロファイル ファイルをダブルクリックして tunnelblik にプロファイルを作成します。
8. アプリケーション フォルダーから Tunnelblik を起動します。
9. システム トレイの Tunnelblik アイコンをクリックし、[接続] を選択します。

## <a name="linux"></a>Linux クライアント

1. 新しいターミナル セッションを開きます。 新しいセッションを開くには、Ctrl キーと Alt キーを押しながら t キーを押します
2. 次のコマンドを入力して、必要なコンポーネントをインストールします。

  ```
  sudo apt-get install openvpn
  sudo apt-get -y install network-manager-openvpn
  sudo service network-manager restart
  ```
3. ゲートウェイの VPN プロファイルをダウンロードします。 この操作は、Azure portal の [ポイント対サイト構成] タブから、または PowerShell の 'New-AzureRmVpnClientConfiguration' を使用して実行できます。
4. 秘密キー セクションに、base64 の P2S クライアント証明書の秘密キーを指定します。 秘密キーの抽出方法については、[秘密キーのエクスポート](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/)に関するページを参照してください。
5. コマンド ラインを使用して接続するには、以下を入力します。
  
  ```
  Sudo openvpn –config <name and path of your VPN profile file>
  ```
5. GUI を使用して接続するには、システム設定に移動します。
6. **+** をクリックして新しい VPN 接続を追加します。
7. **[VPN の追加]** で **[ファイルからインポート]** を選択します。
8. プロファイル ファイルを参照し、ダブルクリックするか **[開く]** を選択します。
9. **[VPN の追加]** ウィンドウで **[追加]** をクリックします。
  
  ![ファイルからインポート](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
10. 接続するには、**[ネットワーク設定]** ページで VPN を**オン**にするか、システム トレイのネットワーク アイコンを選択します。

## <a name="next-steps"></a>次の手順

VPN クライアントが別の vnet (実稼働環境) のリソースにアクセスできるようにするには、[VNet 間](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)の記事に記載されている手順に従って VNet 間の接続を設定します。 ゲートウェイと接続で BGP を有効にしてください。そうしないと、トラフィックは流れません。
