<properties 
	pageTitle="App Service 環境の構成方法" 
	description="App Service 環境の構成、管理、および監視" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# App Service 環境の構成 #

## 概要 ##

App Service 環境は、プレビュー段階で提供されている新しい Premium レベルの機能です。これには、新しいスケーリングおよびネットワーク アクセス機能が用意されています。この新しいスケール機能では、VNET に Azure App Service のインスタンスを配置することができます。App Service 環境 (ASE) の機能に慣れていない場合は、「App Service 環境の概要」(app-service-app-service-environment-intro.md) を参照してください。ASE を作成する方法の詳細については、[App Service 環境の作成方法](app-service-web-how-to-create-an-app-service-environment.md)のドキュメントを参照してください。

大まかに言えば、App Service 環境は次に挙げるいくつかの主要なコンポーネントで構成されます。

- Azure App 環境ホステッド サービスで実行中のコンピューティング リソース
- ストレージ
- データベース
- 1 つ以上のサブネットを持つ Virtual Network
- Azure App 環境のホストされるサービスが実行されるサブネット

App Service 環境の管理と監視を行うには、Azure プレビュー ポータルで [参照]、[App Service 環境] の順にクリックして、管理と監視用の UI にアクセスします。最初のリリースにはシステムの管理に必要な機能が含まれていますが、今後数週間で、機能を追加して改善していく予定です。

![][1]

## Monitoring ##

最初のプレビュー リリースで利用できるメトリック機能は多くありませんが、間もなく公開される予定です。これらのメトリック機能は、システム管理者がシステムのスケーリングや操作についての意思決定を行ううえで役立ちます。

ポータルでは、現在でも App Service 環境のすべての Web アプリのほか、ASE のすべての App Service プランも一覧表示できます。いずれかの一覧を表示するには、[設定] に移動して興味のある項目を選択します。

![][3]

両方の一覧で、使用されるコンピューティング リソースのサイズとインスタンスの数を含む [ワーカー プール] の割り当てを確認できます。個々の App Service プラン内でのパフォーマンスの詳細は、App Service プランの UI を開くことで通常と同じように確認できます。

![][4]

## コンピューティング リソース ##

コンピューティング リソース、ストレージ、データベースは、すべて Azure App Service によって操作されます。ただし、コンピューティング リソースの数量とサイズはユーザーが決めます。

コンピューティング リソースのサイズに関係なく、最小フット プリントには 2 つのフロントエンド サーバーと 2 つのワーカーが含まれます。App Service 環境は合計 55 個までのコンピューティング リソースを使用するように構成できます。この 55 個のコンピューティング リソースのうち、ワークロードのホストに使用できるのは 50 個のみです。その理由は 2 つあります。フロントエンドのコンピューティング リソースは最小で 2 つです。これにより、ワーカー プールの割り当てのサポートには最大で 53 個残ります。ただし、フォールト トレランスを提供するには、次のルールに従い、追加のコンピューティング リソースを割り当てる必要があります。

- 各ワーカー プールには 1 つ以上の追加のコンピューティング リソースが必要で、これをワークロードに割り当てることはできません。
- プール内のコンピューティング リソースの数量が特定の値を超えた場合、別のコンピューティング リソースが必要になります。

1 つのワーカー プール内でのフォールト トレランスの要件は、ワーカー プールに割り当てられたリソースの任意の値 X について、

- X が 2 ～ 20 の場合、ワークロードに使用できるコンピューティング リソースの数は X-1 です。
- X が 21 ～ 40 の場合、ワークロードに使用できるコンピューティング リソースの数は X-2 です。
- X が 41 ～ 53 の場合、ワークロードに使用できるコンピューティング リソースの数は X-3 です。

任意のプールに割り当てることのできるコンピューティング リソースの数量を管理できるだけでなく、サイズを制御することもできます。App Service 環境では、P1 ～ P4 のラベルが付けられた 4 種類のサイズから選択することができます。これらのサイズとその料金設定の詳細については、[App Service の料金](../app-service/app-service-value-prop-what-is.md)に関するページを参照してください。P1 ～ P3 のコンピューティング リソース サイズは、通常利用できるものと同様です。P4 コンピューティング リソースでは、8 つのコアと 14 GB の RAM が提供され、App Service 環境でのみ利用できます。

既に説明したように、App Service 環境の機能は現在プレビュー段階にあるため、まだ拡張の余地があります。App Service 環境が GA に移行するにつれ、追加の監視機能のほか、さらに多くの管理機能が導入される予定です。現時点で、このインターフェイスで管理できるのは、次のわずかな点のみです。

- 各プール内のコンピューティング リソースの数
- 各プール内のコンピューティング リソースのサイズ
- 使用可能な IP アドレスの数

これらを制御するには、上部の [スケール] 構成アイテムを選択します。

![][2]

各プール内のコンピューティング リソースの数量とサイズは、ここで調整できます。ただし、何らかの変更を加える前に、次の点に注意する必要があります。

- 変更が行われるまでには、要求した変更の大きさによって数時間かかる場合があります。
- 既に処理中の App Service 環境の構成の変更が存在する場合、他の変更を開始することはできません。
- ワーカー プールで使用されるコンピューティング リソースのサイズを変更する場合、そのワーカー プールで実行中の Web アプリが停止する可能性があります。

ワーカー プールへのインスタンスの追加は害のない操作であり、システムに影響を与えることはありません。ただし、ワーカー プールで使用されるコンピューティング リソースのサイズを変更する場合は別です。ワーカー プールのサイズ変更中にアプリのダウンタイムが発生するのを避けるためには、次のことをお勧めします。

- 未使用のワーカー プールを使用して、必要なサイズで必要なインスタンスを呼び出す。
- App Service プランを新しいワーカー プールにスケーリングする。  
 
こうすれば、実行中のワークロードでコンピューティング リソースのサイズを変更する場合に比べ、アプリの実行に与える影響は大幅に小さくなります。App Service 環境での Web アプリのスケーリングの詳細については、「[App Service 環境内での Web アプリのスケーリング](app-service-web-scale-a-web-app-in-an-app-service-environment.md)」を参照してください。

## Virtual Network ##

[Virtual Network][virtualnetwork] とサブネットはすべてがユーザーの制御下にあります。App Service 環境には少数のネットワーク要件がありますが、残りの部分はユーザーが制御します。ASE の要件を次に示します。

- 512 個以上のアドレスを持つ VNET
- 256 個以上のアドレスを持つサブネット 
- VNET は地域 VNET であることが必要  
 
VNET の管理は、通常の Virtual Network UI によって行われます。

この機能によって Azure App Service が VNET に配置されるため、ASE でホストされているアプリが ExpressRoute またはサイト間 VPN によってリソースに直接アクセスできるようになっています。App Service 環境をホストしている VNET から利用できるリソースにアクセスするために、App Service 環境内のアプリにネットワーク機能を追加する必要はありません。

必要な場合は、ネットワーク セキュリティ グループを使用してアクセスを制御することもできます。この機能によって、App Service 環境をロック ダウンして必要な IP アドレスのみに制限することができます。この方法の詳細については、「[App Service 環境での受信トラフィックの制御方法](app-service-app-service-environment-control-inbound-traffic.md)」のドキュメントを参照してください。

## App Service 環境の削除 ##

App Service 環境を削除する必要がある場合は、単に [App Service 環境] ブレード上部の [削除] アクションを使用します。ただし、コンテンツを含む ASE を削除することはできません。App Service 環境を削除するためには、すべての Web アプリと App Service プランを削除してください。

## 使用の開始

App Service 環境の使用を開始するには、[App Service 環境の作成方法](app-service-web-how-to-create-an-app-service-environment.md)に関するページを参照してください。

Azure App Service プラットフォームの詳細については、[Azure App Service](../app-service/app-service-value-prop-what-is.md) に関するページを参照してください。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/configureaseblade.png
[2]: ./media/app-service-web-configure-an-app-service-environment/configurescale.png
[3]: ./media/app-service-web-configure-an-app-service-environment/configureasplist.png
[4]: ./media/app-service-web-configure-an-app-service-environment/configurewebapplist.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
 

<!---HONumber=July15_HO3-->