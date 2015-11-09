<properties 
	pageTitle="App Service Environment の構成方法" 
	description="App Service Environment の構成、管理、および監視" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/26/2015" 
	ms.author="ccompy"/>


# App Service Environment の構成 #

## 概要 ##

App Service Environment は、Azure App Service の Premium レベルの機能であり、新しいスケールおよびネットワーク アクセス機能を提供します。この新しいスケール機能では、VNET に Azure App Service のインスタンスを配置することができます。この機能で、Web Apps、Mobile Apps、API Apps をホストできます。ただし、Logic Apps は ASE でまだ実行されていません。

App Service Environment (ASE) の機能に慣れていない場合は、[App Service Environment の概要](app-service-app-service-environment-intro.md)に関するページのドキュメントを参照してください。ASE を作成する方法の詳細については、[App Service Environment の作成方法](app-service-web-how-to-create-an-app-service-environment.md)のドキュメントを参照してください。

大まかに言えば、App Service Environment は次に挙げるいくつかの主要なコンポーネントで構成されます。

- Azure App 環境ホステッド サービスで実行中のコンピューティング リソース
- Storage
- データベース
- 1 つ以上のサブネットを持つクラシック "v1" Virtual Network
- Azure App 環境のホストされるサービスが実行されるサブネット

コンピューティング リソースは、4 つのリソース プールに使用されます。各 App Service Environment には、フロント エンドと 3 つのワーカー プールのセットがあります。3 つのワーカー プールすべてを使用する必要はありません。1 つだけ使用することもできます。フロント エンドとは、ASE に保持されているアプリの HTTP エンドポイントです。ワーカーとは、アプリが実際に実行されている場所です。どのような場合にフロント エンドまたはワーカーを追加する必要があるかは、ASE プラットフォームのアプリの状況によって決まります。たとえば、ASE に 1 つのアプリのみがあり、大量の要求を受け取る "hello world" アプリだとします。このような場合、HTTP 負荷を処理できるようにフロント エンドをスケール アップする必要がありますが、逆にワーカーをスケール アップする必要はありません。このような処理に手動で対応することは困難です。各 ASE で多様なパフォーマンス条件がある複数のアプリが実行されている可能性がある場合は、特に困難な状況になります。幸いにも、App Service Environment に自動スケール機能が追加されたので、このような状況にも簡単に対応できます。App Service Environment のスケールと自動スケールの詳細については、「[自動スケールと App Service Environment][ASEAutoscale]」を参照してください。

各 ASE には、500 GB の記憶域が構成されています。この領域は、ASE 内のすべてのアプリケーションに使用されます。この記憶域は ASE の一部であり、顧客の記憶域を使用するように切り替えることはできません。

データベースには、環境を定義する情報だけでなく、環境内で実行されているアプリに関する詳細情報が格納されています。このような情報も、Azure に保持されているサブスクリプションの一部であり、顧客が直接操作できる情報ではありません。

ASE に使用される仮想ネットワークは、ASE の作成時、または事前に作成したネットワークを使用できます。ASE を、ASE に使用されたものとは異なるリソース グループにする場合は、ASE 作成フローから別に VNET を作成する必要があります。ASE の作成時に、サブネットの作成と同時に使用するサブネットを作成して、ASE が VNET と同じリソース グループになるようにすることをお勧めします。現在、V1 の "クラシック" VNET のみがサポートされています。

App Service Environment を管理および監視する UI は、Azure ポータルから使用できます。ASE があれば、ほとんどの場合、サイドバーに App Service 記号が表示されます。この記号は、Azure ポータルに App Service Environment があることを示すために使用されます。

![][1]

アイコンを使用するか、サイドバーの下部にある不等号 (大なり記号) を選択し、App Service Environment を選択します。いずれの操作でも、全 App Service Environment の一覧を表示する UI が開きます。表示されている ASE のいずれかを選択すると、監視および管理に使用される UI が開きます。

![][2]

この最初のブレードには、ASE の一部のプロパティと、リソース プールごとのメトリック グラフが表示されます。Essentials ブロックに表示されるプロパティの一部は、関連付けられているブレードを開くハイパーリンクです。たとえば、VNET 名を選択して、ASE が実行されている VNET と関連付けられた UI を開くことができます。App Service プランとアプリでそれぞれブレードが開き、ASE 内にある項目が一覧表示されます。

## 監視 ##

グラフから、各リソース プールの多様なパフォーマンス メトリックを確認できます。フロント エンド プールの場合、平均的な CPU とメモリの監視には、多くの利点があります。平均を監視して、フロント エンドの負荷を分散し、全般的なパフォーマンスに関する適切なビューを得ることができます。ただし、ワーカー プールは同じではありません。App Service プランが複数ある場合、ワーカー プール内のワーカーを利用することができます。この場合、CPU とメモリの使用状況は、有益な情報の点で多くの情報を提供しません。それまでに使用したワーカー数と、特に、このシステムを管理する場合に、他のシステムが使用できるワーカー数を追跡することは重要です。

グラフで追跡できるすべてりメトリックは、アラートの設定にも使用できます。アラートの設定は、App Service の他の設定と同様に行います。アラートを設定するには、アラートの UI 部分を使用するか、メトリック UI から [アラートの追加] を選択します。
 
![][3]

これまでに説明したメトリックは、App Service Environment メトリックです。App Service プラン レベルで使用できるメトリックもあります。これは、CPU とメモリの監視が重要な場合のメトリックです。ASE のすべての ASP は、専用の ASP です。つまり、その ASP に割り当てられているホストで実行されているアプリのみが、その ASP 内のアプリです。ASP の詳細を確認するには、ASE UI の任意の一覧から ASP を選択するか、すべての ASP が表示される App Service プランを閲覧します。

また、ASE の外部で ASP に使用できる自動スケール機能や、リソースに使用できるメトリックを利用する方法もあります。同じことが、App Service Environment の自動スケールにも当てはまります。ASE のメトリックに基づいて ASP を自動スケールできるだけでなく、ASE 自体の自動スケール ルールを設定することもできます。自動スケールの設定の詳細なガイドについては、「[自動スケールと App Service Environment][ASEAutoscale]」を参照してください。


## プロパティ ##

ASE ブレードを選択すると、設定ブレードが自動的に開きます。その一番上に [プロパティ] が表示されます。プロパティには多数の項目があり、Essentials に表示される項目と重複していますが、VIP アドレスと発信 IP アドレスはとても便利です。現在のところ、これらは同じ 1 つの項目ですが、今後、VIP アドレスとは別の発信 IP アドレスを持つ可能性があるので、注意してください。そのため、ASE に 1 つのアプリしかないので、SSL の設定と IP アドレスの追加を重視していない場合、ASE にあるアプリ用に DNS に設定される IP は、[プロパティ] のこの項目に表示される VIP アドレスになります。

![][4]

## IP アドレス ##

ここで、使用するアプリ用に ASE に IP アドレスを追加できます。IP SSL を使用して設定するアプリを ASE に作成する場合、そのアプリ専用に予約した IP アドレスを用意する必要があります。そのため、ASE は、割り当てることができる独自の IP アドレスをいくつか持っている必要があります。ASE を作成すると、この用途で 1 つのアドレスも作成されます。さらにアドレスが必要な場合は、この項目でアドレスを追加します。追加する場合に、最大の割り当てまでドラッグする前に、IP アドレスの追加には料金がかかる点に気を付けてください。価格の詳細については、「[App Service の価格][AppServicePricing]」ページの「SSL 接続」セクションまでスクロールしてください。追加の価格は、IP SSL の価格です。

**注:** IP アドレスを追加する場合、これはスケール操作である点に注意してください。複数のスケール処理を同時進行させることはできません。スケール処理には、次の 3 つがあります。

- IP SSL に使用できる ASE 内の IP アドレス数を変更する
- リソース プールに使用されるコンピューティング リソースのサイズを変更する
- 手動または自動スケールで、リソース プールに使用されるコンピューティング リソース数を変更する

## リソース プール ##

[設定] から、フロント エンド プールまたはワーカー プール UI にアクセスできます。各リソース プール ブレードでは、そのリソース プールに関する情報のみが表示されます。また、そのリソース プールのスケール全体を制御できます。

各リソース プールの基本ブレードには、そのリソース プールのメトリックを使用したグラフが表示されます。ASE ブレードのグラフと同様に、グラフにアクセスし、必要に応じてアラートを設定することができます。特定のリソース プールに関する ASE ブレードからアラートを設定する操作は、リソース プールからアラートを設定する操作と同じです。ワーカー プールの [設定] ブレードから、そのワーカー プールで実行されているアプリまたは App Service プランすべての一覧にアクセスできます。

![][5]

### コンピューティング リソースの数量スケール ###

ASE 内のアプリのスケールの詳細については、「[App Service Environment 内での Web アプリのスケール](app-service-web-scale-a-web-app-in-an-app-service-environment.md)」を参照してください。ASE リソース プールの自動スケールを構成する方法については、「[自動スケールと App Service Environment][ASEAutoscale]」を参照してください。このドキュメントには、リソース プールの手動スケール操作について詳しく説明されています。

リソース プール、フロント エンド、ワーカーは、テナントに直接アクセスできません。つまり、テナントに対する RDP、プロビジョニングの変更、またはテナントでの管理者権限での操作は実行できません。このような操作は、Azure が処理し、保守しています。ただし、コンピューティング リソースの数量とサイズは、ユーザーの決定次第です。

リソース プール内のサーバー数を制御する方法は、実際のところ 3 つあります。 - 上部にあるメイン ASE ブレードからのスケール操作 - 個々のリソース プールの [設定] にある [スケール] ブレードからの手動スケール操作 - 個々のリソース プールの [スケール] ブレードから設定する自動スケール

ASE ブレードで [スケール] 操作を使用するには、[スケール] をクリックし、スライダーを目的の数量までドラッグして、保存します。この UI は、サイズの変更もサポートしています。

![][6]

特定のリソース プールで手動または自動スケール機能を使用するには、まず ASE ブレードの [設定] を選択します。[設定] から、必要に応じてフロント エンド プールまたはワーカー プールを開き、変更するプールを開きます。[設定] には [スケール] と不等号があります。ここで開いたブレードは、手動スケールまたは自動スケールに使用できます。

![][7]

App Service Environment は合計 55 個までのコンピューティング リソースを使用するように構成できます。この 55 個のコンピューティング リソースのうち、ワークロードのホストに使用できるのは 50 個のみです。その理由は 2 つあります。フロントエンドのコンピューティング リソースは最小で 2 つです。これにより、ワーカー プールの割り当てのサポートには最大で 53 個残ります。フォールト トレランスを提供するには、次のルールに従い、追加のコンピューティング リソースを割り当てる必要があります。

- 各ワーカー プールには、ワークロードに割り当てることができない追加のコンピューティング リソースを 1 つ以上用意する必要があります。
- ワーカー プール内のコンピューティング リソースの数量が特定の値を超えた場合、フォールト トレランスのために別のコンピューティング リソースが必要になります。この対応は、フロント エンド プールの場合には該当しません。

1 つのワーカー プール内でのフォールト トレランスの要件は、ワーカー プールに割り当てられたリソースの任意の値 X について、

- X が 2 ～ 20 の場合、ワークロードに使用できるコンピューティング リソースの数は X-1 です。
- X が 21 ～ 40 の場合、ワークロードに使用できるコンピューティング リソースの数は X-2 です。
- X が 41 ～ 53 の場合、ワークロードに使用できるコンピューティング リソースの数は X-3 です。

前述のように、最低限のフットプリントは 2 つのフロント エンド サーバーと 2 つのワーカーです。上記の文は、わかりやすくするための例です。

- 1 つのプール内に 30 個のワーカーがある場合、そのうち 28 個をワークロードのホストに使用できます。 
- 1 つのプール内に 2 つのワーカーがある場合、1 つをワークロードのホストに使用できます。
- 1 つのプール内に 20 個のワーカーがある場合、19 個をワークロードのホストに使用できます。  
- 1 つのプール内に 21 個のワーカーがある場合でも、ワークロードのホストに使用できるのは 19 個のみです。  

フォールト トレランスの側面は重要ですが、スケールが特定のしきい値を超える場合に注意する必要があります。20 個のインスタンスから容量を追加するには、22 個以上に増やします。これは、21 個では容量が追加されないためです。同様に、40 個を超える数に増やす場合、容量が追加される次の数は 42 です。

### コンピューティング リソースのサイズ スケール ###

任意のプールに割り当てることのできるコンピューティング リソースの数量を管理できるだけでなく、サイズを制御することもできます。App Service Environment では、P1 ～ P4 のラベルが付けられた 4 種類のサイズから選択することができます。これらのサイズとその料金設定の詳細については、[App Service の料金](../app-service/app-service-value-prop-what-is.md)に関するページを参照してください。P1 ～ P3 のコンピューティング リソース サイズは、通常利用できるものと同様です。P4 コンピューティング リソースでは、8 つのコアと 14 GB の RAM が提供され、App Service Environment でのみ利用できます。

プールに使用されるコンピューティング リソースのサイズを変更する場合、2 つの方法があります。ASE ブレードと価格レベル ブレード (個々のリソース プールの [設定] からアクセスできます) から使用できるスケール コマンドがあります。

![][8]

ただし、何らかの変更を加える前に、次の点に注意する必要があります。

- 変更が完了するまでには、変更の規模によっては数時間かかる可能性があります。
- 既に処理中の App Service Environment の構成の変更が存在する場合、他の変更を開始することはできません。
- ワーカー プールで使用されるコンピューティング リソースのサイズを変更する場合、そのワーカー プールで実行中のアプリが停止する可能性があります。

ワーカー プールへのインスタンスの追加は害のない操作であり、そのプール内のリソースのためにアプリが停止することはありません。ただし、ワーカー プールで使用されるコンピューティング リソースのサイズを変更する場合は別です。ワーカー プールのサイズ変更中にアプリのダウンタイムが発生するのを避けるためには、次のことをお勧めします。

- 未使用のワーカー プールを使用して、必要なサイズで必要なインスタンスを呼び出す。
- App Service プランを新しいワーカー プールにスケーリングする。  
 
こうすれば、実行中のワークロードでコンピューティング リソースのサイズを変更する場合に比べ、アプリの実行に与える影響は大幅に小さくなります。App Service Environment でのアプリのスケールの詳細については、「[App Service Environment 内での Web アプリのスケール](app-service-web-scale-a-web-app-in-an-app-service-environment.md)」を参照してください。

## Virtual Network ##

ASE などのホステッド サービスとは異なり、[Virtual Network][virtualnetwork] とサブネットは、すべてがユーザーの制御下にあります。App Service Environment には少数のネットワーク要件がありますが、残りの部分はユーザーが制御します。ASE の要件を次に示します。

- 従来の "v1" VNET 
- 8 個以上のアドレスを持つサブネット 
- VNET は地域 VNET であることが必要  
 
VNET の管理は、Virtual Network UI または PowerShell によって行われます。

この機能によって Azure App Service が VNET に配置されるため、ASE でホストされているアプリが ExpressRoute またはサイト間 VPN によってリソースに直接アクセスできるようになっています。App Service Environment をホストしている VNET から利用できるリソースにアクセスするために、App Service Environment 内のアプリにネットワーク機能を追加する必要はありません。つまり、VNET 統合またはハイブリッド接続を使用して、リソースを取り込んだり、VNET に接続したりする必要はありません。VNET に接続されていないネットワーク内のリソースにアクセスするために、今後もこれらの両方の機能を使用できます。たとえば、VNET 統合を使用して、サブスクリプション内にあり、ASE が属している VNET に接続していない VNET と統合することができます。また、ハイブリッド接続を使用して、通常と同様の方法で他のネットワーク内のリソースにアクセスすることもできます。

ExpressRoute VPN を使用して VNET を構成している場合、ASE に関するいくつかのルーティング ニーズに気を付ける必要があります。ASE とは互換性がないユーザー定義ルート (UDR) 構成がいくつかあります。ExpressRoute を使用する VNET 環境で ASE を実行する場合の詳細については、「[ExpressRoute を使用した App Service Environment のネットワーク構成の詳細][ExpressRoute]」を参照してください。

また、ネットワーク セキュリティ グループを使用してアプリへのアクセスを制御することもできます。この機能によって、App Service Environment をロック ダウンして必要な IP アドレスのみに制限することができます。この方法の詳細については、「[App Service Environment での受信トラフィックの制御方法](app-service-app-service-environment-control-inbound-traffic.md)」のドキュメントを参照してください。

## App Service Environment の削除 ##

App Service Environment を削除する必要がある場合は、単に [App Service Environment] ブレード上部の [削除] アクションを使用します。この操作を実行すると、App Service Environment の名前を入力し、操作の実行を確定するように求められます。注: App Service Environment を削除すると、その内部のすべてのコンテンツも削除されます。

![][9]

## 使用の開始

App Service Environment の使用を開始するには、[App Service Environment の作成方法](app-service-web-how-to-create-an-app-service-environment.md)に関するページを参照してください。

Azure App Service プラットフォームの詳細については、[Azure App Service](../app-service/app-service-value-prop-what-is.md) に関するページを参照してください。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=Nov15_HO1-->