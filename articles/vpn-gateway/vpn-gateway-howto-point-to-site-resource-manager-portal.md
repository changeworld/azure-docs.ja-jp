---
title: 'P2S VPN と証明書認証を使用して VNet に接続する: ポータル'
titleSuffix: Azure VPN Gateway
description: VPN Gateway ポイント対サイト接続と自己署名または CA 発行の証明書を使用して、Windows、macOS、Linux クライアントを VNet に安全に接続する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/19/2021
ms.author: cherylmc
ms.openlocfilehash: 70bb2f54d6e56574a5cbbb98d1c0c467f4715f3d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818150"
---
# <a name="configure-a-point-to-site-vpn-connection-using-azure-certificate-authentication-azure-portal"></a>Azure 証明書認証を使用したポイント対サイト VPN 接続を構成する: Azure portal

この記事では、Windows、Linux、または macOS が実行されている個々のクライアントを Azure VNet に安全に接続する方法を紹介します。 ポイント対サイト VPN 接続は、自宅や会議室でのテレワークなど、リモートの場所から VNet に接続する場合に便利です。 VNet への接続を必要とするクライアントがごく少ない場合は、サイト対サイト VPN の代わりに P2S を使用することもできます。 ポイント対サイト接続に、VPN デバイスや公開 IP アドレスは必要ありません。 P2S により、SSTP (Secure Socket トンネリング プロトコル) または IKEv2 経由の VPN 接続が作成されます。 ポイント対サイト VPN について詳しくは、「[ポイント対サイト VPN について](point-to-site-about.md)」を参照してください。

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="コンピューターから Azure VNet への接続 - ポイント対サイト接続の図":::

ポイント対サイト VPN の詳細については、「[ポイント対サイト VPN について](point-to-site-about.md)」を参照してください。 Azure PowerShell を使用してこの構成を作成するには、[Azure PowerShell を使用したポイント対サイト VPN の構成](vpn-gateway-howto-point-to-site-rm-ps.md)に関する記事を参照してください。

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

### <a name="example-values"></a><a name="example"></a>値の例

次の値を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。

**VNet**

* **VNet 名:** VNet1
* **[アドレス空間]:** 10.1.0.0/16<br>この例では、1 つのアドレス空間のみを使用します。 VNet には、複数のアドレス空間を使用することができます。
* **サブネット名:** FrontEnd
* **サブネットのアドレス範囲:** 10.1.0.0/24
* **サブスクリプション:** サブスクリプションが複数ある場合は、適切なサブスクリプションを使用していることを確認します。
* **[リソース グループ]:** TestRG1
* **[場所]:** 米国東部

**仮想ネットワーク ゲートウェイ**

* **仮想ネットワーク ゲートウェイ名:** VNet1GW
* **ゲートウェイの種類:** VPN
* **VPN の種類:** ルート ベース
* **SKU:** VpnGw2
* **世代:** Generation2
* **ゲートウェイ サブネットのアドレス範囲:** 10.1.255.0/27
* **パブリック IP アドレス名:** VNet1GWpip

**接続の種類とクライアント アドレス プール**

* **[接続の種類]** : ポイント対サイト
* **クライアント アドレス プール:** 172.16.201.0/24<br>このポイント対サイト接続を利用して VNet に接続する VPN クライアントは、クライアント アドレス プールから IP アドレスを受け取ります。

## <a name="create-a-vnet"></a><a name="createvnet"></a>VNet を作成する

このセクションでは、仮想ネットワークを作成します。

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vpn-gateway"></a><a name="creategw"></a>VPN ゲートウェイの作成

この手順では、VNet の仮想ネットワーク ゲートウェイを作成します。 選択したゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

>[!NOTE]
>Basic ゲートウェイ SKU では、IKEv2 と RADIUS 認証はサポートされません。 Mac クライアントを仮想ネットワークに接続する予定がある場合は、Basic SKU を使用しないでください。
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-portal-include.md)]

デプロイの状態は、ゲートウェイの [概要] ページで確認できます。 ゲートウェイの作成後は、ポータルの仮想ネットワークを調べることで、ゲートウェイに割り当てられている IP アドレスを確認できます。 ゲートウェイは、接続されたデバイスとして表示されます。

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="generate-certificates"></a><a name="generatecert"></a>証明書の生成

証明書は、ポイント対サイト VPN 接続を介して VNet に接続するクライアントを認証するために、Azure によって使用されます。 ルート証明書を取得したら、公開キー情報を Azure に[アップロード](#uploadfile)します。 ルート証明書は、Azure によって "信頼された" と見なされ、P2S 経由での仮想ネットワークへの接続に使用されます。 また、信頼されたルート証明書からクライアント証明書を生成し、それを各クライアント コンピューターにインストールします。 クライアント証明書は、クライアントで VNet への接続を開始するときに、そのクライアントを認証するために使用されます。

### <a name="generate-a-root-certificate"></a><a name="getcer"></a>ルート証明書を生成する

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-client-certificates"></a><a name="generateclientcert"></a>クライアント証明書を生成する

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="add-the-vpn-client-address-pool"></a><a name="addresspool"></a>VPN クライアント アドレス プールを追加する

クライアント アドレス プールとは、指定するプライベート IP アドレスの範囲です。 ポイント対サイト VPN 経由で接続するクライアントは、この範囲内の IP アドレスを動的に受け取ります。 接続元であるオンプレミスの場所、または接続先とする VNet と重複しないプライベート IP アドレス範囲を使用してください。 複数のプロトコルを構成するとき、SSTP がプロトコルの 1 つの場合、構成後のアドレス プールは構成されるプロトコル間で均等に分割されます。

1. 仮想ネットワーク ゲートウェイが作成されたら、[仮想ネットワーク ゲートウェイ] ページの **[設定]** セクションに移動します。 **[設定]** で、 **[ポイント対サイトの構成]** を選択します。 **[今すぐ構成]** を選択して、構成ページを開きます。

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="[ポイント対サイトの構成] ページ。" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. **[ポイント対サイトの構成]** ページの **[アドレス プール]** ボックスに、使用するプライベート IP アドレス範囲を追加します。 VPN クライアントには、指定した範囲から動的に IP アドレスが割り当てられます。 最小のサブネット マスクは、アクティブ/パッシブ構成の場合は 29 ビット、アクティブ/アクティブ構成の場合は 28 ビットです。
1. 次のセクションに進み、認証とトンネルの種類を構成します。

## <a name="specify-tunnel-type-and-authentication-type"></a><a name="type"></a>トンネルの種類と認証の種類を指定する

このセクションでは、トンネルの種類と認証の種類を指定します。 [ポイント対サイトの構成] ページで、トンネルの種類も認証の種類も表示されない場合、ご利用のゲートウェイで使用されているのは Basic SKU です。 Basic SKU では、IKEv2 と RADIUS 認証はサポートされません。 これらの設定を使用する場合は、ゲートウェイを削除し、別のゲートウェイ SKU を使って再作成する必要があります。

### <a name="tunnel-type"></a><a name="tunneltype"></a>トンネルの種類

**[ポイント対サイトの構成]** ページで、 **[トンネルの種類]** を選択します。 トンネルの種類を選択するときは、次の点にご注意ください。

* Android と Linux の strongSwan クライアントおよび iOS と macOS のネイティブ IKEv2 VPN クライアントでは、接続にトンネルの種類 IKEv2 のみを使用します。
* Windows クライアントでは最初に IKEv2 を試し、接続できなかった場合に SSTP にフォールバックします。
* OpenVPN クライアントを使用して、OpenVPN トンネルの種類に接続することができます。

### <a name="authentication-type"></a><a name="authenticationtype"></a>認証の種類

**[認証の種類]** で **[Azure 証明書]** を選択します。

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication.png" alt-text="Azure 証明書が選択されている認証の種類のスクリーンショット。" :::

## <a name="upload-root-certificate-public-key-information"></a><a name="uploadfile"></a>ルート証明書の公開キー情報のアップロード

このセクションでは、公開ルート証明書データを Azure にアップロードします。 公開証明書データがアップロードされたら、Azure でそれを使用し、信頼されたルート証明書から生成されたクライアント証明書がインストールされているクライアントを認証できます。

1. **[ルート証明書]** セクションで **仮想ネットワーク ゲートウェイの [ポイント対サイトの構成]** ページに移動します。 このセクションは、認証の種類として **[Azure 証明書]** を選択した場合にのみ表示されます。
1. ルート証明書を **Base 64 でエンコードされた X.509 (.CER)** ファイルとして、エクスポートしたことをご確認ください。 証明書をテキスト エディターで開くことができるように、この形式でエクスポートする必要があります。 秘密キーをエクスポートする必要はありません。

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/base-64.png" alt-text="Base-64 でエンコードされた x.509 としてのエクスポートを示すスクリーンショット。" :::
1. 証明書をメモ帳などのテキスト エディターで開きます。 証明書データをコピーするときはに、必ず、テキストを復帰や改行のない 1 つの連続した行としてコピーしてください。 復帰や改行を確認するには、テキスト エディターのビューを "記号を表示する/すべての文字を表示する" ように変更することが必要になる場合があります。 次のセクションのみを 1 つの連続した行としてコピーします。

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png" alt-text="メモ帳のルート証明書情報を示すスクリーンショット。" border="false":::
1. **[ルート証明書]** セクションで、最大 20 個の信頼されたルート証明書を追加できます。

   * **[公開証明書データ]** フィールドに証明書データを貼り付けます。 
   * 証明書に **名前** を付けます。 

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/root-certificate.png" alt-text="証明書データ フィールドのスクリーンショット。" :::
1. ページの上部にある **[保存]** を選択して、構成設定をすべて保存します。

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save-configuration.png" alt-text="[保存] が選択されている P2S 構成のスクリーンショット。" :::

## <a name="install-exported-client-certificate"></a><a name="installclientcert"></a>エクスポートしたクライアント証明書をインストールします

クライアント証明書の生成に使用したクライアント コンピューター以外から P2S 接続を作成する場合は、クライアント証明書をインストールする必要があります。 クライアント証明書をインストールするときに、クライアント証明書のエクスポート時に作成されたパスワードが必要になります。

クライアント証明書が証明書チェーン全体と一緒に .pfx としてエクスポートされている (既定値) ことを確認します。 そうでないと、ルート証明書情報がクライアント コンピューターに存在せず、クライアントは正しく認証されません。

インストールの手順については、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページを参照してください。

## <a name="configure-settings-for-vpn-clients"></a><a name="clientconfig"></a>VPN クライアントの設定を構成する

P2S を使用して仮想ネットワーク ゲートウェイに接続するために、各コンピューターは、オペレーティング システムの一部としてネイティブにインストールされている VPN クライアントを使用します。 たとえば、Windows コンピューターの VPN 設定に移動すると、別に VPN クライアントをインストールせずに VPN 接続を追加できます。 各 VPN クライアントは、クライアント構成パッケージを使用して構成します。 クライアント構成パッケージには、作成した VPN ゲートウェイに固有の設定が含まれています。 

VPN クライアント構成ファイルの生成とインストールに関する手順については、[Azure 証明書認証の P2S 構成のための VPN クライアント構成ファイルを作成およびインストール](point-to-site-vpn-client-configuration-azure-cert.md)に関するページを参照してください。

## <a name="connect-to-azure"></a><a name="connect"></a>Azure に接続する

### <a name="to-connect-from-a-windows-vpn-client"></a>Windows VPN クライアントから接続するには

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Mac の VPN クライアントから接続するには

[ネットワーク] ダイアログ ボックスで、使用するクライアント プロファイルを検索し、[VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac) の設定を指定して、 **[接続]** を選択します。 詳細な手順については、[VPN クライアント構成ファイルを生成してインストールする - macOS](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) に関するページを参照してください。 

接続に問題がある場合は、仮想ネットワーク ゲートウェイが Basic SKU を使用していないことを確認します。 Basic SKU は Mac クライアントではサポートされていません。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-connect.png" alt-text="[接続] ボタンを示すスクリーンショット。" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-connect.png":::

## <a name="to-verify-your-connection"></a><a name="verify"></a>接続を確認するには

ここで紹介する手順は、Windows クライアントに適用されます。

1. VPN 接続がアクティブであることを確認するには、管理者特権でのコマンド プロンプトを開いて、 *ipconfig/all* を実行します。
2. 結果を表示します。 受信した IP アドレスが、構成に指定したポイント対サイト VPN クライアント アドレス プール内のアドレスのいずれかであることに注意してください。 結果は次の例のようになります。

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>仮想マシンに接続するには

ここで紹介する手順は、Windows クライアントに適用されます。

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* VNet に対して DNS サーバーの IP アドレスが指定された後に VPN クライアント構成パッケージが生成されたことを確認します。 DNS サーバーの IP アドレスを更新した場合は、新しい VPN クライアント構成パッケージを生成してインストールしてください。

* 接続元のコンピューターのイーサネット アダプターに割り当てられている IPv4 アドレスを "ipconfig" でチェックします。 その IP アドレスが、接続先の VNet のアドレス範囲内または VPNClientAddressPool のアドレス範囲内にある場合、これを "アドレス空間の重複" といいます。 アドレス空間がこのように重複していると、ネットワーク トラフィックが Azure に到達せずローカル ネットワーク上に留まることになります。

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>信頼されたルート証明書を追加または削除するには

信頼されたルート証明書を Azure に追加したり、Azure から削除したりできます。 ルート証明書を削除すると、そのルートから証明書を生成したクライアントは認証が無効となり、接続できなくなります。 クライアントの認証と接続を正常に実行できるようにするには、Azure に信頼されている (Azure にアップロードされている) ルート証明書から生成した新しいクライアント証明書をインストールする必要があります。

信頼されたルート証明書 .cer ファイルを最大 20 個まで Azure に追加できます。 手順については、[信頼されたルート証明書のアップロード](#uploadfile)に関するセクションを参照してください。

信頼されたルート証明書を削除するには

1. ご利用の仮想ネットワーク ゲートウェイの **[ポイント対サイトの構成]** ページに移動します。
1. ページの **[ルート証明書]** セクションで、削除する証明書を見つけます。
1. 証明書の横にある省略記号を選んでから、 **[削除]** を選択します。

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>クライアント証明書を失効させるには

クライアント証明書は失効させることができます。 証明書失効リストを使用すると、個々のクライアント証明書に基づくポイント対サイト接続を選択して拒否することができます。 これは、信頼されたルート証明書を削除することとは異なります。 信頼されたルート証明書 .cer を Azure から削除すると、失効したルート証明書によって生成または署名されたすべてのクライアント証明書のアクセス権が取り消されます。 ルート証明書ではなくクライアント証明書を失効させることで、ルート証明書から生成されたその他の証明書は、その後も認証に使用できます。

一般的な方法としては、ルート証明書を使用してチームまたは組織レベルでアクセスを管理し、失効したクライアント証明書を使用して、個々のユーザーの細かいアクセス制御を構成します。

失効リストに拇印を追加することで、クライアント証明書を失効させることができます。

1. クライアント証明書の拇印を取得します。 詳細については、「[方法: 証明書のサムプリントを取得する](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)」を参照してください。
1. 情報をテキスト エディターにコピーし、文字列が 1 つにつながるようにスペースをすべて削除します。
1. 仮想ネットワーク ゲートウェイの **[ポイント対サイトの構成]** ページに移動します。 このページは、[信頼されたルート証明書のアップロード](#uploadfile)に使用したものです。
1. **[失効した証明書]** セクションで、証明書のフレンドリ名を入力します (証明書 CN にする必要はありません)。
1. 拇印の文字列をコピーして **[拇印]** フィールドに貼り付けます。
1. 拇印が検証され、自動的に失効リストに追加されます。 リストが更新されていることを示すメッセージが画面に表示されます。 
1. 更新が完了した後は、証明書を接続に使用することができなくなります。 この証明書を使用して接続を試みたクライアントには、証明書が無効になっていることを示すメッセージが表示されます。

## <a name="point-to-site-faq"></a><a name="faq"></a>ポイント対サイトに関する FAQ

よくあるご質問については、[FAQ](vpn-gateway-vpn-faq.md#P2S) を参照してください。

## <a name="next-steps"></a>次のステップ
接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](../index.yml) に関するページを参照してください。 ネットワークと仮想マシンの詳細については、「[Azure と Linux の VM ネットワークの概要](../virtual-network/network-overview.md)」を参照してください。

P2S のトラブルシューティング情報については、[Azure ポイント対サイト接続のトラブルシューティング](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)に関するページを参照してください。