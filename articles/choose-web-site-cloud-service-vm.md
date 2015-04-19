<properties 
	pageTitle="Azure Websites 、クラウド サービス、仮想マシンの比較" 
	description="Web アプリケーションをホストするにあたり、どのようなときにどのサービス (Azure Websites、Cloud Services、Virtual Machines) を使用するかについて説明します。" 
	services="web-sites, virtual-machines, cloud-services" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="tdykstra"/>

# Azure Websites、Cloud Services、Virtual Machines の比較

## 概要

Azure には、Web サイトをホストするための方法がいくつかあります ([Azure Websites][]、[クラウド サービス][]、[仮想マシン][])。それぞれの選択肢についてこの記事で理解を深め、実際の Web アプリケーションに適した方法をお選びください。

Azure Websites は、ほとんどの Web アプリケーションに適しています。デプロイメントと管理機能がそのプラットフォームに統合され、トラフィックの負荷に応じてサイトのスケールを機敏に調整できるほか、組み込みの負荷分散機能と Traffic Manager によって高い可用性が得られます。既にあるサイトは、[オンライン移行ツール](https://www.migratetoazure.net/)を使用して簡単に Azure Websites へ移行できます。Web アプリケーション ギャラリーからオープン ソース アプリを使用したり、好きなフレームワークとツールを使用して新しいサイトを作成したりできます。[Web ジョブ][]機能を使用すると、バックグラウンド ジョブの処理を簡単に、アプリに追加できます。 

サーバーへのリモート アクセス、サーバーのスタートアップ タスクの構成など、Web サーバー環境をもっと細かく制御する必要がある場合は、Azure Cloud Services が通常、最善の選択肢となります。

既にあるアプリケーションを Azure Websites や Azure Cloud Services で実行するためには、かなりの修正が必要という場合、Azure Virtual Machines を選ぶことで、クラウドへの移行を単純化できる場合があります。ただし、VM の構成、セキュリティ対策、メンテナンスを正しく行うためには、Azure Websites や Cloud Services と比べて、はるかに時間がかかり、IT に関する豊富な知識と経験が要求されます。Azure Virtual Machines を選択する場合は、VM 環境に対する修正プログラムの適用、更新、管理に伴って日々発生するメンテナンスの労力を考慮してください。  

以上に挙げた Azure の Web ホスティング方法ごとに、制御の柔軟性と使いやすさの相対的な度合いを示したのが次の図です。 

![ChoicesDiagram][ChoicesDiagram]

## <a name="scenarios"></a>シナリオと推奨事項

以降、一般的なアプリケーションのシナリオをいくつか取り上げると共に、それぞれのシナリオで最適と思われる Azure Web ホスティングの選択肢を紹介します。

- [Web フロント エンドとバックエンド (バックグラウンド処理とデータベース) を組み合わせて、ビジネス アプリケーションをオンプレミスのアセットと連係させたい。](#onprem)
- [拡張性が高く、世界展開も視野に入れた信頼性の高い方法で会社の Web サイトをホスティングする必要がある。](#corp)
- [Windows Server 2003 上で動作する IIS6 アプリケーションを使用している。](#iis6)
- [スモール ビジネスのオーナーです。将来の成長を考慮しながら安価にサイトをホストできる方法を探しています。](#smallbusiness)
- [Web デザイナーまたはグラフィック デザイナーです。顧客の Web サイトをデザインしたり構築したりする必要があります。](#designer)
- [Web フロント エンドを含む多層アプリケーションをクラウドに移行している。](#multitier)
- [使用しているアプリケーションが、高度にカスタマイズされた Windows や Linux 環境に依存しているが、そのアプリケーションをクラウドに移行したい。](#custom)
- [自分のサイトでオープン ソース ソフトウェアを使用していて、そのソフトウェアを Azure でホストしたい。](#oss)
- [社内ネットワークに接続する必要がある基幹業務アプリケーションがある。](#lob)
- [モバイル クライアント向けの REST API や Web サービスをホストしたい。](#mobile)


### <a id="onprem"></a>Web フロント エンドとバックエンド (バックグラウンド処理とデータベース) を組み合わせて、ビジネス アプリケーションをオンプレミスのアセットと連係させたい。

そのような複合的なビジネス アプリケーションには、Azure Websites が最適です。Azure Websites 向けにアプリケーションを開発することで、負荷分散プラットフォーム上でスケールを自動的に調整し、Active Directory でセキュリティを確保しながら、オンプレミスのリソースに接続することが可能です。それらのアプリケーションは、ワールドクラスの管理ポータルと API によって簡単に管理できます。さらに、そのアプリケーションが顧客によってどのように利用されているかを、アプリケーション洞察ツールで深く見通すことができます。新しい [Web ジョブ][]機能を使用してバックグラウンドの処理とタスクを Web 階層の中で実行すると共に、ハイブリッド接続と [VNET 機能](../fundamentals-introduction-to-azure/#networking/) を使ってオンプレミスのリソースに対して簡単に接続できます。Azure Websites は 99.9% の稼働率が SLA で保証されています。Azure Websites でできることを次に示します。

* 自己復旧機能と自動修正機能を備えたクラウド プラットフォーム上でアプリケーションを実行し、高い信頼性を確保する。 
* グローバルなデータセンターのネットワークを活かしてスケールを自動的に調整する。
* バックアップと復元によって災害復旧に対応する。 
* ISO、SOC2、PCI に準拠する。
* Active Directory と連係する。

### <a id="corp"></a>拡張性が高く、世界展開も視野に入れた信頼性の高い方法で会社の Web サイトをホスティングする必要がある。

Azure Websites は、企業 Web サイトをホスティングするためのソリューションとしてきわめて優れた特長を有しています。グローバルなデータセンターのネットワークを活かしてサイトのスケールを短期間で拡張し、すぐに需要に応えることが可能です。また、ローカル リーチ、フォールト トレランス、インテリジェントなトラフィック管理が実現されています。そのすべてが、ワールドクラスの管理ツールを備えたプラットフォームに集約され、サイトの稼働状態とトラフィックの状況を速やかにかつ簡単に把握できます。Azure Websites は 99.9% の稼働率が SLA で保証されています。Azure Websites でできることを次に示します。

* 自己復旧機能と自動修正機能を備えたクラウド プラットフォーム上で Web サイトを実行し、高い信頼性を確保する。 
* グローバルなデータセンターのネットワークを活かしてスケールを自動的に調整する。
* バックアップと復元によって災害復旧に対応する。 
* 統合されたツールを使用してログとトラフィックを管理する。 
* ISO、SOC2、PCI に準拠する。
* Active Directory と連係する。

### <a id="iis6"></a>Windows Server 2003 上で動作する IIS6 アプリケーションを使用している。

Azure Websites は、過去の IIS6 アプリケーションの移行に伴うインフラストラクチャ コストを効果的に抑制します。互換性をチェックして、変更が必要な項目を洗い出すことのできる「[使いやすい移行ツールと詳細な移行ガイド](https://www.movemetowebsites.net/)」がマイクロソフトから提供されています。Visual Studio や TFS、さらに、広く普及している CMS ツールとの高い親和性により、IIS6 アプリケーションを手間なく直接クラウドにデプロイできます。デプロイ後は、Azure 管理ポータルの堅牢な管理ツールを使ってスケールを調整 (スケール ダウンまたはスケール アップ) することにより、必要に応じてコストを抑制したり需要の増大に対応したりることができます。移行ツールを使用すると、次のことができます。

* 既存の Windows Server 2003 Web アプリケーションをすばやく簡単にクラウドへと移行する。
* 必要であれば、接続先の SQL Database をオンプレミスに維持し、ハイブリッド型のアプリケーションを作成する。 
* SQL Database をレガシ アプリケーションと共に自動的に移動する。 

### <a id="smallbusiness"></a>スモール ビジネスのオーナーです。将来の成長を考慮しながら安価にサイトをホストできる方法を探しています。

このシナリオでは、無料で使用を開始して必要なときにさらに機能を追加できる、Azure Websites が最適なソリューションです。無料の Web サイトにはいずれも、Azure によって提供されるドメインが付属しています (*your_company*.azurewebsites.net)。また、統合デプロイメント/管理ツールや、導入作業を支援するアプリケーション ギャラリーも、このプラットフォームに含まれています。他にも、ユーザーの要求が増えるにつれて拡大できる多くのサービスや拡張オプションがあります。Azure Websites を使用すると、次のことができます。

- 無料の層から開始し、必要に応じてスケール アップできます。
- アプリケーション ギャラリーを使用して、WordPress などの一般的な Web アプリケーションをすばやくセットアップできます。
- 必要に応じて、Azure のその他のサービスや機能をアプリケーションに追加できます。
- Web サイトのセキュリティを HTTPS で保護できます。

### <a id="designer"></a> Web デザイナーまたはグラフィック デザイナーです。顧客の Web サイトをデザインして構築する必要があります。

Web 開発者とデザイナーのために、Azure Websites は、Git や FTP でのデプロイメントに対応するなど、さまざまなフレームワークとツールと容易に連係できるようになっているほか、Visual Studio や SQL Database などのツールやサービスと緊密に統合されています。Websites を使用すると、次のことができます。

- [タスクの自動化][scripting]のためのコマンドライン ツールを使用できます。
- [.Net][dotnet]、[PHP][]、[Node.js][nodejs]、[Python][] といった一般的な言語で作業できます。
- 非常に高い容量に拡張するための 3 つの異なる拡張レベルを選択できます。
- [SQL Database][sqldatabase]、[Service Bus][servicebus]、[Storage][] などの他の Azure サービスや、[Azure ストア][azurestore]からの MySQL や MongoDB などのパートナー製品と統合できます。
- Visual Studio、Git、WebMatrix、WebDeploy、TFS、FTP などのツールと統合できます。

### <a id="multitier"></a>Web フロント エンドを含む多層アプリケーションをクラウドに移行している。

データベースに接続する Web サーバーなど多層アプリケーションを実行する用途において、Azure SQL Database と緊密に連係する Azure Websites は良い選択といえます。Web ジョブ機能を使用してバックエンド プロセスを実行することもできます。

サーバーへのリモート アクセス、サーバーのスタートアップ タスクの構成など、さらに細かくサーバー環境を制御する必要がある場合は、1 つまたは複数の階層に Cloud Services を使用してください。

独自のコンピューター イメージの使用を希望される場合や、Cloud Services 上で構成することのできないサーバー ソフトウェア (またはサーバー サービス) を使用する必要がある場合は、1 つまたは複数の階層に Virtual Machines を使用してください。 

### <a id="custom"></a>使用しているアプリケーションが、高度にカスタマイズされた Windows や Linux 環境に依存しているが、そのアプリケーションをクラウドに移行したい。

アプリケーションを使用するために、ソフトウェアとオペレーティング システムの複雑なインストールまたは構成が必要な場合、仮想マシンが最良のソリューションであると思われます。Virtual Machines を使用すると、次のことができます。

- 仮想マシン ギャラリーを使用して、Windows や Linux などのオペレーティング システムから始め、後でアプリケーションの要件に合わせてカスタマイズできます。 
- 既存の内部設置型サーバーのカスタム イメージを作成してアップロードすることで、Azure の仮想マシンで実行できます。 

### <a id="oss"></a>自分のサイトでオープン ソース ソフトウェアを使用していて、そのソフトウェアを Azure でホストしたい。

そのオープン ソース フレームワークが Websites でサポートされている場合、ご利用のアプリケーションに必要な言語とフレームワークが自動的に構成されます。Websites を使用すると、次のことができます。

- [.NET][dotnet]、 [PHP][]、 [Node.js][nodejs]、[Python][] などの多くの一般的なオープン ソース言語を使用できます。 
- WordPress、Drupal、Umbraco、DNN、多くのサード パーティ製 Web アプリケーションをセットアップできます。 
- 既存のアプリケーションを移行することも、アプリケーション ギャラリーから新規アプリケーションを作成することもできます。 

ご利用のオープン ソース フレームワークが Websites でサポートされていない場合は、他の 2 つの Azure Web ホスティング方法をご利用ください。Cloud Services を使用する場合、スタートアップ タスクを使用して、必要なオープン ソース ソフトウェア (Windows 上で動作) をインストールと構成します。Virtual Machines を使用する場合、Windows または Linux ベースのマシン イメージにソフトウェアをインストールして構成します。 

### <a id="lob"></a>社内ネットワークに接続する必要がある基幹業務アプリケーションがある。

基幹業務アプリケーションを作成する場合は、Web サイトから社内ネットワーク上のサービスやデータに直接アクセスすることが必要な場合があります。これは、[Azure 仮想ネットワーク サービス](/services/virtual-network/)を使用することにより、Websites 、クラウド サービス、仮想マシンで実現できます。Websites 上で、新しい [VNET 統合機能](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)を使用して、見かけ上、社内ネットワークで動作しているかのように Azure アプリケーションを実行できます。

### <a id="mobile"></a>モバイル クライアント向けの REST API や Web サービスをホストしたい。

HTTP ベースの Web サービスを使用すると、モバイル クライアントを含めて広範囲のクライアントをサポートできます。ASP.NET Web API のようなフレームワークは、REST サービスを作成したり使用したりしやすくするために Visual Studio と統合されています。これらのサービスは Web エンドポイントから公開されるため、Azure での Web ホスト手法を使用してこのシナリオをサポートできます。ただし、REST API をホストするためには Websites が適切な選択です。Websites を使用すると、次のことができます。

- Web サイトを迅速に作成して、Azure のグローバルに分散したデータ センターの 1 つで HTTP Web サービスをホストできます。
- 既にあるサービスを移行したり、新しいサービスを作成したりできます。
- 1 つのインスタンスで可用性の SLA を実現するか、または複数の専用コンピューターにスケール アウトします。 
- 発行済みのサイトを使用して、モバイル クライアントを含む HTTP クライアントに REST API を提供します。

## <a name="features"></a>機能の比較

次の表では、最善の選択に役立てられるよう Websites、クラウド サービス、仮想Machinesの機能を比較しています。それぞれのオプションに関する最新の SLA 情報については、「[Azure Service Level Agreements (Azure サービス レベル アグリーメント)](/support/legal/sla/)」をご覧ください。

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">機能</th>
   <th align="left" valign="middle">Websites</th>
   <th align="left" valign="middle">クラウド サービス (Web ロール)</th>
   <th align="left" valign="middle">仮想マシン</th>
   <th align="left" valign="middle">メモ</th>
</tr>
<tr>
   <td valign="middle"><p>ほぼ即時の展開</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle">アプリケーションまたはアプリケーションの更新プログラムを Cloud Services にデプロイしたり、VM を作成したりするには、最低でも数分かかります。一方、アプリケーションを Web サイトにデプロイする場合の所要時間は数秒です。</td>
</tr>
<tr>
   <td valign="middle"><p>再展開なしでの大型マシンへのスケール アップ</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Web サーバーのインスタンスは、コンテンツと構成を共有します。つまり、スケールする際に、デプロイまたは構成をやり直す必要はありません。</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>複数の展開環境 (運用環境とステージング環境)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>OS の自動更新の管理</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>シームレスなプラットフォームの切り替え (32 ビットと 64 ビット間で簡単に移動)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>GIT、FTP によるコードの展開</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Web 展開によるコードの展開</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">Cloud Services では、Web 配置を使用して、個々のロール インスタンスに更新プログラムをデプロイできます。ただし、ロールの初回デプロイメントに Web 配置を使用することはできません。また、更新プログラムに Web 配置を使用する場合は、各ロールのインスタンスに対して個別にデプロイする必要があります。運用環境で Cloud Services の SLA を満たすには、複数のインスタンスが必要です。</td>
</tr>
<tr>
   <td valign="middle"><p>WebMatrix サポート</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>サービス バス、ストレージ、SQL データベースのようなサービスへのアクセス</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>多層アーキテクチャの Web 層または Web サービス層のホスト</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>多層アーキテクチャの中間層のホスト</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">REST API 中間層は、Websites で簡単にホストできます。バックグラウンド処理ジョブは、Websites の <a href="http://go.microsoft.com/fwlink/?linkid=390226">Web ジョブ</a>機能でホストできます。Web ジョブを専用 Web サイトで実行することにより、その階層のスケーラビリティを個別に確保できます。</td>
</tr>
<tr>
   <td valign="middle"><p>統合されたサービスとしての MySQL のサポート</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Cloud Services は、ClearDB のサービスを介してサービスとしての MySQL を統合できますが、管理ポータル ワークフローの一部として統合することはできません。</td>
</tr>
<tr>
   <td valign="middle"><p>ASP.NET、クラシック ASP、Node.js、PHP、Python のサポート</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>再展開なしでの複数インスタンスへのスケール アウト</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Virtual Machines は複数のインスタンスにスケール アウトできますが、そこで実行されるサービスが、このようなスケール アウトに対応できるように記述されていなければなりません。要求を複数のコンピューターにルーティングするためのロード バランサーを構成すると共に、アフィニティ グループを作成して、メンテナンスやハードウェアの障害で全インスタンスが同時に再起動するのを防ぐ必要があります。</td>
</tr>
<tr>
   <td valign="middle"><p>SSL のサポート</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Websites の場合、カスタム ドメイン名の SSL は Basic モードと Standard モードでのみサポートされます。Websites での SSL の使い方については、「<a href="../web-sites-configure-ssl-certificate/">Azure Websites の SSL 証明書の構成</a>」をご覧ください。</td>
</tr>
<tr>
   <td valign="middle"><p>Visual Studio 統合</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>リモート デバッグ</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>TFS によるコードの展開</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p><a href="/services/virtual-network/">Azure の仮想ネットワーク</a>によるネットワークの分離</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">「<a href="/blog/2014/09/15/azure-websites-virtual-network-integration/">Azure Websites Virtual Network Integration (Azure Websites Virtual Network の統合)</a>」もご覧ください。</td>
</tr>
<tr>
   <td valign="middle"><p><a href="/services/traffic-manager/">Azure のトラフィック マネージャー<a href="/services/traffic-manager/">のサポート</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>統合エンドポイント監視</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>サーバーへのリモート デスクトップ アクセス</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>カスタム MSI のインストール</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>スタートアップ タスクの定義と実行</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>ETW イベントのリッスン</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
</table>


> [AZURE.NOTE]
> アカウントにサインアップする前に Azure Websites を実際に使ってみるには、<a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a> にアクセスしてください。Azure Websites で、有効期限付きの ASP.NET スターター サイトを無償ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。


## <a id="nextsteps"></a> 次のステップ

3 つの Web ホスティング方法の詳細については、次のリソースをご覧ください。

* [Microsoft Azure の概要](fundamentals-introduction-to-azure.md)
* [Azure が提供するコンピューティング ホスティング オプション](fundamentals-application-models.md)

アプリケーションに使用するホスティング方法が決まったら、次のリソースで基本事項をご確認ください。

* [Azure Websites](/documentation/services/websites/)
* [Azure クラウド サービス](/documentation/services/cloud-services/)
* [Azure 仮想マシン](/documentation/services/virtual-machines/)

  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Azure Websites]: http://go.microsoft.com/fwlink/?LinkId=306051
  [クラウド サービス]: http://go.microsoft.com/fwlink/?LinkId=306052
  [仮想マシン]: http://go.microsoft.com/fwlink/?LinkID=306053
  [ClearDB]: http://www.cleardb.com/
  [Web ジョブ]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [Azure Websites の SSL 証明書の構成]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
  [azurestore]: http://www.windowsazure.com/gallery/store/
  [scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
  [dotnet]: http://www.windowsazure.com/develop/net/
  [nodejs]: http://www.windowsazure.com/develop/nodejs/
  [PHP]: http://www.windowsazure.com/develop/php/
  [Python]: http://www.windowsazure.com/develop/python/
  [servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
  [sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
  [Storage]: http://www.windowsazure.com/documentation/services/storage/
  [ストレージ]: http://www.windowsazure.com/documentation/services/storage/

<!--HONumber=47-->
