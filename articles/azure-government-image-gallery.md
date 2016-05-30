<properties
   pageTitle="Azure Government イメージ ギャラリー"
   description="この記事では、Azure Government イメージ ギャラリーと、含まれるイメージの概要について説明します。"
   services="Azure-Government"
   documentationCenter=""
   authors="joharve2"
   manager="chrisnie"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="azure-government"
   ms.date="05/20/2015"
   ms.author="jharve"/>


#  Microsoft Azure Government イメージ ギャラリー

<p> Microsoft Azure Government イメージ ギャラリーの使い心地は Azure ポータルと似ています。Microsoft や Microsoft のパートナーが事前に構築したイメージのデプロイを選択し、自身の VHD にアップロードできます。これによって、必要に応じて自身のニーズに応じて標準化したイメージを自由にデプロイできるようになります。

下の図は Azure Government イメージ ギャラリーで利用できるイメージのリストを示しています。事前に構成されたイメージの内いくつかでは、特定のソフトウェアのライセンスに対する支払いも生じます。詳細は「<a href="http://azure.microsoft.com/pricing/details/virtual-machines/">Virtual Machines 料金</a>」をご確認いただき、Azure Government 特有の料金について Microsoft アカウント チームや販売店にご確認ください。


## Azure Government イメージ ギャラリー

発行元|イメージ名|説明|OS|ディスク サイズ
---|---|---|---|---
Barracuda Networks, Inc.|Barracuda NextGen Firewall F シリーズ 6.2.1-057|注: このアプライアンスはクライアント アプリケーションで管理され、TCP/807 で転送される必要があります。デプロイ README を参照してください。https://techlib.barracuda.com/NG61/DeployAzure...|Linux|80 GB
Barracuda Networks, Inc.|Barracuda Web Application Firewall 8.0.1.008-20160405|注: このアプライアンスは Web UI で管理され、TCP/8000 で転送される必要があります。詳細については、デプロイ README https://techlib.barracuda.com/WAF/Azure を参照してください。Barracuda Web...|Linux|50 GB
Bitnami|Ruby Stack 2.0|Bitnami RubyStack は、Rails アプリケーションにおける Ruby の開発やデプロイの手間を大幅に削減します。Ruby on Rails は、データベースを備えた Web アプリケーション向けのフルスタックの MVC フレームワークで...|Linux|30 GB
Bitnami|Nginx Stack 1.6|Bitnami Nginx Stack は、包括的で、完全に統合された、すぐに使用できる PHP、MySQL、Nginx の開発環境です。さらに、phpMyAdmin、SQLite、ImageMagick、FastCGI,、Memcache、GD...|Linux|30 GB
Bitnami|LAMP Stack 5.6|Bitnami LAMPStack は、PHP アプリケーションの開発やデプロイの手間を大幅に削減します。すぐに使用できる Apache、MySQL、PHP、phpMyAdmin、他のソフトウェアが含まれ...|Linux|30 GB
Bitnami|LAMP Stack 5.4|Bitnami LAMPStack は、PHP アプリケーションの開発やデプロイの手間を大幅に削減します。すぐに使用できる Apache、MySQL、PHP、phpMyAdmin、他のソフトウェアが含まれ...|Linux|30 GB
Bitnami|Drupal 8|Bitnami が提要する Drupal は、Microsoft Azure で Drupal を実行するための事前構成されたすぐに使用できるイメージです。Drupal は、最も汎用性の高いオープン ソースのコンテンツ管理システムの 1 つです。次の...|Linux|30 GB
Bitnami|Drupal 7|Bitnami が提要する Drupal は、Microsoft Azure で Drupal を実行するための事前構成されたすぐに使用できるイメージです。Drupal は、最も汎用性の高いオープン ソースのコンテンツ管理システムの 1 つです。次の...|Linux|30 GB
Bitnami|Drupal 6|Bitnami が提要する Drupal は、Microsoft Azure で Drupal を実行するための事前構成されたすぐに使用できるイメージです。Drupal は、最も汎用性の高いオープン ソースのコンテンツ管理システムの 1 つです。次の...|Linux|30 GB
Bitnami|Ruby Stack 2.1|Bitnami RubyStack は、Rails アプリケーションにおける Ruby の開発やデプロイの手間を大幅に削減します。Ruby on Rails は、データベースを備えた Web アプリケーション向けのフルスタックの MVC フレームワークで...|Linux|30 GB
Canonical|Ubuntu Server 12.04.5-LTS|Microsoft Azure 用 Ubuntu Server 12.04.5-LTS (amd64 20160315)。Ubuntu Server は、クラウド環境の世界で最も人気のある Linux です。Ubuntu 12.04.5-LTS の更新とパッチを入手できます...|Linux|30 GB
Canonical|Ubuntu Server 16.04 LTS|Microsoft Azure 用 Ubuntu Server 16.04 LTS (amd64 20160420.3)。Ubuntu Server は、クラウド環境の世界で最も人気のある Linux です。Ubuntu 16.04 LTS の更新とパッチを入手できます...|Linux|30 GB
Canonical|Ubuntu Server 14.04.4-LTS|Microsoft Azure 用 Ubuntu Server 14.04.4-LTS (amd64 20160314)。Ubuntu Server は、クラウド環境の世界で最も人気のある Linux です。Ubuntu 14.04.4-LTS の更新とパッチを入手できます...|Linux|30 GB
Check Point|Check Point vSec、R77.30-015052|Azure 用 Check Point vSEC は、次世代のファイアウォールとセキュリティ ゲートウェイを提供します。 |Linux|50 GB
CloudLink|CloudLink SecureVM 5.0 BYOL|簡単かつ確実に VM を制御、監視、暗号化します。EMC の CloudLink SecureVM は、ポリシー ベースのキー管理とオーケストレーションを提供することによってネイティブ Windows BitLocker および Linux OS 暗号化機能をロック解除します。|Linux|30 GB
CloudLink|CloudLink SecureVM|CloudLink® SecureVM は、仮想マシンのボリュームの暗号化と VM イメージの整合性の検証による起動前の承認を提供します。これにより、仮想マシンを起動するタイミングと場所を制御できます。SecureVM は、ネイティブの Windows のロックを解除...|Linux|30 GB
coreos|CoreOS Stable|運用クラスターは Stable チャネルを使用する必要があります。CoreOS のバージョンは、昇格される前に、ベータおよびアルファ チャネル内で実証済みです。 |Linux|31 GB
credativ|Debian 7 Wheezy|Microsoft Azure 用 Debian 7 Wheezy (20160405.0)。Debian GNU/Linux は最も人気のある Linux ディストリビューションの 1 つです。 |Linux|30 GB
credativ|Debian 8 Jessie|Microsoft Azure 用 Debian 8 Jessie (20160405.0)。Debian GNU/Linux は最も人気のある Linux ディストリビューションの 1 つです。 |Linux|30 GB
Esri|ArcGIS 10.3.1 for Server (Windows)|世界で最も汎用的な GIS サーバーである ArcGIS for Server 、組織の重要な geo 資産およびデータを完全に制御します。 |Windows|128 GB
Microsoft Hybrid Cloud Storage Group|StorSimple Virtual Appliance GU2、Feb 2016|Microsoft StorSimple Virtual Appliance GU2 |Windows|32 GB
Microsoft Hybrid Cloud Storage Group|StorSimple Virtual Appliance 1100、October 2015|Microsoft StorSimple Virtual Appliance 1100 |Windows|32 GB
Microsoft Hybrid Cloud Storage Group|StorSimple Virtual Appliance 1100、July 2015|Microsoft StorSimple Virtual Appliance 1100 |Windows|32 GB
Microsoft Hybrid Cloud Storage Group|StorSimple Virtual Appliance 1100、May 2015|Microsoft StorSimple Virtual Appliance 1100 |Windows|32 GB
Microsoft Open Technologies, Inc.|Oracle Database 12c Enterprise Edition on Windows Server 2012|Oracle Database http://www.oracle.com/database 12c Enterprise Edition (12.1.0.1.0) は、新しいマルチテナント アーキテクチャを提供する、クラウド向けの次世代データベースで...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 11g R2 と WebLogic Server 11g Standard Edition on Windows Server 2008 R2|Oracle Database http://www.oracle.com/database 11g R2 Standard Edition (11.2.0.4.0) は、中規模の企業に最適な、機能を取りそろえた手頃な価格のデータ管理ソリューションです。Oracle WebLo...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 11g R2 Standard Edition on Windows Server 2008 R2|Oracle Database http://www.oracle.com/database 11g R2 Standard Edition (11.2.0.4.0) は、中規模の企業に最適な、機能を取りそろえた手頃な価格のデータ管理ソリューションです。最小の...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2|Oracle WebLogic Server http://www.oracle.com/weblogicserver 11g Enterprise Edition (10.3.6) は最新のデータセンター向けの優れた Java アプリケーション サーバーです。最新の機能を...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 11g R2 Enterprise Edition on Windows Server 2008 R2|Oracle Database http://www.oracle.com/database 11g R2 Enterprise Edition (11.2.0.4.0) は、極めて負荷の高いトランザクション処理の管理を容易にする包括的な機能や...|Windows|128 GB
Microsoft Open Technologies, Inc.|Windows Server 2012 R2 の JDK 8|Java プラットフォーム http://www.oracle.com/java の Standard Edition 8 (update 25) は、安全で持ち運べる、パフォーマンスの高いアプリケーションの開発に役立ちます。Java Development Kit (JDK) をはじめ...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 12c Standard Edition on Windows Server 2012|Oracle Database http://www.oracle.com/database 12c Standard Edition (12.1.0.1.0) は、中規模の企業に最適な、機能を取りそろえた手頃な価格のデータ管理ソリューションです。最小の...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 11g R2 と WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2|Oracle Database http://www.oracle.com/database 11g R2 Enterprise Edition (11.2.0.4.0) は、極めて負荷の高いトランザクション処理の管理を容易にする包括的な機能や...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012|Oracle Database http://www.oracle.com/database 12c Enterprise Edition (12.1.0.1.0) は、新しいマルチテナント アーキテクチャを提供する、クラウド向けの次世代データベースで...|Windows|128 GB
Microsoft Open Technologies, Inc.|Windows Server 2012 の JDK 6|Java プラットフォーム http://www.oracle.com/java の Standard Edition 6 (update 85) は、安全で持ち運べる、パフォーマンスの高いアプリケーションの開発に役立ちます。Java Development Kit (JDK) をはじめ...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012|Oracle Database http://www.oracle.com/database 12c Standard Edition (12.1.0.1.0) は、中規模の企業に最適な、機能を取りそろえた手頃な価格のデータ管理ソリューションです。Oracle WebLogic...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle WebLogic Server 12c Enterprise Edition on Windows Server 2012|Oracle WebLogic Server http://www.oracle.com/weblogicserver 12c Enterprise Edition (12.1.2.0) は優れた Java EE アプリケーション サーバーで、次世代アプリケーションの...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle WebLogic Server 12c Standard Edition on Windows Server 2012|Oracle WebLogic Server http://www.oracle.com/weblogicserver 12c Standard Edition (12.1.2.0) は優れた Java EE アプリケーション サーバーで、次世代アプリケーションの...|Windows|128 GB
Microsoft Open Technologies, Inc.|Windows Server 2012 R2 の JDK 7|Java プラットフォーム http://www.oracle.com/java の Standard Edition 7 (update 71) は、安全で持ち運べる、パフォーマンスの高いアプリケーションの開発に役立ちます。Java Development Kit (JDK) をはじめ...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle WebLogic Server 11g Standard Edition on Windows Server 2008 R2|Oracle WebLogic Server http://www.oracle.com/weblogicserver 11g Standard Edition (10.3.6) は、あらゆる規模の企業向けの優れた Java アプリケーション サーバーで、開発者に...|Windows|128 GB
Microsoft SQL Server グループ|SQL Server 2014 RTM Standard on Windows Server 2012 R2|このイメージには、SQL Server の完全バージョンが含まれています。A2 以上のサイズの仮想マシンを使用することをお勧めします。このイメージは、Azure 向けに事前に構成されました。構成には CEIP を有効に...|Windows|127 GB
Microsoft SQL Server グループ|SQL Server 2014 RTM Enterprise on Windows Server 2012 R2|このイメージには、SQL Server の完全バージョンが含まれています。A3 以上のサイズの仮想マシンを使用することをお勧めします。このイメージは、Azure 向けに事前に構成されました。構成には CEIP を有効に...|Windows|127 GB
Microsoft SQL Server グループ|SQL Server 2014 RTM Standard on Windows Server 2012 R2|このイメージには、SQL Server の完全バージョンが含まれています。A2 以上のサイズの仮想マシンを使用することをお勧めします。このイメージは、Azure 向けに事前に構成されました。構成には CEIP を有効に...|Windows|127 GB
Microsoft SQL Server グループ|SQL Server 2012 SP2 Standard on Windows Server 2012|一部の SQL Server コンポーネントでは、使用前ｊに設定や構成を追加する必要があります。A2 以上のサイズの仮想マシンを使用することをお勧めします。このイメージは、Azure 向けに事前に構成され...|Windows|127 GB
Microsoft SQL Server グループ|SQL Server 2014 RTM Enterprise on Windows Server 2012 R2|このイメージには、SQL Server の完全バージョンが含まれています。A3 以上のサイズの仮想マシンを使用することをお勧めします。このイメージは、Azure 向けに事前に構成されました。構成には CEIP を有効に...|Windows|127 GB
Microsoft SQL Server グループ|SQL Server 2012 SP2 Enterprise on Windows Server 2012|このイメージには、SQL Server の完全バージョンが含まれています。一部の SQL Server コンポーネントでは、使用前ｊに設定や構成を追加する必要があります。A3 以上のサイズの仮想マシンを使用することをお勧めします。...|Windows|127 GB
Microsoft Windows Server グループ|Windows Server 2012 Datacenter、December 2015|Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。スケーラブルでダイナミック、マルチ テナント...|Windows|128 GB
Microsoft Windows Server グループ|Windows Server 2012 R2 Datacenter、December 2015|Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。そのパフォーマンスは企業規模の...|Windows|128 GB
Microsoft Windows Server グループ|Windows Server Nano 2016 Tech Preview 4|Nano Server は、フル サイズのオペレーティング システムの一部で実行される Windows Server 用の新しいヘッドレス デプロイ オプションです。このテクニカル プレビューでは、Nano Server を試すことができます。|Windows|8 GB
Microsoft Windows Server グループ|Windows Server 2008 R2 SP1、November 2015|Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。また...|Windows|128 GB
Microsoft Windows Server グループ|Windows Server 2008 R2 SP1、December 2015|Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。また...|Windows|128 GB
Microsoft Windows Server グループ|Windows Server 2008 R2 SP1、January 2016|Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。また...|Windows|128 GB
Microsoft Windows Server グループ|Windows Server 2008 R2 SP1、February 2016|Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。また...|Windows|128 GB
Microsoft Windows Server グループ|Windows Server 2012 Datacenter、November 2015|Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。スケーラブルでダイナミック、マルチ テナント...|Windows|128 GB
Microsoft Windows Server グループ|Windows Server 2012 Datacenter、January 2016|Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。スケーラブルでダイナミック、マルチ テナント...|Windows|128 GB
Microsoft Windows Server グループ|Windows Server 2016 Core with Containers Tech Preview 4|サーバーはクラウド サービスに関する Microsoft の世界規模の経験をユーザーのデータセンターのインフラストラクチャにお届けします。Windows Server 2016 Core with Containers Technical Preview 4 ...|Windows|127 GB
Microsoft Windows Server グループ|Windows Server 2016 Technical Preview 4|Windows Server は Microsoft Cloud OS 構想の中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのデータセンターのインフラストラクチャにお届けします。Windows ...|Windows|128 GB
Microsoft Windows Server グループ|Windows Server 2012 R2 Datacenter、February 2016|Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。そのパフォーマンスは企業規模の...|Windows|128 GB
Microsoft Windows Server グループ|Windows Server 2012 R2 Datacenter、January 2016|Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。そのパフォーマンスは企業規模の...|Windows|128 GB
Microsoft Windows Server グループ|Windows Server 2012 Datacenter、February 2016|Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。スケーラブルでダイナミック、マルチ テナント...|Windows|128 GB
Microsoft Windows Server グループ|Windows Server 2012 R2 Datacenter、November 2015|Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。そのパフォーマンスは企業規模の...|Windows|128 GB
OpenLogic|OpenLogic 7.2|この Linux のディストリビューションは CentOS バージョン 7.2 に基づいており、OpenLogic によって提供されます。Basic Server パッケージのインストールが含まれています。 |Linux|30 GB
OpenLogic|OpenLogic 6.7|この Linux のディストリビューションは CentOS バージョン 6.7 に基づいており、OpenLogic によって提供されます。Basic Server パッケージのインストールが含まれています。 |Linux|30 GB
Oracle|Oracle WebLogic Server 12.1.2 on Oracle Linux 6.4.0.0.0|Oracle WebLogic Server 12c Enterprise Edition は優れた Java EE アプリケーション サーバーで、次世代アプリケーションをミッション クリティカルなクラウド プラットフォームで提供し、ネイティブなクラウド管理、...|Linux|30 GB
Oracle|Oracle Linux 7.0.0.0.0|Oracle Linux 7.0.0.0 は、エンタープライズ アプリケーションおよびシステムに対して、きわめて高いパフォーマンス、スケーラビリティ、信頼性を提供します。エンタープライズのワークロードに最適化されている Oracle Linux は、...|Linux|30 GB
Oracle|Oracle Database 12.1.0.1 Enterprise Edition on Oracle Linux 6.4.0.0.0|Oracle Database 12c Enterprise Edition は、クラウド向けの次世代データベースで、新しいマルチテナント アーキテクチャを、速度、拡張性、信頼性、安全性に優れたデータベース プラットフォームで提供...|Linux|40 GB
Oracle|Oracle Database 12.1.0.1 Standard Edition on Oracle Linux 6.4.0.0.0|Oracle Database 12c Standard Edition は、中規模の企業に最適な、高すぎず、機能を取り揃えたデータ管理ソリューションです。詳細については、http://www.oracle.com/databaseで確認できます。 |Linux|40 GB
SUSE|SUSE Linux Enterprise Server 12|Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます...|Linux|30 GB
SUSE|SUSE Linux Enterprise Server 12 SP1|Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます...|Linux|30 GB
SUSE|SUSE Linux Enterprise Server 12 (Premium Image)|Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます...|Linux|30 GB
SUSE|SUSE Linux Enterprise Server 11 SP4|Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます...|Linux|30 GB



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>次のステップ

イメージ ギャラリーからのデプロイについて、また VHD の作成についての詳細は、次をご覧ください。Azure Government 使用時におけるエンドポイントのプログラム的な相違点の詳細について、<a href="../azure-government-developer-guide">Azure Government 開発者向けガイド</a>を参照してください。

###その他のリソース:

- [Windows を実行する仮想マシンの作成](./virtual-machines/virtual-machines-windows-hero-tutorial.md)

- [Linux を実行する仮想マシンの作成](./virtual-machines/virtual-machines-linux-create-cli-complete.md)

- [仮想マシンに関する FAQ](./virtual-machines/virtual-machines-linux-classic-faq.md)

- [Windows Server VHD の作成と Azure へのアップロード](./virtual-machines/virtual-machines-linux-about-disks-vhds.md/)

- [Linux 仮想マシンをキャプチャする方法](virtual-machines/virtual-machines-linux-classic-capture-image.md)

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ./virtual-machines/virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ./app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ./storage/storage-whatis-account.md

<!---HONumber=AcomDC_0518_2016-->