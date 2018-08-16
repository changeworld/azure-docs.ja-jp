---
title: Azure Stack とデータセンターの統合 - エンドポイントの公開 | Microsoft Docs
description: データセンターで Azure Stack のエンドポイントを公開する方法を学習します
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/02/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: ''
ms.openlocfilehash: c5d0bc6a0e7e885f61ba28be0575697b03fece09
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480709"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack とデータセンターの統合 - エンドポイントの公開
Azure Stack は、そのインフラストラクチャ ロールのために仮想 IP アドレス (VIP) を設定します。 この VIP はパブリック IP アドレス プールから割り当てられます。 各 VIP は、ソフトウェア定義のネットワーク レイヤーで、アクセス制御リスト (ACL) で保護されます。 ACL は、ソリューションをさらに強化するために、さまざまな物理スイッチ (TOR や BMC) でも使われます。 デプロイ時に指定された外部 DNS ゾーン内のエンドポイントごとに DNS エントリが作成されます。


次のアーキテクチャ図は、さまざまなネットワーク レイヤーと ACL を示しています。

![構造の画像](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>ポートとプロトコル (受信)

Azure Stack エンドポイントを外部ネットワークに公開するには、一連のインフラストラクチャ VIP が必要です。 "*エンドポイント (VIP)*" の表は、各エンドポイント、必要なポート、およびプロトコルを示しています。 SQL のリソース プロバイダーなど、追加のリソース プロバイダーを必要とするエンドポイントについては、特定のリソース プロバイダーの展開に関するドキュメントを参照してください。

社内インフラストラクチャの VIP は Azure Stack の発行には不要なため、記載されていません。

> [!NOTE]
> ユーザーの VIP は動的で、Azure Stack オペレーターによって管理されず、ユーザー自身が定義します。


|エンドポイント (VIP)|DNS ホスト A レコード|プロトコル|ポート|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|ポータル (管理者)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Azure Resource Manager (管理者)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|ポータル (ユーザー)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (ユーザー)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|証明書の失効リスト|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP と UDP|53|
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


|目的|URL|プロトコル|ポート|
|---------|---------|---------|---------|
|ID|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com<br>office.com|HTTP<br>HTTPS|80<br>443|
|Marketplace シンジケーション|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|パッチと更新プログラム|https://&#42;.azureedge.net|HTTPS|443|
|登録|https://management.azure.com|HTTPS|443|
|使用法|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net|HTTPS|443|
|Windows Defender|.wdcp.microsoft.com<br>.wdcpalt.microsoft.com<br>*.updates.microsoft.com<br>*.download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>http://www.microsoft.com/pkiops/crl<br>http://www.microsoft.com/pkiops/certs<br>http://crl.microsoft.com/pki/crl/products<br>http://www.microsoft.com/pki/certs<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|
|NTP|     |UDP|123|
|DNS|     |TCP<br>UDP|53|
|     |     |     |     |



## <a name="next-steps"></a>次の手順

[Azure Stack PKI の要件](azure-stack-pki-certs.md)
