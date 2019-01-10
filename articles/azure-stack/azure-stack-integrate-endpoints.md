---
title: Azure Stack とデータセンターの統合 - エンドポイントの公開 | Microsoft Docs
description: データセンターで Azure Stack のエンドポイントを公開する方法を学習します
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 12/06/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: ''
ms.openlocfilehash: 23c2206a873dc37f5b4f40e0c692e6a35869c419
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106479"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack とデータセンターの統合 - エンドポイントの公開

Azure Stack は、そのインフラストラクチャ ロールのために仮想 IP アドレス (VIP) を設定します。 この VIP はパブリック IP アドレス プールから割り当てられます。 各 VIP は、ソフトウェア定義のネットワーク レイヤーで、アクセス制御リスト (ACL) で保護されます。 ACL は、ソリューションをさらに強化するために、さまざまな物理スイッチ (TOR や BMC) でも使われます。 デプロイ時に指定された外部 DNS ゾーン内のエンドポイントごとに DNS エントリが作成されます。


次のアーキテクチャ図は、さまざまなネットワーク レイヤーと ACL を示しています。

![構造の画像](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>ポートとプロトコル (受信)

Azure Stack エンドポイントを外部ネットワークに公開するには、一連のインフラストラクチャ VIP が必要です。 "*エンドポイント (VIP)*" の表は、各エンドポイント、必要なポート、およびプロトコルを示しています。 SQL のリソース プロバイダーなど、追加のリソース プロバイダーを必要とするエンドポイントについては、特定のリソース プロバイダーの展開に関するドキュメントを参照してください。

社内インフラストラクチャの VIP は Azure Stack の発行には不要なため、記載されていません。

> [!Note]  
> ユーザーの VIP は動的で、Azure Stack オペレーターによって管理されず、ユーザー自身が定義します。

|エンドポイント (VIP)|DNS ホスト A レコード|Protocol|ポート|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|ポータル (管理者)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|AdminHosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (管理者)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|ポータル (ユーザー)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (ユーザー)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|証明書の失効リスト|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP と UDP|53|
|ホスティング | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault (ユーザー)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault (管理者)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|ストレージ キュー|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|ストレージ テーブル|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|ストレージ BLOB|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL リソース プロバイダー|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL リソース プロバイダー|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP、UDP|21、1021、10001-10100 (FTP)<br>990 (FTPS)|
|VPN ゲートウェイ|     |     |[VPN Gateway に関する FAQ を参照してください](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability)。|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>ポートと URL (送信)

Azure Stack は、透過的なプロキシ サーバーのみをサポートします。 透過プロキシから従来のプロキシ サーバーへのアップリンクが存在するデプロイ環境では、次のポートと URL に外部への通信を許可する必要があります。

> [!Note]  
> Azure Stack では、Express Route を利用して､次の表に示す Azure サービスに到達することはできません。

|目的|URL|Protocol|ポート|
|---------|---------|---------|---------|
|ID|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com<br>office.com|HTTP<br>HTTPS|80<br>443|
|Marketplace シンジケーション|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|パッチと更新プログラム|https://&#42;.azureedge.net|HTTPS|443|
|登録|https://management.azure.com|HTTPS|443|
|使用法|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net|HTTPS|443|
|Windows Defender|.wdcp.microsoft.com<br>.wdcpalt.microsoft.com<br>*.updates.microsoft.com<br>*.download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>https://www.microsoft.com/pkiops/crl<br>https://www.microsoft.com/pkiops/certs<br>https://crl.microsoft.com/pki/crl/products<br>https://www.microsoft.com/pki/certs<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|
|NTP|(デプロイに提供される NTP サーバーの IP)|UDP|123|
|DNS|(デプロイに提供される DNS サーバーの IP)|TCP<br>UDP|53|
|CRL|(証明書上で CRL 配布ポイントの下にある URL)|HTTP|80|
|インフラストラクチャ バックアップ|(外部ターゲット ファイル サーバーの IP または FQDN)|SMB|445|
|     |     |     |     |

> [!Note]  
> 送信 URL は Azure Traffic Manager を使用して負荷分散され、地理的な場所に基づいて可能な限り最適な接続が提供されます。 URL を負荷分散することで、Microsoft は、顧客に影響を与えることなくバックエンド エンドポイントを更新および変更することができます。 Microsoft では、負荷分散される URL の IP アドレスのリストを共有していません。 IP ではなく URL に基づくフィルターをサポートするデバイスを使用する必要があります。

> [!Note]  
> 1809 では、インフラストラクチャ バックアップ サービスは、パブリック VIP ネットワークの外部ファイル サーバーに通信します。 1809 より前には、このサービスはパブリック インフラストラクチャ ネットワーク経由で通信していました。 ご使用の環境でパブリック VIP ネットワークからインフラストラクチャ リソースにアクセスできない場合には、Azure Stack 用の最新の [1809 修正プログラム](azure-stack-update-1809.md#post-update-steps)を適用してください。 この修正プログラムにより、インフラストラクチャ バックアップ サービスはパブリック インフラストラクチャ ネットワークに戻されます。 1811 では、1809 修正プログラムを適用すると、インフラストラクチャ バックアップ サービスはパブリック インフラストラクチャ ネットワーク上に残ります。 この修正プログラムを適用しないと、更新プログラムはそのサービスをパブリック インフラストラクチャ ネットワークに戻します。

## <a name="next-steps"></a>次の手順

[Azure Stack PKI の要件](azure-stack-pki-certs.md)
