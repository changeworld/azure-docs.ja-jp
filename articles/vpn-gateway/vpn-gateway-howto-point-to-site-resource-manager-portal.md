---
title: "ポイント対サイトを使用してコンピューターを Azure 仮想ネットワークに接続する: ポータル | Microsoft Docs"
description: "Resource Manager と Azure Portal を使用してポイント対サイト VPN ゲートウェイ接続を作成することで、Azure Virtual Network に安全に接続します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: cfe70a92c29dcdef962bbbe256de1a687782b6e4
ms.contentlocale: ja-jp
ms.lasthandoff: 05/16/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Azure Portal を使用した VNet へのポイント対サイト接続の構成

この記事では、Azure Portal を使用して、ポイント対サイト接続を備えた VNet を Resource Manager デプロイメント モデルで作成する手順を説明します。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [クラシック - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

ポイント対サイト (P2S) 構成では、個々のクライアント コンピューターから仮想ネットワークへのセキュリティで保護された接続を作成することができます。 ポイント対サイト接続は、自宅や会議室など、リモートの場所から VNet に接続する場合や、仮想ネットワークに接続する必要があるクライアントの数が少ない場合に便利です。 P2S VPN 接続は、クライアント コンピューターからネイティブ Windows VPN クライアントを使用して開始されます。 クライアントの接続には、認証のために証明書が使用されます。 


![ポイント対サイトのダイアログ](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

ポイント対サイト接続に、VPN デバイスや公開 IP アドレスは必要ありません。 P2S により、SSTP (Secure Socket トンネリング プロトコル) 経由での VPN 接続が作成されます。 サーバー側でのサポート対象の SSTP バージョンは、1.0、1.1、1.2 です。 使用するバージョンはクライアントによって決まります。 Windows 8.1 以降の場合、SSTP では既定で 1.2 が使用されます。 ポイント対サイト接続の詳細については、この記事の最後にある「[ポイント対サイト接続に関してよく寄せられる質問](#faq)」を参照してください。

P2S 接続には、以下のものが必要です。

* RouteBased VPN ゲートウェイ。
* Azure にアップロードされた、ルート証明書の公開キー (.cer ファイル)。 これは信頼された証明書と見なされ、認証に使用されます。
* ルート証明書から生成され、接続する各クライアント コンピューターにインストールされたクライアント証明書。 この証明書はクライアントの認証に使用されます。
* VPN クライアント構成パッケージが生成され、接続するすべてのクライアント コンピューターにインストールされていること。 このクライアント構成パッケージによって構成されるのは、既にオペレーティング システム上で動作し、VNet への接続に必要な情報を備えているネイティブ VPN クライアントです。

### <a name="example"></a>値の例

次の値を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。

* **名前: VNet1**
* **アドレス空間: 192.168.0.0/16**<br>この例では、1 つのアドレス空間のみを使用します。 VNet には、複数のアドレス空間を使用することができます。
* **サブネット名: FrontEnd**
* **サブネットのアドレス範囲: 192.168.1.0/24**
* **サブスクリプション:** サブスクリプションが複数ある場合は、適切なサブスクリプションを使用していることを確認します。
* **リソース グループ: TestRG**
* **場所: 米国東部**
* **GatewaySubnet: 192.168.200.0/24**
* **仮想ネットワーク ゲートウェイ名: VNet1GW**
* **ゲートウェイの種類: VPN**
* **VPN の種類: ルート ベース**
* **パブリック IP アドレス: VNet1GWpip**
* **接続の種類: ポイント対サイト**
* **クライアント アドレス プール: 172.16.201.0/24**<br>このポイント対サイト接続を利用して VNet に接続する VPN クライアントは、クライアント アドレス プールから IP アドレスを受け取ります。

## <a name="createvnet"></a>1 - 仮想ネットワークの作成

最初に Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。 練習としてこの構成を作成する場合は、[値の例](#example)を参照してください。

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="address"></a>2 - アドレス空間とサブネットの指定

VNet が作成されたら、アドレス空間とサブネットをさらに追加できます。

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3 - ゲートウェイ サブネットの追加

仮想ネットワークをゲートウェイに接続する前に、まず、接続先の仮想ネットワークのゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サービスでは、ゲートウェイ サブネット内に指定された IP アドレスを使用します。 将来的な構成要件も見越して十分な IP アドレスを確保するために、可能であれば、/28 または /27 の CIDR ブロックを使用してゲートウェイ サブネットを作成します。

このセクションのスクリーンショットは、参照用の例です。 構成に必要な値に対応する GatewaySubnet アドレス範囲を使用するようにしてください。

### <a name="to-create-a-gateway-subnet"></a>ゲートウェイ サブネットを作成するには

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4 - DNS サーバーの指定 (省略可能)

仮想ネットワークを作成した後は、名前解決を処理するために、DNS サーバーの IP アドレスを追加できます。 指定された DNS サーバーは、接続するリソースの名前を解決できる DNS サーバーである必要があります。 後続の手順で生成する VPN クライアント構成パッケージには、この設定で指定した DNS サーバーの IP アドレスが追加されます。 将来 DNS サーバーのリストを更新する必要が生じた場合は、その新しいリストを反映した新しい VPN クライアント構成パッケージを生成してインストールすることができます。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>5 - 仮想ネットワーク ゲートウェイの作成

ポイント対サイト接続では、以下の設定が必要です。

* ゲートウェイの種類: VPN
* VPN の種類: ルート ベース

### <a name="to-create-a-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成するには

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>6 - 証明書の生成

証明書は、ポイント対サイト VPN の VPN クライアントを認証するために、Azure によって使用されます。 そのため、ルート証明書の公開キー情報を Azure にアップロードします。 その後、その公開キーは "信頼された" と見なされます。 信頼されたルート証明書からクライアント証明書を生成し、それを各クライアント コンピューターの [証明書 - 現在のユーザー] の [個人] 証明書ストアにインストールする必要があります。 この証明書は、クライアントで VNet への接続を開始するときに、そのクライアントを認証するために使用されます。 証明書の生成とインストールの詳細については、[ポイント対サイトの証明書](vpn-gateway-certificates-point-to-site.md)に関するページを参照してください。

### <a name="getcer"></a>手順 1 - ルート証明書の .cer ファイルの取得

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>手順 2 - クライアント証明書の生成

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>7 - クライアント アドレス プールの追加

クライアント アドレス プールとは、指定するプライベート IP アドレスの範囲です。 P2S 経由で接続するクライアントは、この範囲内の IP アドレスを受け取ります。 接続元であるオンプレミスの場所、または接続先とする VNet と重複しないプライベート IP アドレス範囲を使用してください。

1. 仮想ネットワーク ゲートウェイが作成されたら、[仮想ネットワーク ゲートウェイ] ブレードの **[設定]** セクションに移動します。 **[設定]** セクションで **[ポイント対サイトの構成]** をクリックして、**[構成]** ブレードを開きます。

  ![ポイント対サイトのブレード](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png)
2. 自動入力される範囲は削除してから、使用するプライベート IP アドレス範囲を追加することができます。 設定を確認して保存するには、**[保存]** をクリックします。

  ![クライアント アドレス プール](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>8 - ルート証明書 .cer ファイルのアップロード

ゲートウェイが作成された後は、信頼されたルート証明書の .cer ファイル (公開キー情報を含むもの) を Azure にアップロードできます。 .cer ファイルがアップロードされると、Azure ではそれを使用し、信頼されたルート証明書から生成されたクライアント証明書がインストールされているクライアントを認証できます。 その後は必要に応じて、最大で合計 20 個になるまで信頼されたルート証明書を追加でアップロードできます。 

1. 証明書は、**[ルート証明書]** セクションの **[ポイント対サイトの構成]** ブレードで追加します。  
2. ルート証明書を Base 64 でエンコードされた X.509 (.cer) ファイルとしてエクスポートしたことを確認してください。 証明書をテキスト エディターで開くことができるように、この形式でエクスポートする必要があります。
3. 証明書をメモ帳などのテキスト エディターで開きます。 証明書データをコピーするときはに、必ず、テキストを復帰や改行のない 1 つの連続した行としてコピーしてください。 復帰や改行を確認するには、テキスト エディターのビューを "記号を表示する/すべての文字を表示する" ように変更することが必要になる場合があります。 次のセクションのみを 1 つの連続した行としてコピーします。

  ![証明書データ](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)
4. **[公開証明書データ]** フィールドに証明書データを貼り付けます。 証明書に**名前**を付けて、**[保存]** をクリックします。 最大 20 個の信頼されたルート証明書を追加できます。

  ![証明書のアップロード](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="clientconfig"></a>9 - VPN クライアント構成パッケージのインストール

ポイント対サイト VPN を使って VNet に接続するには、ネイティブ Windows VPN クライアントを構成するためのパッケージをクライアントごとにインストールする必要があります。 構成パッケージは、仮想ネットワークへの接続に必要な設定を使ってネイティブ Windows VPN クライアントを構成します。したがって VNet の DNS サーバーを指定した場合、クライアントの名前解決に使用される DNS サーバーの IP アドレスが構成パッケージに追加されます。 指定した DNS サーバーを後から、クライアント構成パッケージの生成後に変更する場合は、必ず新しいクライアント構成パッケージを生成してクライアント コンピューターにインストールしてください。

バージョンがクライアントのアーキテクチャと一致すれば、各クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。 サポートされているクライアント オペレーティング システムの一覧については、この記事の最後にある「[ポイント対サイト接続に関してよく寄せられる質問](#faq)」を参照してください。

### <a name="step-1---download-the-client-configuration-package"></a>手順 1 - クライアント構成パッケージをダウンロードする

1. **[ポイント対サイトの構成]** ブレードで **[Download VPN client (VPN クライアントのダウンロード)]** をクリックして、**[Download VPN client (VPN クライアントのダウンロード)]** ブレードを開きます。 パッケージの生成には 1 ～ 2 分かかります。

  ![VPN クライアントのダウンロード 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. クライアント用の適切なパッケージを選択して、**[ダウンロード]** をクリックします。 構成パッケージ ファイルを保存します。 この VPN クライアント構成パッケージは、仮想ネットワークに接続する各クライアント コンピューターにインストールします。

  ![VPN クライアントのダウンロード 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpnclient.png)

### <a name="step-2---install-the-client-configuration-package"></a>手順 2 - クライアント構成パッケージをインストールする

1. 仮想ネットワークに接続するコンピューターのローカルに構成ファイルをコピーします。 
2. .exe ファイルをダブルクリックしてパッケージをクライアント コンピューターにインストールします。 構成パッケージを作成したのは皆さん自身であり、署名されていないために警告が表示されることがあります。 Windows SmartScreen ポップアップが表示された場合は、**[詳細]** (左側)、**[実行]** の順にクリックして、パッケージをインストールします。
3. パッケージをクライアント コンピューターにインストールします。 Windows SmartScreen ポップアップが表示された場合は、**[詳細]** (左側)、**[実行]** の順にクリックして、パッケージをインストールします。
4. クライアント コンピューターで **[ネットワークの設定]** に移動し、**[VPN]** をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。

## <a name="installclientcert"></a>10 - クライアント証明書のインストール

クライアント証明書の生成に使用したクライアント コンピューター以外から P2S 接続を作成する場合は、クライアント証明書をインストールする必要があります。 クライアント証明書をインストールするときに、クライアント証明書のエクスポート時に作成されたパスワードが必要になります。 通常は、証明書をダブルクリックするだけでインストールできます。 詳細については、「[エクスポートしたクライアント証明書のインストール](vpn-gateway-certificates-point-to-site.md#install)」を参照してください。

## <a name="connect"></a>11 - Azure への接続

1. VNet に接続するには、クライアント コンピューターで [VPN 接続] に移動し、作成した VPN 接続を見つけます。 仮想ネットワークと同じ名前が付いています。 **[接続]**をクリックします。 証明書を使用することを示すポップアップ メッセージが表示される場合があります。 **[続行]** をクリックして、昇格された特権を使用します。

2. **接続**の状態ページで、**[接続]** をクリックして接続を開始します。 **[証明書の選択]** 画面が表示された場合は、表示されているクライアント証明書が接続に使用する証明書であることを確認します。 そうでない場合は、ドロップダウン矢印を使用して適切な証明書を選択し、 **[OK]**をクリックします。

  ![Azure への VPN クライアントの接続](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. 接続が確立されました。

  ![確立された接続](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

接続に問題がある場合は、次の点を確認してください。

- **[ユーザー証明書の管理]** を開いて **[Trusted Root Certification Authorities\Certificates]** に移動します。 ルート証明書が表示されていることを確認します。 認証が正しく機能するためには、ルート証明書が存在している必要があります。 既定値の [証明のパスにある証明書を可能であればすべて含む] でクライアント証明書の .pfx をエクスポートすると、ルート証明書の情報もエクスポートされます。 クライアント証明書をインストールするときに、ルート証明書もクライアント コンピューターにインストールされます。 

- エンタープライズ CA ソリューションを使用して発行した証明書を使用している場合に認証の問題が発生したときは、クライアント証明書の認証の順序を確認します。 認証の一覧の順序を確認するには、クライアント証明書をダブルクリックし、**[詳細]、[拡張キー使用法]** の順に選択します。 一覧の最初の項目として "クライアント認証" が表示されていることを確認します。 表示されていない場合は、一覧の最初の項目が "クライアント認証" であるユーザー テンプレートに基づいたクライアント証明書を発行する必要があります。


## <a name="verify"></a>12 - 接続を確認する

1. VPN 接続がアクティブであることを確認するには、管理者特権でのコマンド プロンプトを開いて、 *ipconfig/all*を実行します。
2. 結果を表示します。 受信した IP アドレスが、構成に指定したポイント対サイト VPN クライアント アドレス プール内のアドレスのいずれかであることに注意してください。 結果は次の例のようになります。
   
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


P2S での仮想マシンへの接続に問題がある場合は、接続元のコンピューターのイーサネット アダプターに割り当てられている IPv4 アドレスを "ipconfig" でチェックします。 その IP アドレスが、接続先の VNet のアドレス範囲内または VPNClientAddressPool のアドレス範囲内にある場合、これを "アドレス空間の重複" といいます。 アドレス空間がこのように重複していると、ネットワーク トラフィックが Azure に到達せずローカル ネットワーク上に留まることになります。 ネットワーク アドレス空間が重複していないのに、VM に接続できない場合は、[VM へのリモート デスクトップ接続のトラブルシューティング](../virtual-machines/windows/troubleshoot-rdp-connection.md)に関するページを参照してください。

## <a name="connectVM"></a>仮想マシンへの接続

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="add"></a>信頼されたルート証明書を追加または削除する

信頼されたルート証明書を Azure に追加したり、Azure から削除したりできます。 ルート証明書を削除すると、そのルートから証明書を生成したクライアントは認証が無効となり、接続できなくなります。 クライアントの認証と接続を正常に実行できるようにするには、Azure に信頼されている (Azure にアップロードされている) ルート証明書から生成した新しいクライアント証明書をインストールする必要があります。

### <a name="to-add-a-trusted-root-certificate"></a>信頼されたルート証明書を追加するには

信頼されたルート証明書 .cer ファイルを最大 20 個まで Azure に追加できます。 手順については、この記事の[信頼されたルート証明書のアップロード](#uploadfile)に関するセクションを参照してください。

### <a name="to-remove-a-trusted-root-certificate"></a>信頼されたルート証明書を削除するには

1. 信頼されたルート証明書を削除するには、仮想ネットワーク ゲートウェイの **[ポイント対サイトの構成]** ブレードに移動します。
2. ブレードの **[ルート証明書]** セクションで、削除する証明書を見つけます。
3. 証明書の横にある省略記号をクリックし、[削除] をクリックします。

## <a name="revokeclient"></a>クライアント証明書の失効

クライアント証明書は失効させることができます。 証明書失効リストを使用すると、個々のクライアント証明書に基づくポイント対サイト接続を選択して拒否することができます。 これは、信頼されたルート証明書を削除する場合とは異なります。 信頼されたルート証明書 .cer を Azure から削除すると、失効したルート証明書によって生成または署名されたすべてのクライアント証明書のアクセス権が取り消されます。 ルート証明書ではなくクライアント証明書を失効させることで、ルート証明書から生成されたその他の証明書は、その後も認証に使用できます。

一般的な方法としては、ルート証明書を使用してチームまたは組織レベルでアクセスを管理し、失効したクライアント証明書を使用して、個々のユーザーの細かいアクセス制御を構成します。

### <a name="to-revoke-a-client-certificate"></a>クライアント証明書を失効させるには

失効リストに拇印を追加することで、クライアント証明書を失効させることができます。

1. クライアント証明書の拇印を取得します。 詳細については、「[方法: 証明書のサムプリントを取得する](https://msdn.microsoft.com/library/ms734695.aspx)」を参照してください。
2. 情報をテキスト エディターにコピーし、文字列が 1 つにつながるようにスペースをすべて削除します。
3. 仮想ネットワーク ゲートウェイの **[ポイント対サイトの構成]** ブレードに移動します。 このブレードは、[信頼されたルート証明書のアップロード](#uploadfile)に使用したものです。
4. **[失効した証明書]** セクションで、証明書のフレンドリ名を入力します (証明書 CN にする必要はありません)。
5. 拇印の文字列をコピーして **[拇印]** フィールドに貼り付けます。
6. 拇印が検証され、自動的に失効リストに追加されます。 リストが更新されていることを示すメッセージが画面に表示されます。 
7. 更新が完了した後は、証明書を接続に使用することができなくなります。 この証明書を使用して接続を試みたクライアントには、証明書が無効になっていることを示すメッセージが表示されます。

## <a name="faq"></a>ポイント対サイト接続に関してよく寄せられる質問

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>次のステップ
接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) に関するページを参照してください。 ネットワークと仮想マシンの詳細については、「[Azure と Linux の VM ネットワークの概要](../virtual-machines/linux/azure-vm-network-overview.md)」を参照してください。
