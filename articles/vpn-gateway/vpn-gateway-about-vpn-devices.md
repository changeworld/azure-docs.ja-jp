<properties 
   pageTitle="サイト間 Azure 仮想ネットワークの接続の VPN デバイスについて | Microsoft Azure"
   description="Azure 仮想ネットワークのサイト間 VPN Gateway 接続用の VPN デバイスおよび IPsec パラメーターについて理解する。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/07/2015"
   ms.author="cherylmc" />

# サイト間仮想ネットワークの接続の VPN デバイスについて

サイト間 VPN 接続を構成するには、VPN デバイスが必要です。サイト間接続は、ハイブリッドなクラウド ソリューションを作成する際に、またはオンプレミスのネットワークと仮想ネットワークの間にセキュリティで保護された接続が必要な場合にいつでも使用することができます。この記事では、互換性のある VPN デバイス、および構成パラメーターについて説明します。

サイト間接続には、互換性のある VPN デバイスに加えて、公開 IPv4 IP アドレスも必要です。さらに、ソリューションをサポートするゲートウェイの種類も選択する必要があります。すべての VPN デバイスがすべてのゲートウェイの種類をサポートするわけではありません。ソリューションを作成する必要があるゲートウェイの種類を確認するには、[VPN Gataway](vpn-gateway-about-vpngateways.md) を参照してください。



## VPN デバイス

マイクロソフトでは、デバイス ベンダーと協力して複数の標準的なサイト間 (S2S) VPN デバイスを検証しました。Virtual Network と互換性があることがわかっている VPN デバイスの一覧は、以下の[互換性のある VPN デバイス](#compatible-vpn-devices)のセクションを参照してください。この一覧に含まれているデバイス ファミリ内のすべてのデバイスが、Azure VPN ゲートウェイで動作する必要があります。VPN デバイスを構成するには、適切なデバイス ファミリに対応するデバイスの構成サンプルを参照してください。

高パフォーマンスの VPN Gateway と動的ルーティング VPN Gateway の仕様は、特に記載がない場合を除き同じです。たとえば、Azure 動的ルーティング VPN Gateway と互換性がある検証済みの VPN デバイスは、新しい Azure の高パフォーマンスの VPN Gateway とも互換性があります。


### 互換性のある VPN デバイス

特定の VPN デバイス ファミリを共同で評価するため、複数の VPN デバイスのベンダーを使用しました。以下のセクションでは、マイクロソフトの VPN Gataway で動作するすべてのデバイス ファミリの一覧を示します。表示されているデバイス ファミリのメンバーであるすべてのデバイスは、例外が記載されていない限り、動作することがわかっています。デバイスが一覧に表示されていない場合は、「[デバイスが互換一覧にない場合](#devices-not-on-the-compatible-list)」を参照してください。



VPN デバイスのサポートは、デバイスの製造元に問い合わせてください。



| **ベンダー名** | **デバイス ファミリ** | **OS の最小バージョン** | **静的ルーティング** | **動的ルーティング** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | AR シリーズ VPN ルーター | 2\.9.2 | 近日対応予定 | 互換性なし |
| Barracuda Networks, Inc. | Barracuda NG Firewall | Barracuda NG Firewall 5.4.3 | [Barracuda NG Firewall](https://techlib.barracuda.com/display/BNGV54/How%20to%20Configure%20an%20IPsec%20Site-to-Site%20VPN%20to%20a%20Windows%20Azure%20VPN%20Gateway)| 互換性なし |
| Barracuda Networks, Inc. | Barracuda Firewall | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | 互換性なし |
| Brocade | Vyatta 5400 vRouter | 仮想ルーター 6.6R3 GA | [構成の手順](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | 互換性なし |
| Check Point | セキュリティ ゲートウェイ | R75.40、R75.40VS | [構成の手順](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [構成の手順](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Cisco ASA サンプル](https://msdn.microsoft.com/library/azure/dn133793.aspx) | 互換性なし |
| Cisco | ASR | IOS 15.1 (静的)、IOS 15.2 (動的) | [Cisco ASR サンプル](https://msdn.microsoft.com/library/azure/dn133802.aspx) | [Cisco ASR サンプル](https://msdn.microsoft.com/library/azure/dn133802.aspx) |
| Cisco | ISR | IOS 15.0 (静的)、IOS 15.1 (動的) | [Cisco ISR サンプル](https://msdn.microsoft.com/library/azure/dn133800.aspx) | [Cisco ISR サンプル](https://msdn.microsoft.com/library/azure/dn133800.aspx) |
| Citrix | CloudBridge MPX アプライアンス、または VPX 仮想アプライアンス | 該当なし | [統合の手順](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration) | 互換性なし |
| Dell SonicWALL | TZ シリーズ、NSA シリーズ、SuperMassive シリーズ、E-class NSA シリーズ | SonicOS 5.8.x、SonicOS 5.9.x、SonicOS 6.x | [構成の手順](https://www.sonicwall.com/app/projects/file_downloader/document_lib.php?t=TN&id=348) | 互換性なし |
| F5 | BIG-IP シリーズ | 該当なし | [構成の手順](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | 互換性なし |
| Fortinet | FortiGate | FortiOS 5.0.7 | [構成の手順](http://docs.fortinet.com/fortigate/admin-guides) | [構成の手順](http://docs.fortinet.com/fortigate/admin-guides) |
| Internet Initiative Japan (IIJ) | SEIL シリーズ | SEIL/x 4.60、SEIL/B1 4.60、SEIL/x86 3.20 | [構成の手順](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | 互換性なし |
| Juniper | SRX | JunOS 10.2 (静的)、JunOS 11.4 (動的) | [Juniper SRX サンプル](https://msdn.microsoft.com/library/azure/dn133794.aspx) | [Juniper SRX サンプル](https://msdn.microsoft.com/library/azure/dn133794.aspx) |
| Juniper | J シリーズ | JunOS 10.4r9 (静的)、JunOS 11.4 (動的) | [Juniper J シリーズ サンプル](https://msdn.microsoft.com/library/azure/dn133799.aspx) | [Juniper J シリーズ サンプル](https://msdn.microsoft.com/library/azure/dn133799.aspx) |
| Juniper | ISG | ScreenOS 6.3 (静的および動的) | [Juniper ISG サンプル](https://msdn.microsoft.com/library/azure/dn133797.aspx) | [Juniper ISG サンプル](https://msdn.microsoft.com/library/azure/dn133797.aspx) |
| Juniper | SSG | ScreenOS 6.2 (静的および動的) | [Juniper SSG サンプル](https://msdn.microsoft.com/library/azure/dn133796.aspx) | [Juniper SSG サンプル](https://msdn.microsoft.com/library/azure/dn133796.aspx) |
| Microsoft | ルーティングとリモート アクセス サービス | Windows Server 2012 | 互換性なし | [ルーティングとリモート アクセス サービス (RRAS) のサンプル](https://msdn.microsoft.com/library/azure/dn133801.aspx) |
| Openswan | Openswan | 2\.6.32 | (近日対応予定) | 互換性なし |
| Palo Alto Networks | PAN-OS 5.0 またはそれ以上を実行しているすべてのデバイス | PAN-OS 5x またはそれ以上 | [Palo Alto Networks](https://support.paloaltonetworks.com/) | 互換性なし |
| Watchguard | すべて | Fireware XTM v11.x | [構成の手順](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | 互換性なし |


### デバイスが互換一覧にない場合


既知の互換性がある VPN デバイス一覧にないデバイスを VPN 接続で使用する場合は、[ゲートウェイの要件](vpn-gateway-about-vpngateways.md#gateway-requirements)テーブルに記載されている最小要件を満たしていることを確認する必要があります。最小要件を満たしていれば、そのデバイスも Virtual Network で動作します。詳細なサポートおよび構成手順については、デバイスの製造元にお問い合わせください。


## デバイス構成のサンプルの編集

提供されている VPN デバイス構成のサンプルをダウンロードしたら、一部の値をお使いの環境の設定を反映した値に置換する必要があります。

**サンプルを編集するには:**

1. メモ帳を使用してサンプルを開きます。 
1. お使いの環境に関連する値を含む <*テキスト*> 文字列をすべて検索して置き換えます。< and > を必ず含めてください。名前を指定する場合、選択する名前は一意である必要があります。コマンドが機能しない場合は、デバイスの製造元のドキュメントを参照してください。

| **サンプル テキスト** | **次に変更** |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP\_OnPremisesNetwork&gt; | このオブジェクトに選択した名前。例: myOnPremisesNetwork |
| &lt;RP\_AzureNetwork&gt; | このオブジェクトに選択した名前。例: myAzureNetwork |
| &lt;RP\_AccessList&gt; | このオブジェクトに選択した名前。例: myAzureAccessList |
| &lt;RP\_IPSecTransformSet&gt; | このオブジェクトに選択した名前。例: myIPSecTransformSet |
| &lt;RP\_IPSecCryptoMap&gt; | このオブジェクトに選択した名前。例: myIPSecCryptoMap |
| &lt;SP\_AzureNetworkIpRange&gt; | 範囲を指定します。例：192.168.0.0 |
| &lt;SP\_AzureNetworkSubnetMask&gt; | サブネット マスクを指定します。例: 255.255.0.0 |
| &lt;SP\_OnPremisesNetworkIpRange&gt; | オンプレミスの範囲を指定します。例: 10.2.1.0 |
| &lt;SP\_OnPremisesNetworkSubnetMask&gt; | オンプレミスのサブネット マスクを指定します。例: 255.255.255.0 |
| &lt;SP\_AzureGatewayIpAddress&gt; | この情報は仮想ネットワークに固有であり、**ゲートウェイの IP アドレス**として管理ポータルに存在しています。 |
| &lt;SP\_PresharedKey&gt; | この情報は仮想ネットワークに固有であり、[キーの管理] として管理ポータルに存在しています。 |



## IPsec パラメーター

### IKE フェーズ 1 セットアップ

| **プロパティ** | **静的ルーティング VPN Gateway** | **動的ルーティング VPN Gateway と標準または高パフォーマンスの VPN Gateway** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| IKE のバージョン | IKEv1 | IKEv2 |
| Diffie-hellman グループ | グループ 2 (1024 ビット) | グループ 2 (1024 ビット) |
| 認証方法 | 事前共有キー | 事前共有キー |
| 暗号化アルゴリズム | AES256 AES128 3DES | AES256 3DES |
| ハッシュ アルゴリズム | SHA1(SHA128) | SHA1(SHA128) |
| フェーズ 1 のセキュリティ アソシエーション (SA) の有効期間 (時間) | 28,800 秒 | 28,800 秒 |


### IKE フェーズ 2 セットアップ

| **プロパティ** | **静的ルーティング VPN Gateway** | **動的ルーティング VPN Gateway と標準または高パフォーマンスの VPN Gateway** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| IKE のバージョン | IKEv1 | IKEv2 |
| ハッシュ アルゴリズム | SHA1(SHA128) | SHA1(SHA128) |
| フェーズ 2 のセキュリティ アソシエーション (SA) の有効期間 (時間) | 3,600 秒 | - | | フェーズ 2 のセキュリティ アソシエーション (SA) の有効期間 (スループット) | 102,400,000 KB | - | | IPsec SA 暗号化および認証のプラン (優先度順) | 1.ESP-AES256 2.ESP-AES128 3.ESP-3DES 4.N/A | 動的ルーティング ゲートウェイ IPsec セキュリティ アソシエーション (SA) のプランを参照 | | Perfect Forward Secrecy (PFS) | なし | あり (DH Group1) | | Dead Peer Detection | サポートなし | サポートあり |

### 動的ルーティング ゲートウェイ IPsec セキュリティ アソシエーション (SA) のプラン

次の表は、IPsec SA 暗号化および認証のプランを一覧表示しています。プランは提示される、または受け入れられる優先度順に表示されています。

| **IPsec SA 暗号化および認証のプラン** | **発信側としての Azure ゲートウェイ** | 応答側としての Azure ゲートウェイ |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1 | ESP AES\_256 SHA | ESP AES\_128 SHA |
| 2 | ESP AES\_128 SHA | ESP 3\_DES MD5 |
| 3 | ESP 3\_DES MD5 | ESP 3\_DES SHA |
| 4 | ESP 3\_DES SHA | AH SHA1 (ESP AES\_128 null HMAC) |
| 5 | AH SHA1 (ESP AES\_256 null HMAC) | AH SHA1 (ESP 3\_DES null HMAC) |
| 6 | AH SHA1 (ESP AES\_128 null HMAC) | AH MD5 (ESP 3\_DES null HMAC)、有効期間の提示なし |
| 7 | AH SHA1 (ESP 3\_DES null HMAC) | AH SHA1 (ESP 3\_DES SHA1)、有効期間なし |
| 8 | AH MD5 (ESP 3\_DES null HMAC)、有効期間の提示なし | AH MD5 (ESP 3\_DES MD5)、有効期間なし |
| 9 | AH SHA1 (ESP 3\_DES SHA1)、有効期間なし | ESP DES MD5 |
| 10 | AH MD5 (ESP 3\_DES MD5)、有効期間なし | ESP DES SHA1、有効期間なし |
| 11 | ESP DES MD5 | AH SHA1 (ESP DES null HMAC)、有効期間の提示なし |
| 12 | ESP DES SHA1、有効期間なし | AH MD5 (ESP DES null HMAC)、有効期間の提示なし |
| 13 | AH SHA1 (ESP DES null HMAC)、有効期間の提示なし | AH SHA1 ( ESP DES SHA1)、有効期間なし |
| 14 | AH MD5 (ESP DES null HMAC)、有効期間の提示なし | AH MD5 (ESP DES MD5)、有効期間なし |
| 15 | AH SHA1 ( ESP DES SHA1)、有効期間なし | ESP SHA、有効期間なし |
| 16 | AH MD5 (ESP DES MD5)、有効期間なし | ESP MD5、有効期間なし |
| 17 | - | AH SHA、有効期間なし | | 18 | - | AH MD5、有効期間なし |




- Azure ネットワークの VNet 間の接続を目的として、動的ルーティングと高パフォーマンスの VPN Gateway を使用した IPsec ESP NULL 暗号化を指定することができます。 

- インターネット経由のクロスプレミス接続では、重要な通信のセキュリティを確保するため、上記の表にある暗号化およびハッシュ アルゴリズムによる既定の Azure VPN Gateway 設定を使用してください。

## 次のステップ


VPN Gateway について詳しくは、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。

サイト間 VPN を構成するには、「[サイト間 VPN 接続を使用した仮想ネットワークの作成](vpn-gateway-site-to-site-create.md)」を参照してください。

<!---HONumber=August15_HO7-->