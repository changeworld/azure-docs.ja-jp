---
title: "ポイント対サイトを使用してコンピューターを Azure 仮想ネットワークに接続する: PowerShell | Microsoft Docs"
description: "ポイント対サイト VPN Gateway 接続を作成することで、コンピューターを Azure 仮想ネットワークに安全に接続します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: d340210d799f995cb10a20cf48a9245bbd3bc8d3
ms.lasthandoff: 04/27/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>PowerShell を使用した VNet へのポイント対サイト接続の構成
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [クラシック - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

ポイント対サイト (P2S) 構成では、個々のクライアント コンピューターから仮想ネットワークへのセキュリティで保護された接続を作成することができます。 P2S は、SSTP (Secure Socket トンネリング プロトコル) 経由の VPN 接続です。 ポイント対サイト接続は、自宅や会議室など、リモートの場所から VNet に接続する場合や、仮想ネットワークに接続する必要があるクライアントの数が少ない場合に便利です。 P2S 接続に VPN デバイスや公開 IP アドレスは必要ありません。 VPN 接続は、クライアント コンピューターから確立します。

この記事では、PowerShell を使用した、Resource Manager デプロイメント モデルでのポイント対サイト接続を持つ VNet の作成について説明します。 ポイント対サイト接続の詳細については、この記事の最後にある「[ポイント対サイト接続に関してよく寄せられる質問](#faq)」を参照してください。

### <a name="deployment-models-and-methods-for-p2s-connections"></a>P2S 接続のデプロイメント モデルとデプロイ方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

次の表は、2 つのデプロイメント モデルと、P2S 構成で使用可能なデプロイ方法を示しています。 構成手順を説明した記事が利用できるようになったら、表から直接リンクできるようにします。

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>基本的なワークフロー
![コンピューターを Azure VNet に接続する - ポイント対サイト接続の図](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

このシナリオでは、ポイント対サイト接続で仮想ネットワークを作成します。 この手順は、この構成で必要となる証明書の生成にも役立ちます。 P2S 接続は、VPN ゲートウェイのある VNet、ルート証明書 .cer ファイル (公開キー)、クライアント証明書、クライアントの VPN 構成で構成されます。 

この構成では次の値を使用します。 この記事のパート [1](#declare) で変数を設定します。 手順をチュートリアルとして利用して値を変更せずに使用することも、実際の環境に合わせて値を変更することもできます。 

### <a name="example"></a>値の例
* **名前: VNet1**
* **アドレス空間: 192.168.0.0/16** と **10.254.0.0/16**<br>この例では、この構成が複数のアドレス空間で機能することを示すために、複数のアドレス空間を使用します。 ただし、この構成で複数のアドレス空間は必須ではありません。
* **サブネット名: FrontEnd**
  * **サブネットのアドレス範囲: 192.168.1.0/24**
* **サブネット名: BackEnd**
  * **サブネットのアドレス範囲: 10.254.1.0/24**
* **サブネット名: GatewaySubnet**<br>VPN ゲートウェイが機能するには、サブネット名 *GatewaySubnet* が必須となります。
  * **GatewaySubnet のアドレス範囲: 192.168.200.0/24** 
* **VPN クライアント アドレス プール: 172.16.201.0/24**<br>このポイント対サイト接続を利用して VNet に接続する VPN クライアントは、この VPN クライアント アドレス プールから IP アドレスを受け取ります。
* **サブスクリプション:** サブスクリプションが複数ある場合は、適切なサブスクリプションを使用していることを確認します。
* **リソース グループ: TestRG**
* **場所: 米国東部**
* **DNS サーバー**: 名前解決に利用する DNS サーバーの IP アドレス。
* **GW 名: Vnet1GW**
* **パブリック IP 名: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="before-beginning"></a>作業を開始する前に
* Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。
* Azure Resource Manager PowerShell コマンドレットの最新版をインストールしてください。 PowerShell コマンドレットのインストールの詳細については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)に関するページを参照してください。 

## <a name="declare"></a>パート 1 - ログインと変数の設定
このセクションでは、ログインのほか、この構成で使用される値の宣言を行います。 サンプル スクリプトでは、宣言済みの値が使用されます。 実際の環境に合わせて値を変更してください。 宣言済みの値を使用し、以下の手順を練習として使用することもできます。

1. 昇格された特権で PowerShell コンソールを開き、Azure アカウントにログインします。 このコマンドレットは、ログイン資格情報をユーザーに求めます。 ログイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。

  ```powershell
  Login-AzureRmAccount
  ```
2. Azure サブスクリプションの一覧を取得します。

  ```powershell  
  Get-AzureRmSubscription
  ```
3. 使用するサブスクリプションを指定します。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
4. 使用する変数を宣言します。 次のサンプルを使用し、必要に応じて独自の値で置き換えます。

  ```powershell
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $DNS = "8.8.8.8"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="ConfigureVNet"></a>パート 2 - VNet の構成
1. リソース グループを作成します。

  ```powershell
  New-AzureRmResourceGroup -Name $RG -Location $Location
  ```
2. 仮想ネットワークのサブネット構成を作成し、*FrontEnd*、*BackEnd*、*GatewaySubnet* いう名前を付けます。 これらのプレフィックスは、宣言した VNet アドレス空間に含まれている必要があります。

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
  ```
3. 仮想ネットワークを作成する。 指定された DNS サーバーは、接続するリソースの名前を解決できる DNS サーバーである必要があります。 この例では、パブリック IP アドレスを使用しましたが、 実際には独自の値を使用してください。

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
  ```
4. 作成した仮想ネットワークの変数を指定します。

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  ```
5. 動的に割り当てられたパブリック IP アドレスを要求します。 この IP アドレスは、ゲートウェイが適切に機能するために必要です。 後でゲートウェイをゲートウェイ IP 構成に接続します。

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```


## <a name="Certificates"></a>パート 3 - 証明書

証明書は、ポイント対サイト VPN の VPN クライアントを認証するために、Azure によって使用されます。 ルート証明書の作成後、(秘密キーではなく) 公開証明書データを、Base-64 でエンコードされた X.509 .cer ファイルとしてエクスポートします。 その後、公開証明書データをルート証明書から Azure にアップロードします。

ポイント対サイトで VNet に接続するすべてのクライアント コンピューターには、クライアント証明書がインストールされている必要があります。 クライアント証明書はルート証明書から生成され、各クライアント コンピューターにインストールされます。 有効なクライアント証明書がインストールされていない状態でクライアントが VNet に接続した場合、認証に失敗します。

### <a name="cer"></a>手順 1 - ルート証明書の .cer ファイルの取得

#### <a name="enterprise-certificate"></a>エンタープライズ証明書
 
エンタープライズ ソリューションを使用している場合、既存の証明書チェーンを使うことができます。 使用するルート証明書の .cer ファイルを取得します。

#### <a name="self-signed-root-certificate"></a>自己署名ルート証明書

エンタープライズ証明書ソリューションを使用していない場合は、自己署名ルート証明書を作成する必要があります。 P2S 認証に必要なフィールドを含む自己署名ルート証明書は、PowerShell を使用して作成できます。 自己署名ルート証明書の作成手順については、「[Create a self-signed root certificate for Point-to-Site connections using PowerShell (PowerShell を使ったポイント対サイト接続用自己署名ルート証明書の作成)](vpn-gateway-certificates-point-to-site.md)」で確認できます。

> [!NOTE]
> 以前は、自己署名ルート証明書を作成してポイント対サイト接続用のクライアント証明書を生成する手段として makecert が推奨されていました。 現在は、PowerShell を使用してそれらの証明書を作成できるようになっています。 PowerShell を使う利点は、SHA-2 証明書を作成できることです。 必要な値については、「[Create a self-signed root certificate for Point-to-Site connections using PowerShell (PowerShell を使ったポイント対サイト接続用自己署名ルート証明書の作成)](vpn-gateway-certificates-point-to-site.md)」を参照してください。
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>自己署名ルート証明書用の公開キーをエクスポートするには

ポイント対サイト接続では、公開キー (.cer) が Azure にアップロードされている必要があります。 次の手順で、自己署名ルート証明書の .cer ファイルをエクスポートしてください。

1. 証明書から .cer ファイルを取得するには、**[ユーザー証明書の管理]** を開きます。
2. "Current User\Personal\Certificates" から "P2SRootCert" という自己署名ルート証明書を探して右クリックします。 **[すべてのタスク]** をクリックし、**[エクスポート]** をクリックして **[証明書のエクスポート ウィザード]** を開きます。
3. ウィザードで **[次へ]** をクリックします。 **[いいえ、秘密キーをエクスポートしません]** を選択して、**[次へ]** をクリックします。
4. **[エクスポート ファイルの形式]** ページで **[Base-64 encoded X.509 (.CER)]** を選択し、**[次へ]** をクリックします。 
5. **[エクスポートするファイル]** ページで "C:" ドライブに移動し、"cert" というサブディレクトリを作成して選択します。 証明書ファイルに "P2SRootCert.cer" という名前を付けて **[保存]** をクリックします。 
6. **[次へ]** をクリックし、**[完了]** をクリックして証明書をエクスポートします。 **エクスポートに成功しました**というメッセージが表示されます。 **[OK]** をクリックしてウィザードを閉じます。

### <a name="generate"></a>手順 2 - クライアント証明書の生成
クライアントごとに一意の証明書を生成することも、複数のクライアントに同じ証明書を使用することもできます。 一意のクライアント証明書を生成する利点は、1 つの証明書を失効させることができる点です。 そうでなければ、すべてのユーザーが同じクライアント証明書を使用していて、その証明書を失効させる必要がある場合は、認証にその証明書を使用するすべてのクライアントに新しい証明書を生成してインストールする必要があります。

#### <a name="enterprise-certificate"></a>エンタープライズ証明書
- エンタープライズ証明書ソリューションを使用している場合は、"domain name\username" 形式ではなく、共通名の値の形式 "name@yourdomain.com" を使用してクライアント証明書を生成します。
- クライアント証明書が、使用リストの最初の項目としてスマート カード ログオンなどではなく "クライアント認証" が指定されている "ユーザー" 証明書テンプレートに基づいていることを確認します。証明書を確認するには、クライアント証明書をダブルクリックし、**[詳細]、[拡張キー使用法]** の順に選択して表示します。

#### <a name="self-signed-root-certificate"></a>自己署名ルート証明書 
自己署名ルート証明書を使用する場合、[PowerShell を使ったクライアント証明書の生成](vpn-gateway-certificates-point-to-site.md#clientcert)に関するページで、ポイント対サイト接続に合ったクライアント証明書を生成する手順を参照してください。


### <a name="exportclientcert"></a>手順 3 - クライアント証明書のエクスポート

[PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) を使った手順で自己署名ルート証明書からクライアント証明書を生成した場合、その作業に使用したコンピューターに自動的にクライアント証明書がインストールされます。 クライアント証明書を別のクライアント コンピューターにインストールする場合は、その証明書をエクスポートする必要があります。
 
1. クライアント証明書をエクスポートするには、**[ユーザー証明書の管理]** を開きます。 エクスポートするクライアント証明書を右クリックして、**[すべてのタスク]**、**[エクスポート]** の順にクリックし、**証明書のエクスポート ウィザード**を開きます。
2. ウィザードで **[次へ]** をクリックし、**[はい、秘密キーをエクスポートします]** を選択して、**[次へ]** をクリックします。
3. **[エクスポート ファイルの形式]** ページでは、既定値をそのまま使用します。 必要なルート証明書の情報もエクスポートするには、**[証明のパスにある証明書を可能であればすべて含む]** を選択してください。 次に、 **[次へ]**をクリックします。
4. **[セキュリティ]** ページでは、秘密キーを保護する必要があります。 パスワードを使用する場合は、この証明書に設定したパスワードを必ず記録しておくか、覚えておいてください。 次に、 **[次へ]**をクリックします。
5. **[エクスポートするファイル]** で、**[参照]** をクリックして証明書をエクスポートする場所を選択します。 **[ファイル名]**に証明書ファイルの名前を指定します。 次に、 **[次へ]**をクリックします。
6. **[完了]** をクリックして、証明書をエクスポートします。

### <a name="upload"></a>手順 4 - ルート証明書 .cer ファイルのアップロード

ゲートウェイが作成されたら、信頼されたルート証明書の .cer ファイルを Azure にアップロードできます。 最大 20 個のルート証明書のファイルをアップロードすることができます。 ルート証明書の秘密キーは、Azure にアップロードしません。 .cer ファイルをアップロードすると、Azure は仮想ネットワークに接続するクライアントの認証にそれを使用します。

1. 証明書名のための変数を、独自の値に置き換えて宣言します。

  ```powershell
  $P2SRootCertName = "Mycertificatename.cer"
  ```
2. ファイルのパスを独自のパスに置換し、次のコマンドレットを実行します。

  ```powershell
  $filePathForCert = "C:\cert\Mycertificatename.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
  ```


## <a name="creategateway"></a>パート 4 - VPN ゲートウェイの作成
VNet の仮想ネットワーク ゲートウェイを構成、作成します。 *-GatewayType* は **Vpn** に、*-VpnType* は **RouteBased** にする必要があります。 VPN ゲートウェイの作成は、完了までに最大 45 分かかることがあります。

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert
```


## <a name="clientconfig"></a>パート 5 - VPN クライアント構成パッケージのダウンロード
ポイント対サイト VPN を使って VNet に接続するには、クライアントごとに VPN クライアント構成パッケージをインストールする必要があります。 このパッケージでは、VPN クライアントはインストールされません。 仮想ネットワークに接続するために必要な設定を使って、Windows のネイティブ VPN クライアントが構成されます。 サポートされているクライアント オペレーティング システムの一覧については、この記事の最後にある「[ポイント対サイト接続に関してよく寄せられる質問](#faq)」を参照してください。

1. ゲートウェイの作成が完了したら、クライアント構成パッケージを生成してダウンロードできます。 この例では、64 ビット クライアント用のパッケージをダウンロードします。 32 ビット クライアントをダウンロードする場合は、"Amd64" を "x86" に置き換えます。 Azure Portal を使用して VPN クライアントをダウンロードすることもできます。

  ```powershell
  Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
  -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
  ```
2. Web ブラウザーに返されたリンクをコピーして貼り付け、パッケージをダウンロードします。リンクを囲む """ は確実に削除してください。 
3. パッケージをクライアント コンピューターにダウンロードしてインストールします。 SmartScreen ポップアップが表示された場合は、**[詳細]**、**[実行]** の順にクリックしてください。 パッケージを保存して、他のクライアント コンピューターにインストールすることもできます。
4. クライアント コンピューターで **[ネットワークの設定]** に移動し、**[VPN]** をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。



## <a name="clientcertificate"></a>パート 6: エクスポートしたクライアント証明書のインストール

クライアント証明書の生成に使用したクライアント コンピューター以外から P2S 接続を作成する場合は、クライアント証明書をインストールする必要があります。 クライアント証明書をインストールするときに、クライアント証明書のエクスポート時に作成されたパスワードが必要になります。

1. *.pfx* ファイルを見つけ、クライアント コンピューターにコピーします。 クライアント コンピューターで、 *.pfx* ファイルをダブルクリックしてインストールします。 **[ストアの場所]** は **[現在のユーザー]** のままにしておき、**[次へ]** をクリックします。
2. **[インポートするファイル]** ページでは、何も変更しないでください。 **[次へ]**をクリックします。
3. **[秘密キーの保護]** ページで、証明書のパスワードを入力するか、セキュリティ プリンシパルが正しいことを確認し、**[次へ]** をクリックします。
4. **[証明書ストア]** ページで、既定の場所をそのまま使用し、**[次へ]** をクリックします。
5. **[完了]**をクリックします。 証明書のインストールの **[セキュリティ警告]** で **[はい]** をクリックします。 証明書を生成したので、[はい] をクリックしても問題ありません。 これで証明書がインポートされます。

## <a name="connect"></a>パート 7 - Azure への接続
1. VNet に接続するには、クライアント コンピューターで [VPN 接続] に移動し、作成した VPN 接続を見つけます。 仮想ネットワークと同じ名前が付いています。 **[接続]**をクリックします。 証明書を使用することを示すポップアップ メッセージが表示される場合があります。 **[続行]** をクリックして、昇格された特権を使用します。 
2. **接続**の状態ページで、**[接続]** をクリックして接続を開始します。 **[証明書の選択]** 画面が表示された場合は、表示されているクライアント証明書が接続に使用する証明書であることを確認します。 そうでない場合は、ドロップダウン矢印を使用して適切な証明書を選択し、 **[OK]**をクリックします。
   
    ![Azure への VPN クライアントの接続](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. 接続が確立されました。
   
    ![確立された接続](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

接続に問題がある場合は、次の点を確認してください。

- **[ユーザー証明書の管理]** を開いて **[Trusted Root Certification Authorities\Certificates]** に移動します。 ルート証明書が表示されていることを確認します。 認証が正しく機能するためには、ルート証明書が存在している必要があります。 既定値の [証明のパスにある証明書を可能であればすべて含む] でクライアント証明書の .pfx をエクスポートすると、ルート証明書の情報もエクスポートされます。 クライアント証明書をインストールするときに、ルート証明書もクライアント コンピューターにインストールされます。 

- エンタープライズ CA ソリューションを使用して発行した証明書を使用している場合に認証の問題が発生したときは、クライアント証明書の認証の順序を確認します。 認証の一覧の順序を確認するには、クライアント証明書をダブルクリックし、**[詳細]、[拡張キー使用法]** の順に選択します。 一覧の最初の項目として "クライアント認証" が表示されていることを確認します。 表示されていない場合は、一覧の最初の項目が "クライアント認証" であるユーザー テンプレートに基づいたクライアント証明書を発行する必要があります。  


## <a name="verify"></a>パート 8 - 接続の確認
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


## <a name="connectVM"></a>仮想マシンへの接続

1. VNet への接続後、P2S 接続で VM に接続することができます。 VM に接続するには、仮想マシンのプライベート IP アドレスが必要です。 プライベート IP アドレスは、以下の例のように [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) で取得できます。 このコマンドを実行すると、すべてのリソース グループに含まれる一連の VM と対応するプライベート IP アドレスが返されます。 

  ```powershell   
  $vms = get-azurermvm
  $nics = get-azurermnetworkinterface | where VirtualMachine -NE $null #skip Nics with no VM
  
  foreach($nic in $nics)
  {
    $vm = $vms | where-object -Property Id -EQ $nic.VirtualMachine.id
    $prv =  $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAddress
    $alloc =  $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAllocationMethod
    Write-Output "$($vm.Name) : $prv , $alloc"
  }
  ```
2. 次のコマンドを使用して、仮想マシンとのリモート デスクトップ セッションを作成します。 IP アドレスは、実際の接続先となる VM に関連付けられているプライベート IP アドレスに置き換えてください。 メッセージが表示されたら、仮想マシンの作成時に使用した資格情報を入力します。 

  ```powershell   
  mstsc /v:192.168.1.4
  ```

P2S での仮想マシンへの接続に問題がある場合は、接続元のコンピューターのイーサネット アダプターに割り当てられている IPv4 アドレスを "ipconfig" でチェックします。 その IP アドレスが、接続先の VNet のアドレス範囲内または VPNClientAddressPool のアドレス範囲内にある場合、これを "アドレス空間の重複" といいます。 アドレス空間がこのように重複していると、ネットワーク トラフィックが Azure に到達せずローカル ネットワーク上に留まることになります。 ネットワーク アドレス空間が重複していないのに、VM に接続できない場合は、[VM へのリモート デスクトップ接続のトラブルシューティング](../virtual-machines/windows/troubleshoot-rdp-connection.md)に関するページを参照してください。

## <a name="addremovecert"></a>信頼されたルート証明書を追加または削除する

信頼されたルート証明書を Azure に追加したり、Azure から削除したりできます。 信頼された証明書を削除すると、そのルート証明書から生成されたクライアント証明書は、ポイント対サイトを介して Azure に接続することができなくなります。 クライアントが接続できるようにするには、Azure で信頼されている証明書から生成された新しいクライアント証明書をインストールする必要があります。

### <a name="to-add-a-trusted-root-certificate"></a>信頼されたルート証明書を追加するには
信頼されたルート証明書 .cer ファイルを最大 20 個まで Azure に追加できます。 ルート証明書は次の手順で追加できます。

1. Azure に追加する新しいルート証明書を作成して準備します。 Base64 でエンコードされた X.509 (.cer) として公開キーをエクスポートし、テキスト エディターで開きます。 次の例で示すように値をコピーします。
   
  ![証明書](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

    > [!NOTE]
    > 証明書データをコピーするときはに、必ず、テキストを復帰や改行のない 1 つの連続した行としてコピーしてください。 復帰や改行を確認するには、テキスト エディターのビューを "記号を表示する/すべての文字を表示する" ように変更することが必要になる場合があります。
  >
  >

2. 証明書名とキーの情報を変数として指定しています。 次の例で示すように、情報は実際のものに置き換えてください。

  ```powershell
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
3. 新しいルート証明書を追加します。 一度に追加できる証明書は 1 つだけです。

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
4. 次の例を使用して、新しい証明書が正しく追加されたことを確認できます。

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```


### <a name="to-remove-a-trusted-root-certificate"></a>信頼されたルート証明書を削除するには


1. 変数を宣言します。

  ```powershell
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
2. 証明書を削除します。

  ```powershell
  Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
3. 次の例を使用して、証明書が正常に削除されたことを確認します。

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

## <a name="revoke"></a>クライアント証明書の失効
クライアント証明書は失効させることができます。 証明書失効リストを使用すると、個々のクライアント証明書に基づくポイント対サイト接続を選択して拒否することができます。 これは、信頼されたルート証明書を削除する場合とは異なります。 信頼されたルート証明書 .cer を Azure から削除すると、失効したルート証明書によって生成または署名されたすべてのクライアント証明書のアクセス権が取り消されます。 ルート証明書ではなくクライアント証明書を失効させることで、ルート証明書から生成されたその他の証明書は、その後も認証に使用できます。

一般的な方法としては、ルート証明書を使用してチームまたは組織レベルでアクセスを管理し、失効したクライアント証明書を使用して、個々のユーザーの細かいアクセス制御を構成します。

### <a name="to-revoke-a-client-certificate"></a>クライアント証明書を失効させるには

1. クライアント証明書の拇印を取得します。 詳細については、「[方法: 証明書のサムプリントを取得する](https://msdn.microsoft.com/library/ms734695.aspx)」を参照してください。
2. 情報をテキスト エディターにコピーし、文字列が 1 つにつながるようにスペースをすべて削除します。 これを次の手順で変数として宣言します。
3. 変数を宣言します。 必ず前の手順で取得した拇印を宣言してください。

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
4. 失効した証明書のリストに拇印を追加します。 拇印が追加されると、"Succeeded" と表示されます。

  ```powershell
  Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
  -Thumbprint $RevokedThumbprint1
  ```
5. 証明書失効リストに拇印が追加されたことを確認します。

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```
6. 拇印が追加された後は、証明書を接続に使用することができなくなります。 この証明書を使用して接続を試みたクライアントには、証明書が無効になっていることを示すメッセージが表示されます。

### <a name="to-reinstate-a-client-certificate"></a>クライアント証明書を回復するには
失効したクライアント証明書のリストから拇印を削除することで、クライアント証明書を回復できます。

1. 変数を宣言します。 回復する証明書用の適切な拇印を宣言していることを確認してください。

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
2. 証明書の失効リストから証明書の拇印を削除します。

  ```powershell
  Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
  ```
3. 失効リストから拇印が削除されているかどうかを確認します。

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```

## <a name="faq"></a>ポイント対サイト接続に関してよく寄せられる質問

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>次のステップ
接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) に関するページを参照してください。 ネットワークと仮想マシンの詳細については、「[Azure と Linux の VM ネットワークの概要](../virtual-machines/linux/azure-vm-network-overview.md)」を参照してください。

