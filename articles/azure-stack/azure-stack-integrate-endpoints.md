---
title: "Azure Stack とデータセンターの統合 - エンドポイントの公開"
description: "データセンターで Azure Stack のエンドポイントを公開する方法を学習します"
services: azure-stack
author: jeffgilb
ms.service: azure-stack
ms.topic: article
ms.date: 02/16/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: 
ms.openlocfilehash: 8af533147f3cc12f2334a43e7b672c69d0d25802
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack とデータセンターの統合 - エンドポイントの公開
Azure Stack は、そのインフラストラクチャ ロールのためにいくつかの仮想 IP アドレス (VIP) を設定します。 この VIP はパブリック IP アドレス プールから割り当てられます。 各 VIP は、ソフトウェア定義のネットワーク レイヤーで、アクセス制御リスト (ACL) で保護されます。 ACL は、ソリューションをさらに強化するために、さまざまな物理スイッチ (TOR や BMC) でも使われます。 デプロイ時に指定した外部 DNS ゾーン内のエンドポイントごとに DNS エントリが作成されます。


次のアーキテクチャ図は、さまざまなネットワーク レイヤーと ACL を示しています。

![アーキテクチャ図](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>ポートとプロトコル (受信)

Azure Stack エンドポイントを外部ネットワークに公開するために必要なインフラストラクチャ VIP を次に示します。 この一覧では、各エンドポイント、必要なポート、そしてプロトコルを示しています。 特定のリソースプロバイダーの展開に関するドキュメントでは、SQL のリソースプロバイダーなどのような、追加のリソースプロバイダーに必要なエンドポイントについて説明します。

社内インフラストラクチャの VIP は Azure Stack の発行には不要なため、記載されていません。

> [!NOTE]
> ユーザーの VIP は動的で、Azure Stack オペレーターによって管理されず、ユーザー自身が定義します。


|エンドポイント (VIP)|DNS ホスト A レコード|プロトコル|ポート|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|ポータル (管理者)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Azure Resource Manager (管理者)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|ポータル (ユーザー)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
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
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP、UDP|21、1021、10001-101000 (FTP)<br>990 (FTPS)|

## <a name="ports-and-urls-outbound"></a>ポートと URL (送信)

Azure Stack は、透過的なプロキシ サーバーのみをサポートします。 透過プロキシから従来のプロキシ サーバーへのアップリンクが存在するデプロイ環境では、次のポートと URL に外部への通信を許可する必要があります。


|目的|URL|プロトコル|ポート|
|---------|---------|---------|---------|
|ID|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|Marketplace シンジケーション|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|パッチと更新プログラム|https://&#42;.azureedge.net|HTTPS|443|
|登録|https://management.azure.com|HTTPS|443|
|使用法|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|


## <a name="next-steps"></a>次の手順
[Azure Stack PKI の要件](azure-stack-pki-certs.md)