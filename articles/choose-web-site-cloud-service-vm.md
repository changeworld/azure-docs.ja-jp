<properties linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="Azure Websites, Cloud Services and Virtual Machines comparison" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Websites, Cloud Services, and Virtual Machines for hosting web applications. Review a feature comparison." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Azure Web サイト、クラウド サービス、および仮想マシンの比較

Azure には、Web アプリケーションをホストするための方法がいくつかあります ([Azure Web サイト][Azure Web サイト]、[クラウド サービス][クラウド サービス]、[仮想マシン][仮想マシン]など)。これらさまざまなオプションを検討しても、自分のニーズに最適な方法がどれなのかわからない場合や、IaaS と PaaS などの概念がはっきりしない場合があります。この記事では、オプションを理解するのに役立つ情報を提供し、自分の Web シナリオに適した選択を行うことができるようにします。これら 3 つのオプションのどれを使用しても、Azure で非常に拡張性の高い Web アプリケーションを実行することができますが、決定する際に考慮が必要な違いもあります。

多くの場合、Azure Web サイトが最良のオプションです。展開と管理のための簡単で柔軟なオプションが提供され、大量のデータがある Web サイトをホストすることができます。Web アプリケーション ギャラリーにある WordPress などの一般的なソフトウェアを使用して新しい Web サイトをすばやく作成することも、既存の Web サイトを Azure Web サイトに移動することもできます。[Azure の Web ジョブ SDK][Azure の Web ジョブ SDK] (現在はプレビュー) を使用して、バック グラウンド ジョブの処理を追加することもできます。

Azure のクラウド サービスまたは Azure の仮想マシンで、Web アプリケーションをホストするオプションもあります。これらのオプションは、Web 層にこれらが提供する追加のレベルの制御およびカスタマイズが必要な場合には適していますが、制御機能を増強するために、アプリケーションの作成、管理、および展開において複雑さが増します。次の図に、3 つのオプションの間でのトレードオフを示します。

![ChoicesDiagram][ChoicesDiagram]

Web サイトは、簡単に設定、管理、および監視できますが、少ない構成オプションしかありません。重要な点は、クラウド サービスまたは仮想マシンに Web フロント エンドを配置する特別な理由がないときは、Azure Web サイトを使用するということです。このドキュメントの残りの部分では、十分な情報に基づいて決定を行うために必要な情報を提供します。次のトピックがあります。

-   [シナリオ][シナリオ]
-   [サービスの概要][サービスの概要]
-   [機能の比較][機能の比較]

## <a name="scenarios"></a>シナリオ

### スモール ビジネスのオーナーです。サイトをホストするための安価な方法が必要ですが、将来の成長も考慮する必要があります。

このシナリオでは、無料で使用を開始して必要なときにさらに機能を追加できる、Azure Web サイト (WAWS) が最適なソリューションです。たとえば、無料の Web サイトごと、Azure によって提供されるドメインが付属しています (*your\_company*.azurewebsites.net)。会社のドメインの使用を開始する準備ができたら、最低 9.80 ドル/月でそれを追加できます (2014 年 1 月現在)。他にも、ユーザーの要求が増えるにつれて拡大できる多くのサービスや拡張オプションがあります。**Azure Web サイト**を使用すると、次のことができます。

-   無料の層から開始し、必要に応じてスケール アップできます。
-   アプリケーション ギャラリーを使用して、WordPress などの一般的な Web アプリケーションをすばやくセットアップできます。
-   必要に応じて、Azure のその他のサービスや機能をアプリケーションに追加できます。
-   ドメイン名 *your\_company*.azurewebsites.net で提供される証明書を使用して、HTTPS で Web サイトをセキュリティで保護できます。

### Web デザイナーまたはグラフィック デザイナーです。顧客の Web サイトをデザインおよび構築する必要があります。

Azure Web サイトには、Web 開発者が洗練された Web アプリケーションを作成するために必要な機能があります。Web サイトは、Visual Studio や SQL データベースなどのツールとの緊密な統合を提供します。**Azure Web サイト**を使用すると、開発者は次のことができます。

-   [タスクの自動化][タスクの自動化]のためのコマンドライン ツールを使用できます。
-   [.Net][.Net]、[PHP][PHP]、[Node.js][Node.js]、[Python][Python] など、一般的な言語で作業できます。
-   非常に高い容量に拡張するための 3 つの異なる拡張レベルを選択できます。
-   [SQL データベース][SQL データベース]、[サービス バス][サービス バス]、[ストレージ][ストレージ]などの他の Azure サービスや、MySQL、MongoDB など、[Windows Azure ストア][Windows Azure ストア]からのパートナーの製品と統合できます。
-   Visual Studio、Git、WebMatrix、WebDeploy、TFS、FTP などのツールと統合できます。

### Web フロント エンドを含む多層アプリケーションをクラウドに移行しています。

データベース サーバーと対話して Web サイトのデータを格納および取得する Web サーバーなどの多層アプリケーションを実行している場合は、Azure ではいくつかのオプションがあります。これらのアーキテクチャ オプションには、Web サイト、クラウド サービス、仮想マシンがあります。ます、**Web サイト**はソリューションの Web 層に適切なオプションであり、2 層アーキテクチャを作成する Azure SQL データベースで使用できます。また、Web サイトでは、Azure の Web ジョブ SDK のプレビューを使用して、バックグラウンド プロセスや実行時間の長いプロセスを実行することもできます。さらに複雑なアーキテクチャまたはより柔軟性のある拡張オプションが必要な場合は、クラウド サービスまたは仮想マシンを選択する方が適切です。

**クラウド サービス**を使用すると、次のことができます。

-   スケーラブルな Web ロールとワーカー ロールで、Web、中間層、およびバックエンド サービスをホストできます。
-   ワーカー ロールで中間層およびバックエンド サービスのみをホストし、Azure Web サイトでフロント エンドを維持できます。
-   フロント エンド サービスとバックエンド サービスを個別に拡張できます。

**仮想マシン**を使用すると、次のことができます。

-   仮想マシン イメージとして高度にカスタマイズされた環境を、より簡単に移行できます。
-   Web サイトまたはクラウド サービスで構成できないソフトウェアまたはサービスを実行できます。

### 使用しているアプリケーションが、高度にカスタマイズされた Windows または Linux 環境に依存しています。

アプリケーションを使用するために、ソフトウェアおよびオペレーティング システムの複雑なインストールまたは構成が必要な場合、仮想マシンが最良のソリューションであると思われます。**仮想マシン**を使用すると、次のことができます。

-   仮想マシン ギャラリーを使用して、Windows や Linux などのオペレーティング システムから始め、後でアプリケーションの要件に合わせてカスタマイズできます。
-   既存の内部設置型サーバーのカスタム イメージを作成してアップロードすることで、Azure の仮想マシンで実行できます。

### 自分のサイトではオープン ソース ソフトウェアを使用しており、そのソフトウェアを Azure でホストする必要があります。

これら 3 つのオプションのいずれを使用しても、オープン ソースの言語やフレームワークをホストできます。**クラウド サービス**を使用するには、スタートアップ タスクを使用して、必要なオープン ソース ソフトウェア (Windows 上で動作) をインストールおよび構成する必要があります。**仮想マシン**を使用すると、Windows または Linux ベースのマシン イメージにソフトウェアをインストールして構成することができます。オープン ソース フレームワークが Web サイトでサポートされる場合、Web サイトではアプリケーションで必要な言語とフレームワークを自動的に構成できるため、この種のアプリケーションをホストするためのより簡単な方法が提供されます。**Web サイト**を使用すると、次のことができます。

-   [.NET][.Net]、[PHP][PHP]、[Node.js][Node.js]、[Python][Python] など、多くの一般的なオープン ソース言語を使用できます。
-   WordPress、Drupal、Umbraco、DNN、および多くのサード パーティ製 Web アプリケーションをセットアップできます。
-   既存のアプリケーションを移行することも、アプリケーション ギャラリーから新規アプリケーションを作成することもできます。

### 社内ネットワークに接続する必要がある基幹業務アプリケーションがあります。

基幹業務アプリケーションを作成する場合は、Web サイトから社内ネットワーク上のサービスやデータに直接アクセスすることが必要な場合があります。**Web サイト**、**クラウド サービス**、および**仮想マシン**ではこれが可能です。それぞれのアプローチには、次のような違いがあります。

-   Web サイトでは、サービス バス リレーを使用して内部設置型リソースに安全に接続できます。これによって、すべてをクラウドに移行したり、仮想ネットワークを設定しなくても、社内ネットワーク上のサービスは Web アプリケーションの代わりにタスクを実行できます。
-   クラウド サービスと仮想マシンでは、仮想ネットワークを利用できます。実際、仮想ネットワークでは、Azure で実行しているコンピューターを内部設置型ネットワークに接続できます。その後、Azure を社内データセンターの延長として扱うことができます。

### モバイル クライアント向けの REST API や Web サービスをホストします。

HTTP ベースの Web サービスを使用すると、モバイル クライアントを含めて広範囲のクライアントをサポートすることができます。ASP.NET Web API のようなフレームワークは、REST サービスを作成および使用しやすくするために Visual Studio と統合されています。これらのサービスは Web エンドポイントから公開されるため、Azure での Web ホスト手法を使用してこのシナリオをサポートすることができます。ただし、REST API をホストするためには **Web サイト**が適切な選択です。Web サイトを使用すると、次のことができます。

-   Web サイトを迅速に作成して、Azure のグローバルに分散したデータ センターの 1 つで HTTP Web サービスをホストすることができます。
-   既存のサービスを移行するか、新しいサービスを作成して、Visual Studio で ASP.NET Web API を利用できます。
-   1 つのインスタンスで可用性の SLA を実現するか、または複数の専用コンピューターにスケール アウトします。
-   発行済みのサイトを使用して、モバイル クライアントを含む HTTP クライアントに REST API を提供します。

## <a name="services"></a>サービスの概要

[Azure Web サイト][Azure Web サイト]を使用すると、スケーラビリティに優れた Web サイトを Azure に迅速に構築できます。Azure ポータルやコマンドライン ツールを使用して、.NET、PHP、Node.js、Python などの一般的な言語を使用して Web サイトをセットアップすることができます。サポートされているフレームワークは既に展開されていて、インストール作業は必要ありません。Azure Web サイト ギャラリーには、Drupal や WordPress のようなサードパーティ アプリケーションのほか、Django や CakePHP のような開発フレームワークも多数登録されています。サイトの作成後、既存の Web サイトを移行することも、まったく新しい Web サイトを構築することもできます。AWeb サイトを使用すると、物理ハードウェアを管理する必要がなくなり、いくつかの拡張オプションも用意されています。共有マルチテナント モデルから、専用マシンが着信トラフィックを処理する標準モードに移行することができます。Web サイトは、SQL データベース、サービス バス、ストレージのような他の Azure サービスと統合することもできます。[Azure の Web ジョブ SDK][Azure の Web ジョブ SDK] プレビューを使用して、バック グラウンドの処理を追加することができます。まとめると、Azure Web サイトでは、多様な言語、オープン ソース アプリケーション、展開方法 (FTP、Git、Web 展開、TFS) がサポートされているため、アプリケーション開発に重点を置くことができます。クラウド サービスや仮想マシンを必要とする特殊な要件がない場合、通常は、Azure Web サイトが最善の選択肢です。

[クラウド サービス][クラウド サービス]を利用すると、高度な PaaS (Platform as a Service) 環境で、高い可用性と拡張性を備えた Web アプリケーションを作成できます。Web サイトとは違って、クラウド サービスはまず Visual Studio のような開発環境で作成され、その後、Azure に展開されます。PHP などのフレームワークは、ロールのスタートアップ時にフレームワークをインストールするというカスタム展開手順またはタスクが必要となります。クラウド サービスの主な利点は、複雑な多層アーキテクチャをサポートできるという点です。1 つのクラウド サービスは、1 つのフロントエンド Web ロールと 1 つ以上の Worker ロールで構成することができます。それぞれの層は独立して規模を変更することができます。また、Web アプリケーション インフラストラクチャに対するコントロール レベルも向上しています。たとえば、ロール インスタンスを実行しているマシンにリモート デスクトップ接続ができます。また、管理者のコントロールを必要とするタスクも含めて、ロールのスタートアップ時に IIS とマシンの詳細な構成変更を実施するスクリプトを作成することもできます。

[仮想マシン][仮想マシン]を使用すると、Azure の仮想マシンで Web アプリケーションを実行できます。この機能は IaaS (Infrastructure as a Service) としても知られています。ポータル経由で新しい Windows Server または Linux マシンを作成するか、既存の仮想マシン イメージをアップロードします。仮想マシンでは、オペレーティング システム、構成、インストールされたソフトウェアとサービスを最大限にコントロールできます。これは、マシンを全体的に移動できるため、複雑な内部設置型 Web アプリケーションをクラウドにすばやく移行する場合に適した選択肢です。仮想ネットワークを使用すると、内部設置型企業ネットワークにこれらの仮想マシンを接続することもできます。クラウド サービスと同様に、これらのマシンにリモート アクセスして、管理者レベルで構成変更を実施することができます。ただし、Web サイトやクラウド サービスとは異なり、インフラストラクチャ レベルで仮想マシン イメージとアプリケーション アーキテクチャを完全に管理する必要があります。1 つの基本的な例として、オペレーティング システムに独自のパッチを適用する必要があります。

## <a name="features"></a>機能の比較

次の表では、最善の選択ができるように、Web サイト、クラウド サービス、および仮想マシンの機能を比較しています。アスタリスクが付いたボックスについては、表の後のメモでさらに説明しています。

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">機能</th>
<th align="left">Websites</th>
<th align="left">クラウド サービス (Web ロール)</th>
<th align="left">仮想マシン</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>サービス バス、ストレージ、SQL データベースのようなサービスへのアクセス</p></td>
<td align="left">○</td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="even">
<td align="left"><p>多層アーキテクチャの Web 層または Web サービス層のホスト</p></td>
<td align="left">○</td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="odd">
<td align="left"><p>多層アーキテクチャの中間層のホスト</p></td>
<td align="left"></td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="even">
<td align="left"><p>統合されたサービスとしての MySQL のサポート</p></td>
<td align="left">○</td>
<td align="left">X <sup>1</sup></td>
<td align="left">○</td>
</tr>
<tr class="odd">
<td align="left"><p>ASP.NET、クラシック ASP、Node.js、PHP、Python のサポート</p></td>
<td align="left">○</td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="even">
<td align="left"><p>再展開なしでの複数インスタンスへのスケール アウト</p></td>
<td align="left">○</td>
<td align="left">○</td>
<td align="left">X <sup>2</sup></td>
</tr>
<tr class="odd">
<td align="left"><p>SSL のサポート</p></td>
<td align="left">X <sup>3</sup></td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="even">
<td align="left"><p>Visual Studio 統合</p></td>
<td align="left">○</td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="odd">
<td align="left"><p>リモート デバッグ</p></td>
<td align="left">○</td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="even">
<td align="left"><p>TFS によるコードの展開</p></td>
<td align="left">○</td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="odd">
<td align="left"><p>GIT、FTP によるコードの展開</p></td>
<td align="left">○</td>
<td align="left"></td>
<td align="left">○</td>
</tr>
<tr class="even">
<td align="left"><p>Web 展開によるコードの展開</p></td>
<td align="left">○</td>
<td align="left"><sup>4</sup></td>
<td align="left">○</td>
</tr>
<tr class="odd">
<td align="left"><p>WebMatrix サポート</p></td>
<td align="left">○</td>
<td align="left"></td>
<td align="left">○</td>
</tr>
<tr class="even">
<td align="left"><p>ほぼ即時の展開</p></td>
<td align="left">○</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>インスタンスが内容と構成を共有</p></td>
<td align="left">○</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>再展開なしでの大型マシンへのスケール アップ</p></td>
<td align="left">○</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>複数の展開環境 (運用環境とステージング環境)</p></td>
<td align="left">○</td>
<td align="left">○</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Azure の仮想ネットワークによるネットワークの分離</p></td>
<td align="left"></td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="odd">
<td align="left"><p>Azure のトラフィック マネージャーのサポート</p></td>
<td align="left">○</td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="even">
<td align="left"><p>サーバーへのリモート デスクトップ アクセス</p></td>
<td align="left"></td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="odd">
<td align="left"><p>スタートアップ タスクの定義と実行</p></td>
<td align="left"></td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="even">
<td align="left"><p>OS の自動更新の管理</p></td>
<td align="left">○</td>
<td align="left">○</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>統合エンドポイント監視</p></td>
<td align="left">○</td>
<td align="left">○</td>
<td align="left">○</td>
</tr>
<tr class="even">
<td align="left"><p>シームレスなプラットフォームの切り替え (32 ビット/64 ビット)</p></td>
<td align="left">○</td>
<td align="left">○</td>
<td align="left"></td>
</tr>
</tbody>
</table>

<sup>1</sup> Web ロールまたは Worker ロールは、ClearDB のサービスを介してサービスとしての MySQL を統合できますが、管理ポータル ワークフローの一部として統合することはできません。

<sup>2</sup> 仮想マシンは複数のインスタンスにスケール アウトできますが、これらのマシン上で実行されるサービスは、このスケール アウトを処理できるように記述する必要があります。マシンの間で要求をルーティングするように、追加のロード バランサーを構成する必要があります。最後に、メンテナンスやハードウェア障害からの同時の再起動から保護するために、同じロールに参加しているすべてのマシンについてアフィニティ グループを作成してください。

<sup>3</sup> Web サイトの場合、カスタム ドメイン名の SSL は標準モードでのみサポートされます。Web サイトでの SSL の使い方の詳細については、「[Azure の Web サイトの SSL 証明書の構成][Azure の Web サイトの SSL 証明書の構成]」を参照してください。

<sup>4</sup> Web 展開は、単一インスタンス ロールに展開するときにクラウド サービスでサポートされます。ただし、運用ロールでは複数のインスタンスが Azure SLA を満たす必要があります。そのため、Web 展開は、運用環境のクラウド サービスに適した展開のしくみではありません。

  [Azure Web サイト]: http://go.microsoft.com/fwlink/?LinkId=306051
  [クラウド サービス]: http://go.microsoft.com/fwlink/?LinkId=306052
  [仮想マシン]: http://go.microsoft.com/fwlink/?LinkID=306053
  [Azure の Web ジョブ SDK]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png
  [シナリオ]: #scenarios
  [サービスの概要]: #services
  [機能の比較]: #features
  [タスクの自動化]: http://www.windowsazure.com/ja-jp/documentation/scripts/?services=web-sites
  [.Net]: http://www.windowsazure.com/ja-jp/develop/net/
  [PHP]: http://www.windowsazure.com/ja-jp/develop/php/
  [Node.js]: http://www.windowsazure.com/ja-jp/develop/nodejs/
  [Python]: http://www.windowsazure.com/ja-jp/develop/python/
  [SQL データベース]: http://www.windowsazure.com/ja-jp/documentation/services/sql-database/
  [サービス バス]: http://www.windowsazure.com/ja-jp/documentation/services/service-bus/
  [ストレージ]: http://www.windowsazure.com/ja-jp/documentation/services/storage/
  [Windows Azure ストア]: http://www.windowsazure.com/ja-jp/gallery/store/
  [Azure の Web サイトの SSL 証明書の構成]: http://www.windowsazure.com/ja-jp/develop/net/common-tasks/enable-ssl-web-site/
