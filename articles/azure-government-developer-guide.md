---
title: "Azure Government 開発者向けガイド"
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: 
cloud: gov
documentationcenter: 
author: Joharve2
manager: Chrisnie
editor: 
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/29/2015
ms.author: jharve
translationtype: Human Translation
ms.sourcegitcommit: 08de3cb0d62e800db88238764e4f9f221d2add06
ms.openlocfilehash: 42c1b022a22cefae15d32690856bdb80b0eaffa9


---
# <a name="microsoft-azure-government-developer-guide"></a>「Microsoft Azure Government Developer Guide (Microsoft Azure Government 開発者向けガイド)」をご覧ください
<p> Azure Government 環境は、Microsoft の他のネットワークから切り離された物理インスタンスです。  この開発者向けガイドには、アプリケーション開発者と管理者が、Azure のこれらの異なるリージョンとやり取りし、操作する必要がある場合の相違点についての詳しい説明が記載されています。

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>このトピックの内容
* [概要](#Overview)
* [開発者向けのガイダンス](#Guidance)
* [Microsoft Azure Government で現在使用できる機能](#Features)
* [エンドポイント マッピング](#Endpoint)
* [次のステップ](#next)

## <a name="a-nameoverviewaoverview"></a><a name="Overview"></a>概要
Microsoft Azure Government は、米国連邦政府機関、州、地方自治体、そのソリューション プロバイダーのセキュリティとコンプライアンスのニーズを満たす Microsoft Azure サービスの個別のインスタンスです。 Azure Government は、米国政府機関以外のデプロイから物理的、ネットワーク的に独立しており、選別された米国のスタッフによって運営されます。

Microsoft では、クラウド アプリケーションの作成、Microsoft のグローバルな Microsoft Azure サービス ("グローバル サービス") と Microsoft Azure Government サービスにデプロイするための多数のツールを提供します。

アプリケーションを作成し、グローバル サービスではなく Azure Government サービスにデプロイするとき、開発者はその&2; つのサービスの主な違いを把握する必要があります  (特に、プログラム環境の設定と構成、エンドポイントの構成、アプリケーションの記述、それらをサービスとした Azure Government へのデプロイについて)。

このドキュメント内の情報は、これらの相違点をまとめ、[Azure Government](http://www.azure.com/gov "Azure Government") サイトと [MSDN の Microsoft Azure のテクニカル ライブラリ](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN")に記載されている情報を補足するものです。 公式の情報は、[Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/ "Microsoft Azure セキュリティ センター"/)、[Azure ドキュメント センター](https://azure.microsoft.com/documentation/)、[Azure ブログ](https://azure.microsoft.com/blog/ "Azure ブログ"/)など、さまざまな場所にも記載されています。

このコンテンツは、Microsoft Azure Government にデプロイしているパートナーと開発者に適しています。

## <a name="a-nameguidanceaguidance-for-developers"></a><a name="Guidance"></a>開発者向けのガイダンス
現在利用可能な技術的なコンテンツのほとんどは、Microsoft Azure Government ではなく、グローバル サービス向けにアプリケーションを開発中であることを前提としているため、開発者は、Azure Government でホストされるように開発されたアプリケーションの主な相違点を確実に意識することが重要です。

* まず、サービスと機能に相違点があります。これは、グローバル サービスの特定のリージョンにある特定の機能は、Azure Government では使用できないことを意味します。
* 第&2; に、Azure Government で提供されている機能の場合、グローバル サービスとは構成上の相違点があります。  そのため、サンプル コード、構成、手順を確認し、Azure Government クラウド サービス環境内で確実に構築と実行するようにしてください。

## <a name="a-namefeaturesa-features-currently-available-in-microsoft-azure-government"></a><a name="Features"></a> Microsoft Azure Government で現在使用できる機能
現在、Azure Government では、米国政府アイオワ州、米国政府バージニア州の両方のリージョンで、次のサービスを使用できます。

* [Virtual Machines]
* 仮想マシン スケール セット
* コンテナー サービス
* Batch アカウント
* リモート アプリ コレクション
* 可用性セット
* Virtual Networks
* ロード バランサー
* アプリケーション ゲートウェイ
* 仮想ネットワーク ゲートウェイ
* ローカル ネットワーク ゲートウェイ
* ルート テーブル
* Traffic Manager プロファイル
* ExpressRoute 回線
* ネットワーク セキュリティ グループ
* ネットワーク インターフェイス
* パブリック IP アドレス
* 接続
* ストレージ アカウント
* StorSimple Manager
* アプリケーション サービス
* Media Services
* [SQL データベース]
* SQL Data Warehouse
* SQL Server Stretch Database
* Redis Cache
* SQL elastic pools
* SQL Server
* Log Analytics
* Event Hubs
* Service Bus 名前空間
* Azure Active Directory
* 多要素認証
* Rights management
* Automation アカウント
* Marketplace

その他のサービスを利用でき、より多くのサービスが継続的に追加される予定です。  最新のサービスのリストについては、各リージョンで提供されているサービスを紹介している [リージョンに関するページ](https://azure.microsoft.com/regions/#services) を参照してください。

現時点では、Azure Government をサポートするデータ センターは、米国政府アイオワ州と米国政府バージニア州です。  現在のデータ センターと利用可能なサービスについては、上記リージョン ページをご覧ください。

## <a name="a-nameendpointaendpoint-mapping"></a><a name="Endpoint"></a>エンドポイント マッピング
次の表を使用すると、Microsoft Azure と SQL データベースのパブリック エンドポイントを Azure Government 特定のエンドポイントにマッピングするときに役立ちます。

| 名前 | Azure Government エンドポイント |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId  | https://management.core.usgovcloudapi.net/ |
| GalleryUrl | https://gallery.usgovcloudapi.net/ |
| ManagementPortalUrl | https://manage.windowsazure.us |
| ServiceManagementUrl | https://management.core.usgovcloudapi.net/ |
| PublishSettingsFileUrl | https://manage.windowsazure.us/publishsettings/index |
| ResourceManagerUrl | https://management.usgovcloudapi.net/ |
| SqlDatabaseDnsSuffix | .database.usgovcloudapi.net |
| StorageEndpointSuffix | core.usgovcloudapi.net |
| ActiveDirectoryAuthority | https://login-us.microsoftonline.com/ |
| GraphUrl | https://graph.windows.net/ |
| GraphEndpointResourceId | https://graph.windows.net/ |
| TrafficManagerDnsSuffix | usgovtrafficmanager.net |
| AzureKeyVaultDnsSuffix | vault.usgovcloudapi.net |
| AzureKeyVaultServiceEndpointResourceId | https://vault.usgovcloudapi.net |

* Azure AD 経由の Azure Resource Manager 認証については、[Azure Resource Manager 要求の認証](https://msdn.microsoft.com/library/azure/dn790557.aspx)に関する記事を参照してください。

## <a name="a-namenextanext-steps"></a><a name="next"></a>次のステップ
Azure Government のさらに詳細については、次のページをご覧ください。

* **[サインアップして試用](https://azuregov.microsoft.com/trial/azuregovtrial)**
* **[Azure Government の入手とアクセス](http://azure.com/gov)**
* **[Azure Government の概要](/azure-government-overview)**
* **[Azure Government のブログ](http://blogs.msdn.com/b/azuregov/)**
* **[Azure のコンプライアンス](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md



<!--HONumber=Dec16_HO2-->


