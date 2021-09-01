---
title: 'P2S VPN クライアント構成ファイルを作成してインストールする: 証明書認証'
titleSuffix: Azure VPN Gateway
description: Windows、Linux (strongSwan)、macOS 用の VPN クライアント構成ファイルを生成してインストールする方法について説明します。 この記事は、証明書認証を使用する VPN Gateway P2S 構成に適用されます。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/15/2021
ms.author: cherylmc
ms.openlocfilehash: efa2c4c120ab54e27126d1f40c433cdc1b66a85e
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114288545"
---
# <a name="generate-and-install-vpn-client-configuration-files-for-p2s-certificate-authentication"></a>P2S 証明書認証用の VPN クライアント構成ファイルを生成してインストールする

ポイント対サイトと証明書認証を使用して Azure VNet に接続する場合は、接続元のオペレーティング システムにネイティブにインストールされている VPN クライアントを使用します。 VPN クライアントに必要なすべての構成設定は、VPN クライアント構成 ZIP ファイルに含まれています。 ZIP ファイル内の設定を使用することで、Windows、Mac IKEv2 VPN、または Linux 用の VPN クライアントを簡単に構成できます。

生成する VPN クライアント構成ファイルは、仮想ネットワークの P2S VPN ゲートウェイ構成に固有です。 ファイルを生成した後に、ポイント対サイト VPN 構成に変更があった場合は (VPN プロトコルの種類や認証の種類の変更など)、新しい VPN クライアント構成ファイルを生成し、接続するすべての VPN クライアントに新しい構成を適用する必要があります。

* ポイント対サイト接続について詳しくは、「[ポイント対サイト VPN について](point-to-site-about.md)」を参照してください。
* OpenVPN の取扱説明については、[P2S 向け OpenVPN を構成する](vpn-gateway-howto-openvpn.md)方法に関するページと [OpenVPN クライアントを構成する](vpn-gateway-howto-openvpn-clients.md)方法に関するページを参照してください。

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>VPN クライアント構成ファイルの生成

PowerShell または Azure Portal を使用してクライアント構成ファイルを生成することができます。 どちらの方法でも、同じ zip ファイルが返されます。 そのファイルを解凍して、次のフォルダーを表示します。

* **WindowsAmd64** および **WindowsX86**。Windows の 32 ビットと 64 ビットのインストーラー パッケージがそれぞれに含まれています。 **WindowsAmd64** インストーラー パッケージは、Amd だけでなく、サポートされている 64 ビットの Windows クライアントを対象としています。
* **Generic**。これには、独自の VPN クライアント構成の作成に使用される全般的な情報が含まれています。 Generic フォルダーが提供されるのは、IKEv2 または SSTP+IKEv2 がゲートウェイ上で構成された場合です。 構成されているのが SSTP のみの場合、Generic フォルダーは存在しません。

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Azure Portal を使用してルールを生成する

1. Azure Portal で、接続する仮想ネットワークの仮想ネットワーク ゲートウェイに移動します。
1. 仮想ネットワーク ゲートウェイ ページで、 **[ポイント対サイトの構成]** を選択して、[ポイント対サイトの構成] ページを開きます。
1. [ポイント対サイトの構成] ページの上部で **[VPN クライアントのダウンロード]** を選択します。 これにより、VPN クライアント ソフトウェアがダウンロードされるのではなく、VPN クライアントの構成に使用される構成パッケージが生成されます。 クライアント構成パッケージが生成されるまでに数分かかります。 この期間中は、パケットが生成されるまで、何も表示されない場合があります。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="VPN クライアント構成のダウンロード。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/download-client.png":::
1. 構成パッケージが生成されると、クライアント構成 ZIP ファイルが使用可能であることがブラウザーに示されます。 ファイルにはゲートウェイと同じ名前が付けられています。 そのファイルを解凍して、フォルダーを表示します。

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>PowerShell を使用してファイルを生成する

1. VPN クライアント構成ファイルを生成する際、"-AuthenticationMethod" の値は "EapTls" です。 次のコマンドを使用して、VPN クライアント構成ファイルを生成します。

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. ブラウザーに URL をコピーして、zip ファイルをダウンロードし、ファイルを解凍してフォルダーを表示します。

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-macos"></a><a name="installmac"></a>Mac (macOS)

Azure に接続するには、ネイティブ IKEv2 VPN クライアントを手動で構成する必要があります。 Azure には、*mobileconfig* ファイルはありません。 **Generic** フォルダーには、構成に必要なすべての情報が含まれています。 

ダウンロードに、Generic フォルダーが表示されない場合は、IKEv2 がトンネルの種類として選択されていない可能性があります。 VPN Gateway Basic SKU は IKEv2 をサポートしていないことに注意してください。 VPN ゲートウェイで、SKU が Basic ではないか確認します。 次に、IKEv2 を選択し、もう一度 zip ファイルを生成して、Generic フォルダーを取得します。

Generic フォルダーには、次のファイルが含まれています。

* **VpnSettings.xml**。サーバー アドレスやトンネルの種類など、重要な設定が含まれています。 
* **VpnServerRoot.cer**。P2S 接続の設定中に Azure VPN ゲートウェイを検証するために必要なルート証明書が含まれています。

証明書認証用に Mac 上でネイティブ VPN クライアントを構成するには、次の手順を実行してください。 これらの手順は、Azure に接続する Mac ごとに完了する必要があります。

### <a name="import-root-certificate-file"></a>ルート証明書ファイルをインポートする

1. Mac にルート証明書ファイルをコピーします。 証明書をダブルクリックする 証明書が自動的にインストールされるか、 **[証明書の追加]** ページが表示されます。
1. **[証明書の追加]** ページで、ドロップダウンから **[ログイン]** を選択します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/login.png" alt-text="[証明書の追加] ページのスクリーンショット。[ログイン] が選択されています。":::
1. **[追加]** をクリックしてファイルをインポートします。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add.png" alt-text="[証明書の追加] ページのスクリーンショット。[追加] が選択されています。":::

### <a name="verify-certificate-install"></a>証明書のインストールを確認する

クライアント証明書とルート証明書の両方がインストールされていることを確認します。 クライアント証明書は認証に使用され、必須です。 クライアント証明書のインストール方法については、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページをご覧ください。

1. **キーチェーン アクセス** アプリケーションを開きます。
1. **[証明書]** タブに移動します。
1. クライアント証明書とルート証明書の両方がインストールされていることを確認します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/keychain.png" alt-text="キーチェーン アクセスのスクリーンショット。証明書がインストールされています。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/keychain.png":::
  
### <a name="create-vpn-client-profile"></a>VPN クライアント プロファイルを作成する

1. **[システム環境設定] -> [ネットワーク]** に移動します。 [ネットワーク] ページで **'+'** を選択して、Azure 仮想ネットワークへの P2S 接続用に、新しい VPN クライアント接続プロファイルを作成します。
1. **[インターフェイス]** では、ドロップダウンから **[VPN]** を選択します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-vpn.png" alt-text="インターフェイスを選択するオプションが表示された [ネットワーク] ウィンドウのスクリーンショット。[VPN] が選択されています。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-vpn.png":::

1. **[VPN の種類]** では、ドロップダウンから **[IKEv2]** を選択します。 **[サービス名]** フィールドに、プロファイルのフレンドリ名を指定します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-type.png" alt-text="[ネットワーク] ウィンドウのスクリーンショット。インターフェイスの選択、VPN の種類の選択、サービス名の入力オプションが表示されています。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/vpn-type.png":::

1. **[作成]** を選択して VPN クライアント接続プロファイルを作成します。
1. テキスト エディターを使用して **Generic** フォルダーの **VpnSettings.xml** ファイルを開き、**VpnServer** タグの値をコピーします。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server-tag.png" alt-text="VpnSettings.xml ファイルが開かれているスクリーンショット。VpnServer タグが強調表示されています。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/server-tag.png":::

1. **VpnServer** タグの値を、プロファイルの **[サーバー アドレス]** と **[リモート ID]** の両方のフィールドに貼り付けます。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/paste-value.png" alt-text="[ネットワーク] ウィンドウのスクリーンショット。値が貼り付けられています。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/paste-value.png":::

1. 認証設定を構成します。 2 種類の手順があります。 OS のバージョンに対応する手順を選択します。

   **Catalina:** 

     * **[認証設定]** で **[なし]** を選択します。 
     * **[証明書]** を選択し、 **[選択]** をクリックして、前にインストールした適切なクライアント証明書を選択します。 次に、 **[OK]** をクリックします
   
        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="スクリーンショットには [ネットワーク] ウィンドウが表示され、[認証設定] に [なし] が選択され、[証明書] が選択されています。":::

   **Big Sur:**

      * **[認証設定]** をクリックし、 **[証明書]** を選択します。 

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-certificate.png" alt-text="[認証設定] のスクリーンショット。[証明書] が選択されています。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/authentication-certificate.png":::

      * **[選択]** をクリックして、 **[Choose An Identity]\(ID の選択\)** ページを開きます。 **[Choose An Identity]\(ID の選択\)** ページでは、選択できる証明書の一覧が表示されます。 使用する証明書が不明な場合は、 **[Show Certificate]\(証明書の表示\)** をクリックして、各証明書の詳細を表示できます。

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/show-certificate.png" alt-text="証明書のプロパティのスクリーンショット。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/show-certificate.png":::
      * 適切な証明書を選択し、 **[続ける]** を選択します。

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-identity.png" alt-text="[Choose an Identity]\(ID の選択\) のスクリーンショット。証明書を選択できます。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/choose-identity.png":::
   
      * **[認証設定]** ページで適切な証明書が表示されていることを確認し、 **[OK]** をクリックします。

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="[Choose An Identity]\(ID の選択\) ダイアログ ボックスのスクリーンショット。適切な証明書を選択できます。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/certificate.png":::

1. Catalina と Big Sur の両方について、 **[ローカル ID]** フィールドに証明書の名前を指定します。 この例では `P2SChildCert` です。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/local-id.png" alt-text="ローカル ID の値を示すスクリーンショット。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/local-id.png":::
1. **[適用]** を選択してすべての変更を保存します。 
1. **[接続]** を選択して、Azure 仮想ネットワークへの P2S 接続を開始します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-connect.png" alt-text="[接続] ボタンを示すスクリーンショット。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-connect.png":::

1. 接続が確立されると、状態が **[接続済み]** と表示され、VPN クライアント アドレス プールから取得した IP アドレスが表示されます。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/connected.png" alt-text="接続済みを示すスクリーンショット。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/connected.png":::

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>strongSwan のインストール

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>証明書の生成

まだ証明書を生成していない場合は、次の手順を使用します。

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>インストールと構成

次の手順は、Ubuntu 18.0.4 上で作成されました。 Ubuntu 16.0.10 は、strongSwan GUI をサポートしていません。 Ubuntu 16.0.10 を使う場合は、[コマンド ライン](#linuxinstallcli)を使う必要があります。 Linux および strongSwan のバージョンによっては、次に示す例が実際に表示される画面と一致しない可能性があります。

1. **[ターミナル]** を起動し、例のコマンドを実行して **strongSwan** とその Network Manager をインストールします。

   ```
   sudo apt install network-manager-strongswan
   ```
1. **[Settings]\(設定\)** 、 **[Network]\(ネットワーク\)** の順に選択します。 **+** ボタンを選択して、新しい接続を作成します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="[ネットワーク接続] ページを示すスクリーンショット。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/edit-connections.png":::

1. メニューから **[IPsec/IKEv2 (strongswan)]** を選び、ダブルクリックします。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="[VPN の追加] ページを示すスクリーンショット。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/add-connection.png":::

1. **[VPN の追加]** ページで、VPN 接続の名前を追加します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="接続の種類の選択を示すスクリーンショット。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/choose-type.png":::
1. ダウンロード クライアント構成ファイルに含まれる **Generic** フォルダーから **VpnSettings.xml** ファイルを開きます。 **VpnServer** というタグを検索して、"azuregateway" で始まり ".cloudapp.net" で終わる名前をコピーします。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="データのコピーを示すスクリーンショット。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/vpn-server.png":::
1. この名前を、 **[ゲートウェイ]** セクションの、新しい VPN 接続の **[アドレス]** フィールドに貼り付けます。 次に、 **[証明書]** フィールドの最後のフォルダー アイコンを選択して、**Generic** フォルダーに移動し、**VpnServerRoot** ファイルを選択します。
1. 接続の **[クライアント]** セクションの **[認証]** で、 **[Certificate/private key]\(証明書/秘密キー\)** を選びます。 **[証明書]** と **[秘密キー]** で、前に作成した証明書および秘密キーを選びます。 **[オプション]** で、 **[Request an inner IP address]\(内部 IP アドレスを要求する\)** をオンにします。 その後、 **[追加]** を選択します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="[内部 IP アドレスを要求する] を示すスクリーンショット。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/ip-request.png":::

1. 接続を **[On]\(オン\)** にします。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="コピーを示すスクリーンショット。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/turn-on.png":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>strongSwan のインストール

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>証明書の生成

まだ証明書を生成していない場合は、次の手順を使用します。

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>インストールと構成

1. Azure portal から VPNClient パッケージをダウンロードします。
1. ファイルを展開します。
1. **VpnServerRoot.cer** を **Generic** フォルダーから **/etc/ipsec.d/cacerts** にコピーするか移動します。
1. **cp client.p12** を **/etc/ipsec.d/private/** にコピーするか移動します。 このファイルは、VPN ゲートウェイのクライアント証明書です。
1. **VpnSettings.xml** ファイルを開き、`<VpnServer>` の値をコピーします。 次の手順でこの値を使用します。
1. 下のサンプルの値を調整し、 **/etc/ipsec.conf** 構成にこのサンプルを追加します。
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
1. 次の値を **/etc/ipsec.secrets** に追加します。

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. 次のコマンドを実行します。

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>次のステップ

作業元の記事に戻り、P2S の構成を完了します。

* [PowerShell の構成手順](vpn-gateway-howto-point-to-site-rm-ps.md)。
* [Azure portal の構成手順](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。
