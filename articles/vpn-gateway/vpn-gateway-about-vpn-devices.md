<properties 
   pageTitle="Azure Virtual Networks でのサイト間 VPN Gateway 接続の VPN デバイスについて | Microsoft Azure"
   description="S2S VPN Gateway 接続用の VPN デバイスおよび IPsec パラメーターについて説明します。サイト間接続は、ハイブリッド構成に使用できます。この記事には、VPN ゲートウェイ デバイスの構成手順およびサンプルへのリンクが含まれています。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/14/2015"
   ms.author="cherylmc" />

# サイト間 VPN Gateway 接続の VPN デバイスについて

サイト間 (S2S) VPN 接続を構成するには、VPN デバイスが必要です。サイト間接続は、ハイブリッド ソリューションを作成するときに、またはオンプレミスのネットワークと仮想ネットワークの間にセキュリティで保護された接続が必要な場合にいつでも使用することができます。この記事では、互換性のある VPN デバイス、および構成パラメーターについて説明します。サイト間接続を構成するときには、VPN デバイスに公開 IPv4 IP アドレスが必要であることに注意してください。

検証済みの VPN デバイスの表にデバイスが見つからない場合は、この記事の「未検証の VPN デバイス」セクションを参照してください。デバイスは引き続き Azure で動作する可能性があります。VPN デバイスのサポートは、デバイスの製造元に問い合わせてください。

**表を確認するときの注意事項:**

- 静的および動的ルーティングの用語に変更がありました。おそらく、両方の用語が見つかるでしょう。機能上の変更はありませんが、名前のみが変更されています。
	- 静的ルーティング = ポリシー ベース
	- 動的ルーティング = ルート ベース 
- 高性能 VPN ゲートウェイとルート ベース VPN ゲートウェイの仕様は、特に記載がない限り同じです。たとえば、ルート ベース VPN ゲートウェイと互換性がある検証済みの VPN デバイスは、新しい Azure 高性能 VPN ゲートウェイとも互換性があります。 


## 検証済みの VPN デバイス 

Microsoft では、デバイス ベンダーと協力して一連の標準的な VPN デバイスを検証しました。以下の一覧に含まれているデバイス ファミリ内のすべてのデバイスは、Azure VPN ゲートウェイで動作します。「[VPN ゲートウェイ](vpn-gateway-about-vpngateways.md)」の記事を参照して、構成するソリューションで作成する必要があるゲートウェイの種類を確認してください。

VPN デバイスを構成するには、適切なデバイス ファミリに対応するリンクを参照してください。



| **ベンダー名** | **デバイス ファミリ** | **OS の最小バージョン** | **ポリシー ベース** | **ルート ベース** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | AR シリーズ VPN ルーター | 2\.9.2 | 近日対応予定 | 互換性なし |
| Barracuda Networks, Inc. | Barracuda NG Firewall | Barracuda NG Firewall 5.4.3 | [Barracuda NG Firewall](https://techlib.barracuda.com/display/BNGV54/How%20to%20Configure%20an%20IPsec%20Site-to-Site%20VPN%20to%20a%20Windows%20Azure%20VPN%20Gateway)| 互換性なし |
| Barracuda Networks, Inc. | Barracuda Firewall | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | 互換性なし |
| Brocade | Vyatta 5400 vRouter | 仮想ルーター 6.6R3 GA | [構成の手順](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | 互換性なし |
| Check Point | セキュリティ ゲートウェイ | R75.40、R75.40VS | [構成の手順](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [構成の手順](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Cisco のサンプル](http://go.microsoft.com/fwlink/p/?LinkID=717348) | 互換性なし |
| Cisco | ASR | IOS 15.1 (ポリシー ベース)、IOS 15.2 (ルート ベース) | [Cisco のサンプル](http://go.microsoft.com/fwlink/p/?LinkID=717348) | [Cisco のサンプル](http://go.microsoft.com/fwlink/p/?LinkID=717348) |
| Cisco | ISR | IOS 15.0 (ポリシー ベース)、IOS 15.1 (ルート ベース) | [Cisco のサンプル](http://go.microsoft.com/fwlink/p/?LinkID=717348) | [Cisco のサンプル](http://go.microsoft.com/fwlink/p/?LinkID=717348) |
| Citrix | CloudBridge MPX アプライアンス、または VPX 仮想アプライアンス | 該当なし | [統合の手順](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration) | 互換性なし |
| Dell SonicWALL | TZ シリーズ、NSA シリーズ、SuperMassive シリーズ、E-class NSA シリーズ | SonicOS 5.8.x、[SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850)、[SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) | [指示 - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [指示 - SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) | [指示 - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [指示 - SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 | BIG-IP シリーズ | 該当なし | [構成の手順](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | 互換性なし |
| Fortinet | FortiGate | FortiOS 5.0.7 | [構成の手順](http://docs.fortinet.com/fortigate/admin-guides) | [構成の手順](http://docs.fortinet.com/fortigate/admin-guides) |
| Internet Initiative Japan (IIJ) | SEIL シリーズ | SEIL/x 4.60、SEIL/B1 4.60、SEIL/x86 3.20 | [構成の手順](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | 互換性なし |
| Juniper | SRX | JunOS 10.2 (ポリシー ベース)、JunOS 11.4 (ルート ベース) | [Juniper のサンプル](http://go.microsoft.com/fwlink/p/?LinkId=717760) | [Juniper のサンプル](http://go.microsoft.com/fwlink/p/?LinkId=717760) |
| Juniper | J シリーズ | JunOS 10.4r9 (ポリシー ベース)、JunOS 11.4 (ルート ベース) | [Juniper のサンプル](http://go.microsoft.com/fwlink/p/?LinkId=717760) | [Juniper のサンプル](http://go.microsoft.com/fwlink/p/?LinkId=717760) |
| Juniper | ISG | ScreenOS 6.3 (ポリシー ベースおよびルート ベース) | [Juniper のサンプル](http://go.microsoft.com/fwlink/p/?LinkId=717760) | [Juniper のサンプル](http://go.microsoft.com/fwlink/p/?LinkId=717760) |
| Juniper | SSG | ScreenOS 6.2 (ポリシー ベースおよびルート ベース) | [Juniper のサンプル](http://go.microsoft.com/fwlink/p/?LinkId=717760) | [Juniper のサンプル](http://go.microsoft.com/fwlink/p/?LinkId=717760) |
| Microsoft | ルーティングとリモート アクセス サービス | Windows Server 2012 | 互換性なし | [Microsoft のサンプル](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Openswan | Openswan | 2\.6.32 | (近日対応予定) | 互換性なし |
| Palo Alto Networks | PAN-OS 5.0 またはそれ以上を実行しているすべてのデバイス | PAN-OS 5x またはそれ以上 | [Palo Alto Networks](https://support.paloaltonetworks.com/) | 互換性なし |
| Watchguard | すべて | Fireware XTM v11.x | [構成の手順](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | 互換性なし |


## 未検証の VPN デバイス

検証済みの VPN デバイスの表 (上記) にデバイスが見つからない場合は、引き続きサイト間接続で動作する可能性があります。使用している VPN デバイスが、「[VPN ゲートウェイについて](vpn-gateway-about-vpngateways.md#gateway-requirements)」の記事の「ゲートウェイの要件」セクションに記載されている最小要件を満たしていることを確認してください。最小要件を満たしていれば、そのデバイスは VPN ゲートウェイでも正常に動作します。詳細なサポートおよび構成手順については、デバイスの製造元にお問い合わせください。


## デバイス構成のサンプルの編集

提供されている VPN デバイス構成のサンプルをダウンロードしたら、一部の値を使用している環境の設定を反映した値に置換する必要があります。

**サンプルを編集するには:**

1. メモ帳を使用してサンプルを開きます。 
1. 使用している環境に関連する値を含む <*テキスト*> 文字列をすべて検索して置き換えます。< and > を必ず含めてください。名前を指定する場合、選択する名前は一意である必要があります。コマンドが機能しない場合は、デバイスの製造元のドキュメントを参照してください。

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

| **プロパティ** | **ポリシー ベース** | **ルート ベース、および Standard または 高性能 VPN ゲートウェイ** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| IKE のバージョン | IKEv1 | IKEv2 |
| Diffie-hellman グループ | グループ 2 (1024 ビット) | グループ 2 (1024 ビット) |
| 認証方法 | 事前共有キー | 事前共有キー |
| 暗号化アルゴリズム | AES256 AES128 3DES | AES256 3DES |
| ハッシュ アルゴリズム | SHA1(SHA128) | SHA1(SHA128) |
| フェーズ 1 のセキュリティ アソシエーション (SA) の有効期間 (時間) | 28,800 秒 | 28,800 秒 |


### IKE フェーズ 2 セットアップ

| **プロパティ** | **ポリシー ベース** | **ルート ベース、および Standard または 高性能 VPN ゲートウェイ** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| IKE のバージョン | IKEv1 | IKEv2 |
| ハッシュ アルゴリズム | SHA1(SHA128) | SHA1(SHA128) |
| フェーズ 2 のセキュリティ アソシエーション (SA) の有効期間 (時間) | 3,600 秒 | - | | フェーズ 2 のセキュリティ アソシエーション (SA) の有効期間 (スループット) | 102,400,000 KB | - | | IPsec SA 暗号化および認証のプラン (優先度順) | 1.ESP-AES256 2.ESP-AES128 3.ESP-3DES 4.N/A | *ルート ベース ゲートウェイ IPsec セキュリティ アソシエーション (SA) のプラン*を参照 (下記) | | Perfect Forward Secrecy (PFS) | なし | あり (DH Group1) | | Dead Peer Detection | サポートなし | サポートあり |

### ルート ベース ゲートウェイ IPsec セキュリティ アソシエーション (SA) のプラン

次の表は、IPsec SA 暗号化および認証のプランを一覧表示しています。プランは提示される、または受け入れられる優先度順に表示されています。

| **IPsec SA 暗号化および認証のプラン** | **発信側としての Azure ゲートウェイ** | **応答側としての Azure ゲートウェイ** |
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


- ルート ベースおよび高性能 VPN ゲートウェイで IPsec ESP NULL 暗号化を指定することができます。Null ベースの暗号化では、転送中のデータ保護は提供されません。そのため、最大のスループットおよび最小の待機時間が必要な場合にのみ使用する必要があります。クライアントは、VNet 間の通信シナリオ、または暗号化がソリューションの他の場所に適用されている場合に、この暗号化の使用を選択することができます。

- インターネット経由のクロスプレミス接続では、重要な通信のセキュリティを確保するため、上記の表にある暗号化およびハッシュ アルゴリズムによる既定の Azure VPN Gateway 設定を使用してください。

<!---HONumber=AcomDC_0224_2016-->