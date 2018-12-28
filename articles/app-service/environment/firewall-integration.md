---
title: App Service Environment の送信トラフィックのロックダウン - Azure
description: Azure Firewall と統合して送信トラフィックをセキュリティで保護する方法について説明します
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 52051ea221a3d49d86cc6b95e020e1075ce8cba2
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275552"
---
# <a name="locking-down-an-app-service-environment"></a>App Service 環境をロックする

App Service Environment (ASE) が適切に動作するには、アクセスする必要がある外部の依存関係が複数あります。 ASE は、お客様の Azure Virtual Network (VNet) 内にあります。 お客様は、ASE の依存トラフィックを許可する必要があります。これは、自社の VNet からのすべての送信をロックしたいお客様にとって問題です。

ASE が持っている受信依存関係は複数あります。 受信管理トラフィックはファイアウォール デバイスを介して送信できません。 このトラフィックのソース アドレスは既知であり、「[App Service Environment の管理アドレス](https://docs.microsoft.com/azure/app-service/environment/management-addresses)」ドキュメントで公開されています。 その情報を使用してネットワーク セキュリティ グループ ルールを作成し、受信トラフィックをセキュリティで保護することができます。

ASE の送信依存関係は、ほぼすべて、背後に静的アドレスがない FQDN を使用して定義されています。 静的アドレスがないということは、ネットワーク セキュリティ グループ (NSG) を使用して ASE からの送信トラフィックをロックできないことを意味します。 アドレスは頻繁に変わるので、現在の解決策に基づいてルールを設定し、それを使用して NSG を作成することができません。 

送信アドレスをセキュリティで保護する解決策は、ドメイン名に基づいて送信トラフィックを制御できるファイアウォール デバイスの使用方法にあります。 Azure Firewall では、宛先の FQDN に基づいて送信 HTTP および HTTPS トラフィックを制限できます。  

## <a name="configuring-azure-firewall-with-your-ase"></a>ASE に合わせて Azure Firewall を構成する 

Azure Firewall を使用して ASE からの送信をロックする手順は次のとおりです。

1. ASE が展開されている、または展開される予定の VNet に Azure Firewall を作成します。 [Azure Firewall のドキュメント](https://docs.microsoft.com/azure/firewall/)
2. Azure Firewall UI から、App Service Environment FQDN タグを選択します
3. インターネットの次ホップがある [App Service Environment 管理アドレス]( https://docs.microsoft.com/azure/app-service/environment/management-addresses)から管理アドレスを使用してルート テーブルを作成します。 ルート テーブル エントリは、非対称ルーティングの問題を回避するために必要です。
4. インターネットの次ホップがある IP アドレスの依存関係に、以下に示す IP アドレスの依存関係のルートを追加します。
5. 0.0.0.0/0 のルート テーブルに、次ホップが Azure Firewall であるルートを追加します。
6. Azure SQL と Azure Storage に到達する ASE サブネットのサービス エンドポイントを作成します。
7. 作成したルート テーブルを ASE サブネットに割り当てます。

## <a name="application-traffic"></a>アプリケーション トラフィック 

上記の手順で、ASE が問題なく動作するようになります。 ただし、実際のアプリケーションのニーズに合わせて構成する必要があります。 Azure Firewall を使用して構成された ASE 内のアプリケーションには 2 つの問題があります。  

- アプリケーション依存関係の FQDN を Azure Firewall またはルート テーブルに追加する必要があります
- 非対称ルーティングの問題を回避するために、トラフィックの送信元のアドレスに対してルートを作成する必要があります

アプリケーションに依存関係がある場合は、それらを Azure Firewall に追加する必要があります。 その他すべてに関する HTTP/HTTPS トラフィックとネットワークのルールを許可するようにアプリケーション ルールを作成します。 

アプリケーション要求トラフィックの送信元であるアドレス範囲がわかっている場合は、そのアドレス範囲を ASE サブネットに割り当てられているルート テーブルに追加することができます。 アドレス範囲が大きい場合や指定されていない場合は、Application Gateway などのネットワーク アプライアンスを使用して、ルート テーブルに追加するアドレスを 1 つ指定することができます。 ILB ASE に合わせて Application Gateway を構成する方法の詳細については、[ILB ASE を Application Gateway と統合する方法](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)に関するページを参照してください。


## <a name="dependencies"></a>依存関係

Azure App Service には多くの外部依存関係があります。 それらはいくつかの主要分野に分類することができます。

- 送信ネットワーク トラフィックをロックする場合は、サービス エンドポイント対応サービスにサービス エンドポイントを設定する必要があります。
- IP アドレス エンドポイントは、ドメイン名を使用してアドレスが指定されていません。 これは、すべての HTTPS トラフィックがドメイン名を使用することを想定しているファイアウォール デバイスの場合に問題になる可能性があります。 IP アドレス エンドポイントは、ASE サブネットに設定されているルート テーブルに追加する必要があります。
- FQDN HTTP/HTTPS エンドポイントは、ファイアウォール デバイスに配置することができます。
- ワイルドカード HTTP/HTTPS エンドポイントは、いくつかの修飾子に基づき、ASE によって異なる依存関係になります。 
- Linux の依存関係は、ASE に Linux アプリをデプロイする場合にのみ考慮する必要があります。 Linux アプリを ASE に展開していない場合は、これらのアドレスをファイアウォールに追加する必要はありません。 


#### <a name="service-endpoint-capable-dependencies"></a>サービス エンドポイント対応の依存関係 

| エンドポイント |
|----------|
| Azure SQL |
| Azure Storage |
| Azure KeyVault |


#### <a name="ip-address-dependencies"></a>IP アドレスの依存関係 

| エンドポイント |
|----------|
| 40.77.24.27:443 |
| 13.82.184.151:443 |
| 13.68.109.212:443 |
| 13.90.249.229:443 |
| 13.91.102.27:443 |
| 104.45.230.69:443 |
| 168.62.226.198:12000 |


#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS の依存関係 

| エンドポイント |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80   |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Wildcard HTTP/HTTPS dependencies 

| エンドポイント |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
|grmdsprod\*mini\*.servicebus.windows.net:443 |
|grmdsprod\*lini\*.servicebus.windows.net:443 |
|grsecprod\*mini\*.servicebus.windows.net:443 |
|grsecprod\*lini\*.servicebus.windows.net:443 |
|graudprod\*mini\*.servicebus.windows.net:443 |
|graudprod\*lini\*.servicebus.windows.net:443 |

#### <a name="linux-dependencies"></a>Linux の依存関係 

| エンドポイント |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

