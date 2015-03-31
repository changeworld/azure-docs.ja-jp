<properties 
	pageTitle="Azure Virtual Machines への SharePoint 2010 のデプロイ" 
	description="Azure Virtual Machines で SharePoint 2010 を使用するときにサポートされるシナリオを理解します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/16/2015" 
	ms.author="josephd"/>




<h1>Azure Virtual Machines への SharePoint 2010 のデプロイ</h1>
<h2>概要</h2>

Microsoft SharePoint Server 2010 は高度に柔軟な展開が可能で、ビジネスのニーズと目標に沿った適切な展開シナリオを決定するのに役立ちます。クラウドでホストされ管理される Azure の仮想マシンは、Microsoft SQL Server や SharePoint 展開のように、さまざまなオンデマンド アプリケーションとデータベース ワークロードをサポートする信頼性が高く実際に利用できる総合的なインフラストラクチャを実現します。


Azure の仮想マシンは複数のワークロードをサポートしますが、このホワイトペーパーでは SharePoint の展開を取り上げます。Azure の仮想マシンを使用すると、SharePoint インフラストラクチャをすばやく作成して管理できるだけでなく、ほぼすべてのホストを対象として汎用的にプロビジョニングとアクセスができます。プロセッサ、RAM、CPU 範囲などの SharePoint 仮想マシン (VM) リソースを全面的にコントロールして管理できます。

Azure の仮想マシンを活用すると、ハードウェアの必要性が減少するため、高くつく前払いのコストや複雑さの処理ではなく、インフラストラクチャを規模に応じて構築し管理することに目を向けることができます。つまり、従来の展開のように日単位や週単位ではなく、時間単位で技術革新、実験、反復作業ができるようになります。

<h3>対象読者</h3>

このペーパーの想定読者は IT の専門家です。さらに、アーキテクトやシステム管理者のような技術的意思決定の担当者も、ここに記載した情報とシナリオを利用して Azure 上で仮想化した SharePoint インフラストラクチャを計画し設計できます。

<h3>このペーパーを読む理由</h3>

このホワイトペーパーでは、Azure の仮想マシン内に SharePoint をセットアップして展開する方法について説明します。また、この種の展開がさまざまな規模の組織にとって利点がある理由についても取り上げます。

<h2>クラウド コンピューティングへの転換</h2>

Gartner によれば、クラウド コンピューティングは「インターネット技術を利用して外部の顧客にきわめて拡張性の高い IT 対応機能を  'as a service' 提供するコンピューティングのスタイル」と定義されています。この定義で重要な単語は、拡張性、サービス、そしてインターネットです。簡単に言えば、クラウド コンピューティングは、 <strong>インターネット経由でデプロイされて配信されて提供</strong> される、 <strong>オンデマンドで拡張可能な</strong>IT サービスと定義できます。

クラウド コンピューティングが、現在 IT の世界に起きている大転換の象徴であることは否定できません。昨日は、集約とコストが話題でした。今日は、クラウド コンピューティングによって実現される新しい種類の利点が話題です。問題になっているのは、新しい種類の力を活用して、組織における IT の方法を変革することです。クラウド コンピューティングは、IT の世界を本質的に変えつつあり、サービス プロバイダーやシステム アーキテクトから開発者やエンド ユーザーまで、あらゆる役割に影響を及ぼしています。

調査によれば、俊敏性、集中性、経済性がクラウドを採用する理由のトップ 3 です。

<ul>
<li><p><strong>俊敏性</strong>:クラウド コンピューティングを利用すると、新しいチャンスを迅速に活用し、ビジネスの需要の変化に迅速に対応できるようになります。</p></li>
<li><p><strong>集中性</strong>:クラウド コンピューティングを利用すると、IT 部門はインフラストラクチャ コストを大幅に削減できます。インフラストラクチャが抽象化され、リソースがプールされることで、IT は複雑なサービスとシステムの集合というよりは、電気や水道の公共サービスのように実行されます。さらに、IT にもっと革新的で戦略的な役割を持たせることができるようになります。</p></li>
<li><p><strong>経済性</strong>:クラウド コンピューティングは IT の提供コストを削減し、データ センターの利用率と効率を高めます。提供コストが低下するのは、クラウド コンピューティングを利用すると、アプリケーションとリソースがセルフサービス方式になり、リソースの利用状況を新しい正確な方法で測定できるようになるためです。インフラストラクチャ リソース (ストレージ、コンピューティング、ネットワーク) がプールされて抽象化されるため、ハードウェアの利用率も向上します。</p></li>
</ul>

<h2>クラウド サービスの提供モデル</h2>

単純な言葉で言えば、クラウド コンピューティングとは IT サービスの抽象化です。サービスの範囲は、基本的なインフラストラクチャから完全なアプリケーションまで多岐にわたります。エンド ユーザーは、抽象化されたサービスを要求して利用すればよく、サービスの構成要素を管理する必要はありません (完全に知っている必要さえありません)。現在、業界ではクラウド サービスの提供モデルが 3 種類認知されていて、それぞれ、コントロール (柔軟性) と総費用との間に明白なトレードオフが存在します。

<ul>
<li><p><strong>サービスとしてのインフラストラクチャ</strong> (IaaS):仮想マシンと、ほとんどが既存のアプリケーションをホストする仮想インフラストラクチャ。</p></li>
<li><p><strong>サービスとしてのプラットフォーム</strong> (PaaS):オンデマンドのアプリケーション ホスティング環境を実現するクラウド アプリケーション インフラストラクチャ。</p></li>
<li><p><strong>サービスとしてのソフトウェア</strong> (SaaS):アプリケーションがインターネット経由で配信され、顧客が利用状況に応じて料金を支払うクラウド サービス モデル (たとえば、Microsoft Office 365 や Microsoft CRM Online)。</p></li>
</ul>

図 1 は、クラウド サービスの分類と IT インフラストラクチャの構成要素との対応関係を示しています。内部設置型モデルの場合、ネットワーク接続からアプリケーションまで、システム全体の管理を顧客が担当します。IaaS の場合、システムの下層は業者が管理し、顧客はオペレーティング システムからアプリケーションまでの管理を担当します。PaaS の場合、プラットフォーム業者がネットワーク接続からランタイムまでのすべてを提供し管理します。顧客に必要なのは、アプリケーションとデータを管理することだけです(Azure はこのモデルが一番よく当てはまります)。最後に、SaaS の場合、業者がアプリケーションを提供して、土台となるすべての構成要素によるサービスをすべて抽象化します。

<p class="caption">図 1:クラウド サービスの分類</p>

![azure-sharepoint-wp-1](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-1.png)

<h2>Azure Virtual Machines</h2>

Azure の仮想マシンは、広範な仮想ネットワーキングと並んで VM を全面的にコントロールし管理する機能を備えています。組織にとっては次のような堅固なメリットがあります。

<ul>
<li><p><strong>管理</strong>:クラウドの VM を集中管理して、インフラストラクチャの構成と保守を全面的にコントロールできます。</p></li>
<li><p><strong>アプリケーションの機動性</strong>:内部設置型の環境とクラウド ベースの環境の間で仮想ハード ドライブ (VHD) を移動できます。クラウドで実行するためにアプリケーションをリビルドする必要はありません。</p></li>
<li><p><strong>Microsoft サーバー アプリケーションへのアクセス</strong>:Microsoft SQL Server、SharePoint Server、Windows Server、Active Directory など、内部設置型と同じアプリケーションとインフラストラクチャをクラウドでも実行できます。</p></li>
</ul>

Azure の仮想マシンは、Windows Server と Linux の VM を数分でデプロイして実行できる、簡単で、オープンかつ柔軟で、強力なプラットフォームです。

<ul>
<li><p><strong>簡単</strong>:Azure の仮想マシンを使用すると、クラウドの VM の構築、移行、デプロイ、管理を簡単かつシンプルに実行できます。既存のコードを変更することなくワークロードを Azure に移行することも、わずか数クリックで Azure に新しい VM をセットアップすることもできます。また、Azure の IaaS 機能と PaaS 機能を統合することで、新しいクラウド アプリケーションの開発を支援します。</p></li>

<li><p><strong>オープンで柔軟</strong>:Azure は、柔軟性を備えたオープンなプラットフォームです。イメージ ライブラリにある構築済みのイメージから開始することも、カスタマイズした内部設置型の VHD を作成して使用し、それをイメージ ライブラリにアップロードすることもできます。Linux のコミュニティと商用バージョンも利用できます。</p></li>

<li><p><strong>強力</strong>:Azure は、クラウドで SQL Server や SharePoint Server、Active Directory のようなアプリケーションを実行して、大企業にも対応できるクラウド プラットフォームです。Azure データ センターと自社ネットワークとの間の VPN 接続を使用して、内部設置型ソリューションとクラウド ソリューションを組み合わせたハイブリッド ソリューションを作成できます。</p></li>
</ul>

<h2>Azure の仮想マシンでの SharePoint</h2>

SharePoint 2010 は、Azure の仮想マシンに展開しても、ほとんどのワークロードを柔軟にサポートします。Azure の仮想マシンは、FIS (SharePoint Server for Internet Sites) シナリオと開発シナリオに最も適しています。同様に、中核的な SharePoint ワークロードもサポートされています。クラウドの仮想化オプションを利用しながら独自の SharePoint 2010 実装を管理してコントロールする場合は、Azure の仮想マシンが展開先として最適です。

Azure の仮想マシンはクラウドでホストされ管理されます。柔軟にデプロイでき、ハードウェア調達に伴う設備投資が減るために、コストが削減されます。インフラストラクチャの俊敏性が向上するため、SharePoint Server を数日や数週間ではなく、数時間で展開できます。Azure の仮想マシンでは、"従量料金制" モデルを使ってクラウドに SharePoint ワークロードを展開することもできます。SharePoint ワークロードが増加するにつれて、インフラストラクチャを急速に拡張できます。コンピューティングのニーズが減少したら、使用しなくなったリソースを返却して、使用する分についてだけ料金を支払えばいいのです。

<h3>IT の焦点の推移</h3>

多くの組織は、ハードウェアやオペレーティング システム、セキュリティ、データ ストレージ、バックアップのような IT インフラストラクチャの共通コンポーネントと管理を外注しても、SharePoint Server のような基幹業務系アプリケーションの管理は組織内に残します。IT プラットフォームの基幹業務系でないサービス レイヤーをすべて仮想プロバイダーに委任することで、IT の焦点を中核的な基幹業務系の SharePoint サービスに移し、インフラストラクチャのセットアップに時間をかける代わりに、SharePoint プロジェクトを使ってビジネス価値を実現できます。

<h3>デプロイの高速化</h3>

大規模な SharePoint インフラストラクチャをサポートして展開すると、IT 部門が迅速に行動してビジネス要件をサポートできなくなることがあります。SharePoint サーバーとファームの構築、テスト、準備を実行し、運用環境にデプロイするのに必要な時間は、組織のプロセスと制約に応じて数週間、ときには数か月かかることがあります。Azure の仮想マシンでは、ハードウェアに設備投資をすることなく SharePoint ワークロードを迅速にデプロイできます。こうすることでインフラストラクチャの俊敏性を利用して、数日や数週間ではなく、数時間で展開できます。

<h3>拡張性</h3>

SharePoint の物理サーバーとファームを展開、テスト、準備しなくても、コンピューティング容量を一瞬のうちにオンデマンドで拡張して契約できます。SharePoint のワークロード要件の増大にあわせて、クラウドのインフラストラクチャを急速に拡張できます。同様に、コンピューティングのニーズが減少すれば、リソースを縮小して使用している分だけ料金を支払うことができます。Azure の仮想マシンを使用すると、前払いの経費と長期契約が減り、SharePoint インフラストラクチャを規模に応じて構築して管理できます。この場合も、従来の展開のように日単位や週単位ではなく、時間単位で技術革新、実験、反復作業ができるようになります。

<h3>使用量の測定</h3>

Azure の仮想マシンには、SharePoint シナリオ用のコンピューティング パワー、メモリ、ストレージが用意されていて、その価格は一般にリソースの消費量に基づいて決められます。使用した分だけ料金を支払えばよく、SharePoint インフラストラクチャを実行するのに必要な容量はすべて提供されます。料金と課金の詳細については、<a href="/pricing/details/">料金の概要ページ</a>をご覧ください。Azure クラウドから内部設置型ネットワークにストレージとデータを移動すると、形ばかりの料金がかかることに注意してください。ただし Azure の場合、データのアップロードには料金がかかりません。

<h3>柔軟性</h3>

Azure の仮想マシンでは、開発者が目的の言語またはランタイム環境を柔軟に選択でき、.NET、Node.js、Java、PHP が正式にサポートされています。開発者はツールを選択することもでき、Microsoft Visual Studio、WebMatrix、Eclipse、テキスト エディターがサポートされています。さらに、Microsoft は低コストで低リスクのクラウド移行パスを用意しており、クラウド レポートのニーズを満たす経済的で簡単なプロビジョニングと展開が可能で、各種デバイスと場所にまたがってビジネス インテリジェンス (BI) にアクセスできます。最後に、Azure では、ユーザーは VHD をクラウドに移動できるだけでなく、適切なラインセンスを持っている限り、VHD をコピーして、それをローカルに実行したり、別のクラウド プロバイダー経由で実行したりできます。

<h2>プロビジョニング プロセス</h2>

ここでは Azure の基本について説明します。Azure の <strong>イメージ ライブラリ</strong> には、利用できる構成済みの VM が一覧表示されています。ユーザーは、SharePoint Server、SQL Server、Windows Server などの ISO/VHD をイメージ ライブラリに発行できます。VM の作成を単純にするために、作成された基本イメージはライブラリに発行されます。正規ユーザーはこのイメージを使用して目的の VM を生成できます。詳細については、Azure サイトにある「<a href="/manage/windows/tutorials/virtual-machine-from-gallery/">Windows Server 2008 R2 を実行する仮想マシンの作成</a>」をご覧ください。図 2 は、Azure 管理ポータルを使用して VM を作成する基本的な手順を示しています。

<p class="caption">図 2:VM の作成手順の概要</p>
![azure-sharepoint-wp-2](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png)

ユーザーは Azure 管理ポータルに sysprep 済みイメージをアップロードすることもできます。詳細については、「<a href="/manage/windows/common-tasks/upload-a-vhd/">Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード</a>」をご覧ください。図 3 は、イメージをアップロードして VM を作成する基本的な手順を示しています。

<p class="caption">図 3:イメージのアップロード手順の概要</p>
![azure-sharepoint-wp-3](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png)

<h3>Azure への SharePoint 2010 のデプロイ</h3>

SharePoint 2010 を Azure にデプロイするには、次の手順に従います。

<ol>
<li>Azure サブスクリプション アカウントを使用して<a href="http://manage.windowsazure.com/">Azure 管理ポータル</a>にログオンします。
<ul>

<li>Azure アカウントを持っていない場合は、<a href="http://www.windowsazure.com/pricing/free-trial/">無料の試用アカウントにサインアップしてください</a>。</li>
</ul>
</li>

<li>基本オペレーティング システムの VM を作成します。Azure 管理ポータルで、 <strong>[新規]、[コンピューティング]、[仮想マシン]、[ギャラリーから] の順にクリックします</strong>。</li>

<li>次のコードは、既存のユーザーの電話番号を変更します。 <strong>Choose an image</strong> ダイアログ ボックスが表示されます。[ <strong>Windows Server 2008 R2 SP1]</strong> プラットフォーム イメージ、右矢印の順にクリックします。</li>

<li>[ <strong><em>仮想マシンの構成 </em></strong>] ダイアログ ボックスが表示されます。次の情報を指定します。

<ul>
<li>[仮想マシン名] <strong>を入力します</strong>。
</li>
<li>適切な <strong>サイズを選択します</strong>。
<ul>
<li>運用環境の場合 (SharePoint アプリケーション サーバーとデータベース)、[A3] <em>(4 コア、7 GB メモリ)</em>。</li>
</ul>
</li>
<li>[ <strong>新しいユーザー名]</strong>に、ローカル管理者アカウントのユーザー名を入力します。</li>
<li>[ <strong>新しいパスワード]</strong> ボックスに、強力なパスワードを入力します。</li>
<li>[ <strong>パスワードの確認</strong> ] ボックスに、パスワードを再度入力し、右矢印をクリックします。</li>
</ul>
<li>2 番目の [ <strong>仮想マシンの構成</strong> ] ダイアログ ボックスが表示されます。次の情報を指定します。
<ul>
<li>[ <strong>クラウド サービス</strong> ] ボックスで、次のいずれかを実行します。
<ul>
<li><strong>[新しいクラウド サービスの作成]</strong>。こちらを選択する場合は、クラウド サービスの DNS 名も入力する必要があります。</li>
<li>既存のクラウド サービスを選択する。</li>
</ul>
<li>[ <strong>リージョン/アフィニティ グループ/仮想ネットワーク] </strong>ボックスで、この仮想イメージをホストするリージョンを選択します。</li>
<li>[ <strong>ストレージ アカウント </strong>] ボックスで、次のいずれかを実行します。
<ul>
<li><strong>[自動的に生成されたストレージ アカウントを使用</strong>]</li>
<li>既存のストレージ アカウント名を選択する。</li>
<ul>
<li>自動的に作成されるストレージ アカウントはリージョンあたり 1 つだけです。この設定で作成する他のすべての VM がこのストレージ アカウントに配置されます。</li>
<li>ストレージ アカウントは 20 個に制限されています。</li>
<li>詳細については、「<a href="/manage/windows/common-tasks/upload-a-vhd/#createstorage">Azure にストレージ アカウントを作成する</a>」をご覧ください。</li>
</ul>
</li>
<li>[ <strong>可用性セット]</strong> ボックスの <STRONG>[(なし)]</STRONG> を選択し、右矢印をクリックします。</li>
</ul>
</li>
</ul>
</li>
<li>3 番目の <strong>仮想マシンの構成 </strong>ダイアログ ボックスで、チェック マークをクリックして VM を作成します。</li>


<li>VM に接続するには:
<ul>
<li>リモート デスクトップを使用して VM を開きます。</li>
<li>Azure 管理ポータルで、VM を選択し、 <strong>ダッシュボード</strong> ページをクリックします。</li>
<li>[ <strong>接続</strong>]</li>
</ul>
</li>
<li>次のいずれかのオプションを使用して、SQL Server VM を構築します。
<ul>
<li>前の手順 1. から手順 7. に従って、SQL Server 2012 VM を作成します。ただし、 <strong>手順 3:</strong>では、Windows Server 2008 R2 SP1 イメージの代わりに SQL Server 2012 イメージを使用します。詳細については、「<a href="/manage/windows/common-tasks/install-sql-server/">Azure での SQL Server 仮想マシンのプロビジョニング</a>」をご覧ください。
<ul>
<li>このオプションを選択した場合は、プロビジョニング プロセスで SQL Server 2012 セットアップ ファイルのコピーが <em>C:\SQLServer_11.0_Full</em> ディレクトリに残されるため、インストールをカスタマイズできます。たとえば、ライセンス キーを使用して SQL Server 2012 の評価版をライセンス版に変換できます。</li>
</ul>
</li>
<li>SQL Server システム準備 (SysPrep) ツールを使用して、基本オペレーティング システムの VM に SQL Server をインストールします (上記の手順 1. ～ 7. を参照)。詳細については、「<a href="http://msdn.microsoft.com/library/ee210664.aspx">SysPrep を使用した SQL Server 2012 のインストール</a>」をご覧ください。</li>
<li>コマンド プロンプトを使用して SQL Server をインストールします。詳細については、「<a href="http://msdn.microsoft.com/library/ms144259.aspx#SysPrep">コマンド プロンプトからの SQL Server 2012 のインストール</a>」をご覧ください。</li>
<li>サポートされている SQL Server メディアとライセンス キーを使用して、基本オペレーティング システムの VM に SQL Server をインストールします (上記の手順 1. ～ 7. を参照)。</li>
</ul>
</li>
<li>次のサブ手順に従って、SharePoint ファームを構築します。
<ul>
<li>サブ手順 1.スクリプト ファイルを使用して Azure サブスクリプションを構成します。</li>
<li>サブ手順 2.基本オペレーティング システムの別の VM を作成して (上記の手順 1. ～ 7. を参照)、SharePoint Server をプロビジョニングします。この VM 上に SharePoint Server を構築するには、次のいずれかのオプションを選択します。
<ul>
<li>SharePoint GUI を使用してプロビジョニングします。
<ul>
<li>SharePoint ファームを作成してプロビジョニングするには、<a href="http://technet.microsoft.com/library/ee805948.aspx#CreateConfigure">Microsoft SharePoint Server ファームの作成に関するページ</a>をご覧ください。</li>
<li>ファームに Web またはアプリケーション サーバーを追加するには、「<a href="http://technet.microsoft.com/library/cc261752.aspx">SharePoint 2013 で Web サーバーまたはアプリケーション サーバーをファームに追加する</a>」をご覧ください。</li>
<li>
<p>データベース サーバーを既存のファームに追加するには、「<a href="http://technet.microsoft.com/library/cc262781">Add a Database Server to an Existing Farm (既存のファームへのデータベース サーバーの追加)</a>」をご覧ください。</p>
<ul>
<li>SharePoint ファームで SQL Server 2012 を使用するには、アプリケーションをインストールした後、サーバーの構成はしないで、SharePoint Server 2010 の Service Pack 1 をダウンロードしてインストールする必要があります。詳細については、「<a href="http://www.microsoft.com/download/details.aspx?id=26623">SharePoint Server 2010 Service Pack 1</a>」をご覧ください。</li>
<li>SQL Server の BI 機能を活用するには、SharePoint Server をスタンドアロン サーバーではなくサーバー ファームとしてインストールすることをお勧めします。詳細については、「<a href="http://technet.microsoft.com/library/hh231681(v=sql.110).aspx">SQL Server 2012 のビジネス インテリジェンス機能のインストール</a>」をご覧ください。</li>
</ul>
</li>
</ul>
</li>
<li>Microsoft Windows PowerShell を使用してプロビジョニングします。Psconfig コマンド ライン ツールを代わりに使用して、SharePoint 2010 製品のプロビジョニング方法を制御するいくつかの操作を実行できます。詳細については、「<a href="http://technet.microsoft.com/library/cc263093.aspx">Psconfig コマンドライン リファレンス</a>」をご覧ください。</li>
</ul>
</li>
<li>サブ手順 3.SharePoint を構成します。各 SharePoint VM の準備が完了したら、次のいずれかの方法で各 VM の SharePoint Server を構成します。
<ul>
<li>GUI 画面で SharePoint を構成します。</li>
<li>Windows PowerShell を使用して SharePoint を構成します。詳細については、「<a href="http://technet.microsoft.com/library/cc262839.aspx">Windows PowerShell を使用して SharePoint Server 2010 をインストールする</a>」をご覧ください。
<ul>
<li>CodePlex プロジェクトの AutoSPInstaller を使用することもできます。これは Windows PowerShell スクリプト、XML 入力ファイル、Microsoft Windows 標準バッチ ファイルから構成されています。AutoSPInstaller は、Windows PowerShell に基づいた SharePoint 2010 インストール スクリプトのフレームワークです。詳細については、<a href="http://autospinstaller.codeplex.com/">CodePlex のAutoSPInstaller のページ</a>をご覧ください。

<strong>注</strong>:管理ポータル エンドポイントでセキュリティを構成し、VM の Windows ファイアウォールで着信ポートを設定してください。その後、管理者の資格情報で Windows PowerShell セッションを開いて、SharePoint アプリケーション サーバーの 1 つに対してリモート Windows PowerShell セッションを開始できることを確認します。
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li>スクリプトの処理が完了したら、VM のダッシュボードを使用して VM に接続します。</li>
<li>SharePoint の構成を確認します。SharePoint Server にログオンして、[サーバーの全体管理] を使用して構成を確認します。</li>
</ol>

<h3>仮想ハード ディスクの作成とアップロード</h3>

独自のイメージを作成してそれを Azure に VHD ファイルとしてアップロードすることもできます。VHD ファイルを作成して Azure にアップロードするには、次の手順に従います。

<ol>
<li>Hyper-V 対応のイメージを作成します。Hyper-V マネージャーを使用して Hyper-V 対応の VHD を作成します。詳細については、「<a href="http://technet.microsoft.com/library/cc742509">仮想ハード ディスクを作成する</a>」をご覧ください。</li>
<li>Azure にストレージ アカウントを作成します。VM の作成に使用できる VHD ファイルを Azure にアップロードするには、Azure のストレージ アカウントが必要です。このストレージ アカウントは、Azure 管理ポータルを使って作成できます。詳細については、「<a href="/manage/windows/common-tasks/upload-a-vhd/">Azure にストレージ アカウントを作成する</a>」をご覧ください。</li>
<li>アップロードするイメージを準備します。イメージを Azure にアップロードする前に、SysPrep コマンドを使用してイメージを一般化する必要があります。詳細については、「<a href="http://technet.microsoft.com/library/bb457073.aspx">Sysprep の使用方法 :紹介</a>」をご覧ください。</li>
<li>Azure にイメージをアップロードします。VHD ファイルに格納されたイメージをアップロードするには、管理証明書を作成してインストールする必要があります。証明書のサムプリントとサブスクリプション ID を取得する接続を設定し、CSUpload コマンド ライン ツールを使用して、VHD ファイルをアップロードします。詳細については、「<a href="/manage/windows/common-tasks/upload-a-vhd/">Upload the Image to Azure (Azure にイメージをアップロードする)</a>」をご覧ください。</li>
</ol>

<h2>使用シナリオ</h2>

このセクションでは、Azure の仮想マシンを使用した SharePoint 展開の主な顧客シナリオについて説明します。各シナリオは 2 つに分けられています。シナリオに関する短い説明の後に、作業開始手順が続きます。

<h3>シナリオ 1:単純な SharePoint 開発とテスト環境</h3>

<h4>説明</h4>

オンショアまたはオフショア開発とテストのために、SharePoint アプリケーションを作成して SharePoint 環境をセットアップするもっと俊敏な方法が求められています。基本的に、求められているのは、SharePoint アプリケーション開発プロジェクトをセットアップするのにかかる時間を短縮し、テスト環境の利用を増やしてコストを削減することです。たとえば、異なる地域にいる同時ユーザー数を増やして、SharePoint Server でオンデマンド負荷テストを実行し、ユーザー受け入れテスト (UAT) を実行する場合です。同様に、オンショアまたはオフショアのチームを統合することが、現代の多くの企業にとってますます重要なビジネス ニーズになっています。

このシナリオでは、開発とテスト ワークロードのために構成済み SharePoint ファームをどのように利用できるかを説明します。SharePoint デプロイ トポロジは、内部設置型の仮想化されたデプロイと見かけはまったく同じです。既存の IT スキルは 1 対 1 で Azure の仮想マシンの展開に応用でき、設備投資から運用経費にコストをほぼ完全に移転できるというのが大きな利点です。物理サーバーを前払いで購入する必要はありません。サーバー ハードウェアの設備投資をゼロにして、テストと開発環境向けに SharePoint ファームを作成しセットアップし拡張するのに必要なプロビジョニング時間を大幅に短縮することで柔軟性を実現できます。IT 部門は容量を大幅に増減して、テストと開発の変化するニーズに対応できます。さらに、IT 部門は SharePoint プロジェクトによってビジネス価値を実現することにいっそう力を注ぎ、インフラストラクチャの管理に手をかけないで済ますことができます。

負荷テスト マシンを全面的に活用するために、Azure で Windows Sever 2008 R2 オペレーティング システムをサポートする SharePoint の仮想化された開発とテスト マシンを構成できます。これにより開発チームは、アプリケーションを作成してテストし、コードを変更することなく、内部設置型またはクラウドの運用環境に簡単に移行できるようになります。同じフレームワークとツールセットを内部設置型環境でもクラウド環境でも利用できるため、分散したチームが同じ環境にアクセスできます。また、VPN 接続を直接確立して、内部設置型システムのデータとアプリケーションにアクセスすることもできます。

<h4>作業の開始</h4>

図 4 は、Azure VM の SharePoint 開発環境とテスト環境を示しています。このデプロイを構築するには、まず、アプリケーション開発に使用している内部設置型環境と同じ SharePoint 開発環境とテスト環境を使用します。次に、テストと開発のためにアプリケーションを Azure VM にアップロードしてデプロイします。アプリケーションを内部設置型環境に戻すことを決めた場合は、アプリケーションを修正しなくても戻すことができます。

<p class="caption">図 4:Azure の仮想マシンの SharePoint 開発環境とテスト環境</p>

![azure-sharepoint-wp-11](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png)

<h4>シナリオ環境のセットアップ</h4>

SharePoint 開発環境とテスト環境を Azure に実装するには、次の手順に従います。

<ol>
<li><em>プロビジョニング</em>:まず、Azure の仮想ネットワークを使用して内部設置型システムと Azure との VPN 接続をプロビジョニングします(ここでは Active Directory を使用していないため、VPN トンネルが必要です)。詳細については、「<a href="http://msdn.microsoft.com/library/windowsazure/jj156007.aspx">Azure Virtual Network (Design Considerations and Secure Connection Scenarios) (Azure の仮想ネットワーク (設計上の考慮事項と安全な接続シナリオ))</a>」をご覧ください。次に、管理ポータルで、イメージ ギャラリーにあるストック イメージを使用して、新しい VM をプロビジョニングします。
<ul>
<li>内部設置型 SharePoint 開発とテスト VM を Azure のストレージ アカウントにアップロードして、その VM をイメージ ライブラリから参照して必要な環境を構築できます。</li>
<li>Windows Server 2008 R2 SP1 イメージの代わりに SQL Server 2012 イメージを使用できます。詳細については、「<a href="/manage/windows/common-tasks/install-sql-server/">Azure での SQL Server 仮想マシンのプロビジョニング</a>」をご覧ください。</li>
</ul>
</li>
<li><em>インストール</em>:リモート デスクトップ接続を使用して、SharePoint Server、Visual Studio、と SQL Server を VM にインストールします。
<ul>
<li>SharePoint Server のインストール オプションを選択します。
<ul>
<li>SharePoint 2010 簡易セットアップ スクリプトを使用して、SharePoint 開発マシンを構築します。詳細については、「<a href="http://www.microsoft.com/download/details.aspx?id=23415">SharePoint 2010 Easy Setup Script (SharePoint 2010 簡易セットアップ スクリプト)</a>」をご覧ください。</li>
<li>Windows PowerShell を使用します。詳細については、「<a href="http://technet.microsoft.com/library/cc262839.aspx">Windows PowerShell を使用して SharePoint Server 2010 をインストールする</a>」をご覧ください。</li>
<li>CodePlex プロジェクトの AutoSPInstaller を使用します。詳細については、<a href="http://autospinstaller.codeplex.com/">CodePlex のAutoSPInstaller のページ</a>をご覧ください。</li>
</ul>
</li>
<li>Visual Studio をインストールします。詳細については、「<a href="http://msdn.microsoft.com/library/e2h7fzkw.aspx">Visual Studio のインストール</a>」をご覧ください。</li>
<li>SQL Server をインストールします。詳細については、「<a href="http://msdn.microsoft.com/library/ee210664.aspx">SysPrep を使用した SQL Server 2012 のインストール</a>」をご覧ください。
<ul>
<li>SharePoint ファーム デプロイ用の SQL Server 2012 の作成と構成については、次のハンズオン ラボをご覧ください:<a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint">Configuring SQL Server 2012 for SharePoint in Azure (Azure で SharePoint 用に SQL Server 2012 を構成)</a></li>
<li>Active Directory を構成しシングル SQL Server データベースを使用する SharePoint ファームの作成については、次のハンズオン ラボをご覧ください:<a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs">Deploying a SharePoint Farm with Azure Virtual Machines (Azure の仮想マシンを備えた SharePoint ファームのデプロイ)</a></li>
</ul>
</li>
</ul>
</li>
<li><em>アプリケーションとデータベースのためのデプロイ パッケージとスクリプトの開発</em>:イメージ ライブラリにある VM を使用する場合は、目的の内部設置型アプリケーションとデータベースを Azure の仮想マシンにデプロイできます。
<ul>
<li>SQL Server Data Tools と Visual Studio を使用して、既存の内部設置型アプリケーションとデータベースのためのデプロイ パッケージを作成します。</li>
<li>このパッケージを使用して、アプリケーションとデータベースを Azure の仮想マシンにデプロイします。</li>
</ul>
</li>
<li><em>SharePoint アプリケーションとデータベースのデプロイ</em>:
<ul>
<li>管理ポータル エンドポイントでセキュリティを構成し、VM の Windows ファイアウォールで着信ポートを設定します。</li>
<li>手順 3. で作成したデプロイ パッケージとスクリプトを使用して、SharePoint アプリケーションとデータベースを Azure の仮想マシンにデプロイします。</li>
<li>デプロイしたアプリケーションとデータベースをテストします。</li>
</ul>
</li>
<li><em>VM の管理</em>:
<ul>
<li>管理ポータルを使用して VM を監視します。</li>
<li>Visual Studio と SQL Server Management Studio を使用してアプリケーションを監視します。</li>
<li>Microsoft System Center - Operations Manager のような内部設置型管理ソフトウェアを使用して VM を監視し管理することもできます。</li>
</ul>
</li>
</ol>
<h3>シナリオ 2:カスタマイズして公開された SharePoint ファーム</h3>

<h4>説明</h4>

組織は、クラウドでホストされ、ニーズと需要に応じて簡単に拡張できるインターネット サイトを作成したいと考えています。また、コラボレーションのためにパートナー用のエクストラネット Web サイトを作成して、分散型オーサリングと Web サイト コンテンツ承認のための簡単なプロセスを導入したいとも考えています。最後に、増大する負荷を処理するために、Web サイトの容量をオンデマンドで増やしたいと考えています。

このシナリオの場合、SharePoint Server は公開された Web サイトをホストする土台として使用されます。安全で拡張性の高いクラウド インフラストラクチャ上で、ビジネス Web サイトの迅速なデプロイ、カスタマイズ、とホストができます。Azure で SharePoint の公開 Web サイトを使用すると、トラフィックの増大に応じてサイズを変更し、使用した分だけ料金を支払うことができます。内部設置型と同様の一般的ツールを、Azure 上の SharePoint でもコンテンツのオーサリング、ワークフロー、承認に使用できます。

さらに、Azure の仮想マシンを使用すると、VM 上で動作しているステージング環境と運用環境を簡単に構成できます。Azure で作成した SharePoint の公開 VM は、仮想ストレージにバックアップできます。さらに、災害復旧目的で、連続的なジオ (主要地域) レプリケーション機能を利用して、あるデータ センターで実行されている VM を遠く離れた別のデータ センターに自動的にバックアップできます(Geo (主要地域) レプリケーションの詳細については、「<a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx">Introducing Geo-replication for Azure Storage (Azure のストレージの Geo (主要地域) レプリケーション入門)</a>」をご覧ください)。

Azure インフラストラクチャの VM は、SQL Server や SharePoint Server など他の Microsoft 製品でも動作することが検証されサポートされています。Azure と SharePoint Server は連携して利用すると一段と便利です。どちらも Microsoft ファミリであり、最適な体験を提供できるように両方をあわせて徹底的に統合、サポート、テストがなされています。どちらも SharePoint アプリケーションと Azure インフラストラクチャのサポートが一元化されています。

<h4>作業の開始</h4>

このシナリオでは、余分なトラフィックをサポートするために SharePoint Server にフロントエンドの Web サーバーを追加する必要があります。追加するサーバーは、ユーザー認証とアクセス制御をサポートするために、セキュリティの強化と、Active Directory Domain Services ドメイン コントローラーが必要です。図 5 に、このシナリオのレイアウトを示します。

<p class="caption">図 5:カスタマイズして公開された SharePoint ファーム</p>
![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

<h4>シナリオ環境のセットアップ</h4>

公開された SharePoint ファームを Azure に実装するには、次の手順に従います。

<ol>
<li><em>Active Directory のデプロイ</em>:Active Directory を Azure の仮想マシンに展開するときに必要な基本的要件は、内部設置型の VM (そしてある程度は物理マシン) に展開するときと同様です。ただし同一ではありません。この違いの詳細や指針などの考慮事項については、「<a href="http://msdn.microsoft.com/library/windowsazure/jj156090">Azure の仮想マシンでの Active Directory のデプロイ ガイドライン</a>」をご覧ください。Azure で Active Directory をデプロイするには、以下の手順を実行します。
<ul>
<li>VM を特定のサブネットに割り当てることのできる仮想ネットワークを定義して作成します。詳細については、「<a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Configure Virtual Networking (仮想ネットワークの構成)</a>」をご覧ください。</li>
<li>管理ポータルを使用して、Azure の新しい VM にドメイン コントローラーを作成してデプロイします。詳細については、「<a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Deploying and Creating the Domain Controller (ドメイン コントローラーのデプロイと作成)</a>」をご覧ください。
<ul>
<li>Windows PowerShell スクリプトを参照し、Azure の仮想マシンと仮想ネットワークを使用して、クラウドにスタンドアロン ドメインをデプロイすることもできます。詳細については、「<a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS">Deploying Active Directory in Azure (Windows PowerShell) (Azure での Active Directory のデプロイ (Windows PowerShell))</a>」をご覧ください。</li>
<li>Azure の仮想ネットワーク上の VM に新しい Active Directory フォレストを作成する方法の詳細については、「<a href="/manage/services/networking/active-directory-forest/">Azure での新しい Active Directory フォレストのインストール</a>」をご覧ください。</li>
</ul>
</li>
</ul>
</li>
<li><em>VM のプロビジョニング</em>:管理ポータルを使用して、イメージ ギャラリーにあるストック イメージから新しい VM をプロビジョニングします。</li>
<li><em>SharePoint ファームのデプロイ</em>:
<ul>
<li>新しくプロビジョニングした VM を使用して SharePoint をインストールし、再利用可能なイメージを生成します。SharePoint Server のインストールの詳細については、「<a href="http://technet.microsoft.com/library/cc262839.aspx">Windows PowerShell を使用して SharePoint Server 2010 をインストールする</a>」または <a href="http://autospinstaller.codeplex.com/">CodePlex のAutoSPInstaller のページ</a>をご覧ください。</li>
<li>SharePoint VM を構成して SharePoint ファームを作成し、このファームに接続します。</li>
<li>管理ポータルを使用してロード バランサーを構成します。
<ul>
<li>VM エンドポイントを構成し、既存のエンドポイントでトラフィックを負荷分散するオプションを選択し、負荷分散された VM の名前を指定します。</li>
<li>余分なトラフィック用として既存の SharePoint ファームに別のフロントエンド Web VM を追加します。</li>
</ul>
</li>
</ul>
</li>
<li><em>VM の管理</em>:
<ul>
<li>管理ポータルを使用して VM を監視します。</li>
<li>[サーバーの全体管理] を使用して SharePoint ファームを監視します。</li>
</ul>
</li>
</ol>

<h3>シナリオ 3:BI サービスを追加するためにスケール アウトしたファーム</h3>

<h4>説明</h4>

ビジネス インテリジェンスは、重要な洞察を得て、迅速で健全な意思決定を行うには不可欠です。企業は、内部設置型から移行するにつれて、既存の BI アプリケーションをクラウドにデプロイしながらも、BI 環境自体は変更したくないと考えています。企業が望んでいるのは、SQL Server Analysis Services (SSAS) または SQL Server Reporting Services (SSRS) のレポートを耐久性と可用性が高い環境でホストしながら、保守に大きな時間と予算をかけることなく、BI アプリケーションを全面的にコントロールすることです。

このシナリオでは、Azure の仮想マシンを使用して基幹業務系の BI アプリケーションをホストする方法について説明しています。SharePoint ファームの Azure の仮想マシンに展開して、アプリケーション サーバー VM の BI コンポーネント (SSRS や Excel Services など) をスケール アウトできます。クラウドでリソース集約的なコンポーネントを拡張することで、特殊なワークロードをもっと簡単にうまくサポートできるようになります。小規模から超大規模まで、SQL Server インスタンスのサイズを簡単に変更できるため、Azure の仮想マシンの SQL Server はパフォーマンスが向上することに注意してください。これがシステムの弾力性となり、直近のワークロード要件に基づいて BI インスタンスを動的にプロビジョニング (拡大) したりプロビジョニングを解除 (縮小) したりできるようになります。

既存の BI アプリケーションを Azure に移行すると拡張性が向上します。SSAS、SSRS、SharePoint Server の力を活用すると、サイズを増減できる強力な BI とレポート アプリケーションとダッシュボードを作成できます。このようなアプリケーションとダッシュボードは、内部設置型のデータとアプリケーションとさらに安全に統合することもできます。Azure では、データ センターのコンプライアンスが ISO 27001 対応であることが保証されています。詳細については、「<a href="/support/trust-center/compliance/">Azure トラスト センター</a>」をご覧ください。

<h4>作業の開始</h4>

BI コンポーネントの展開をスケール アウトするには、PowerPivot、Power View、Excel Services、PerformancePoint Services のようなサービスを実行する新しいアプリケーション サーバーをインストールする必要があります。または、SSAS や SSRS のような SQL Server BI インスタンスを既存のファームに追加して、追加のクエリ処理をサポートする必要があります。サーバーは、SharePoint 2010 Server または SQL Server がインストールされた新しい Azure VM として追加できます。その後、そのサーバーに BI コンポーネントをインストールし、展開して構成できます (図 6)。

<p class="caption">図 6:BI サービスを追加するためにスケール アウトした SharePoint ファーム</p>

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

<h4>シナリオ環境のセットアップ</h4>

Azure で BI 環境をスケール アウトするには、次の手順に従います。

<ol>
<li><em>プロビジョニング</em>:
<ul>
<li>Azure の仮想ネットワークを使用して内部設置型システムと Azure との VPN 接続をプロビジョニングします詳細については、「<a href="http://msdn.microsoft.com/library/windowsazure/jj156007.aspx">Azure Virtual Network (Design Considerations and Secure Connection Scenarios) (Azure の仮想ネットワーク (設計上の考慮事項と安全な接続シナリオ))</a>」をご覧ください。</li>
<li>管理ポータルを使用して、イメージ ギャラリーにあるストック イメージから新しい VM をプロビジョニングします。
<ul>
<li>SharePoint Server または SQL Server の BI ワークロード イメージをイメージ ライブラリにアップロードすると、権限のあるユーザーがその BI コンポーネント VM を選択してスケール アウトした環境を構築できます。</li>
</ul>
</li>
</ul>
</li>
<li><em>インストール</em>:SharePoint Server または SQL Server の BI コンポーネントの構築済みイメージがない場合は、リモート デスクトップ接続を使用して、SharePoint Server と SQL Server を VM にインストールします。
<ul>
<li>SharePoint Server のインストールの詳細については、「<a href="http://technet.microsoft.com/library/cc262839.aspx">Windows PowerShell を使用して SharePoint Server 2010 をインストールする</a>」または <a href="http://autospinstaller.codeplex.com/">CodePlex のAutoSPInstaller のページ</a>をご覧ください。</li>
<li>SQL Server のインストールの詳細については、「<a href="http://msdn.microsoft.com/library/ee210664.aspx">SysPrep を使用した SQL Server 2012 のインストール</a>」をご覧ください。</li>
<li>SharePoint ファーム デプロイ用の SQL Server 2012 の作成と構成については、次のハンズオン ラボをご覧ください:<a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint">Configuring SQL Server 2012 for SharePoint in Azure (Azure で SharePoint 用に SQL Server 2012 を構成)</a></li>
<li>Active Directory を構成しシングル SQL Server データベースを使用する SharePoint ファームの作成については、次のハンズオン ラボをご覧ください:<a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs">Deploying a SharePoint Farm with Azure Virtual Machines (Azure の仮想マシンを備えた SharePoint ファームのデプロイ)</a></li>
</ul>
</li>
<li><em>BI VM の追加</em>:
<ul>
<li>管理ポータル エンドポイントでセキュリティを構成し、VM の Windows ファイアウォールで着信ポートを設定します。</li>
<li>既存の SharePoint または SQL Server ファームに新しく作成した BI VM を追加します。</li>
</ul>
</li>
<li><em>VM の管理</em>:
<ul>
<li>管理ポータルを使用して VM を監視します。</li>
<li>[サーバーの全体管理] を使用して SharePoint ファームを監視します。</li>
<li>Microsoft System Center - Operations Manager のような内部設置型管理ソフトウェアを使用して VM を監視し管理します。</li>
</ul>
</li>
</ol>
<h3>シナリオ 4:完全にカスタマイズした SharePoint ベースの Web サイト</h3>

<h4>説明</h4>

全面的にカスタマイズした SharePoint Web サイトをクラウドに作成したいと考えている組織が増えています。必要とされているのは、クラウドで実行されている複雑なアプリケーションを全面的にコントロールして保守できる耐久性と可用性の高い環境です。しかし、大量の時間と予算をかけることは望まれていません。

このシナリオの場合、クラウドに SharePoint ファーム全体を展開して、すべてのコンポーネントを動的にスケール アウトして容量を増やすことも、内部設置型展開をクラウドに拡張して、必要に応じて容量を増やしパフォーマンスを高めることもできます。このシナリオの対象は、アプリケーション開発と企業コンテンツ管理に関して全面的な "SharePoint 体験" を必要とする組織です。もっと複雑なサイトでも、エンド ツー エンドのフル機能のために、強化されたレポート機能、Power View、PerformancePoint、PowerPivot、詳細なグラフ、その他の SharePoint サイト機能を含めることができます。

Azure の仮想マシンを使用すると、経済的で高度なセキュリティで保護されたクラウド インフラストラクチャ上で、カスタマイズしたアプリケーションと関連コンポーネントをホストできます。また、内部設置型の Microsoft System Center を、内部設置型アプリケーションとクラウド アプリケーションに共通の管理ツールとして使用することもできます。

<h4>作業の開始</h4>

完全にカスタマイズした SharePoint Web サイトを Azure で実装するには、クラウドに Active Directory ドメインを展開して、このドメインに新しい VM をプロビジョニングする必要があります。次に、SQL Server 2012 を実行している VM を作成して SharePoint ファームの一部として構成する必要があります。最後に、SharePoint ファームを作成して、負荷分散し、Active Directory と SQL Server に接続する必要があります (図 7)。

<p class="caption">図 7:完全にカスタマイズした SharePoint ベースの Web サイト</p>

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

<h4>シナリオ環境のセットアップ</h4>

次に示しているのは、イメージ ライブラリにある構築済みイメージから、カスタマイズした SharePoint ファーム環境を作成する手順です。ただし、SharePoint ファーム VM をイメージ ライブラリにアップロードして、権限のあるユーザーがその VM を選択して、必要な SharePoint ファームを Azure 上に構築することもできます。

<ol>
<li>Active Directory のデプロイ<em>:Active Directory を Azure の仮想マシンに展開するときに必要な基本的要件は、内部設置型の VM (そしてある程度は物理マシン) に展開するときと同様です。ただし同一ではありません。この違いの詳細や指針などの考慮事項については、「<a href="http://msdn.microsoft.com/library/windowsazure/jj156090">Azure の仮想マシンでの Active Directory のデプロイ ガイドライン</a>」をご覧ください。Azure で Active Directory をデプロイするには、以下の手順を実行します。</em>
<ul>
<li>VM を特定のサブネットに割り当てることのできる仮想ネットワークを定義して作成します。詳細については、「<a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Configure Virtual Networking (仮想ネットワークの構成)</a>」をご覧ください。</li>
<li>管理ポータルを使用して、Azure の新しい VM にドメイン コントローラーを作成してデプロイします。詳細については、「<a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Deploying and Creating the Domain Controller (ドメイン コントローラーのデプロイと作成)</a>」をご覧ください。
<ul>
<li>Windows PowerShell スクリプトを参照し、Azure の仮想マシンと仮想ネットワークを使用して、クラウドにスタンドアロン ドメインをデプロイすることもできます。詳細については、「<a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS">Deploying Active Directory in Azure (Windows PowerShell) (Azure での Active Directory のデプロイ (Windows PowerShell))</a>」をご覧ください。</li>
<li>Azure の仮想ネットワーク上の VM に新しい Active Directory フォレストを作成する方法の詳細については、「<a href="/manage/services/networking/active-directory-forest/">Azure での新しい Active Directory フォレストのインストール</a>」をご覧ください。</li>
</ul>
</li>
</ul>
</li>
<li><em>SQL Server のデプロイ</em>:
<ul>
<li>管理ポータルを使用して、イメージ ギャラリーにあるストック イメージから新しい VM をプロビジョニングします。</li>
<li>VM で SQL Server を構成します。詳細については、「<a href="http://msdn.microsoft.com/library/ee210664.aspx">SysPrep を使用した SQL Server 2012 のインストール</a>」をご覧ください。</li>
<li>新しく作成した Active Directory ドメインに VM を結合します。</li>
</ul>
</li>
<li><em>マルチサーバー SharePoint ファームのデプロイ</em>:
<ul>
<li>仮想ネットワークを作成します。詳細については、「<a href="http://msdn.microsoft.com/library/windowsazure/jj156007.aspx">Azure Virtual Network (Design Considerations and Secure Connection Scenarios) (Azure の仮想ネットワーク (設計上の考慮事項と安全な接続シナリオ))</a>」をご覧ください。
<ul>
<li>SharePoint VM のデプロイ時には、プロビジョニング時にローカル Active Directory ボックスの DNS アドレスが使用できるように、SharePoint Server のサブネットを指定する必要があります。</li>
</ul>
</li>
<li>管理ポータルを使用して VM を作成します。</li>
<li>SharePoint Server をこの VM にインストールして、再利用可能なイメージを生成します。SharePoint Server のインストールの詳細については、「<a href="http://technet.microsoft.com/library/cc262839.aspx">Windows PowerShell を使用して SharePoint Server 2010 をインストールする</a>」または <a href="http://autospinstaller.codeplex.com/">CodePlex のAutoSPInstaller のページ</a>をご覧ください。</li>
<li>SharePoint VM を構成して SharePoint ファームを作成し、<a href="http://technet.microsoft.com/library/ff607979.aspx">Join-SharePointFarm</a> コマンドを使用してこのファームに接続します。</li>
<li>管理ポータルを使用してロード バランサーを構成します。
<ul>
<li>VM エンドポイントを構成し、既存のエンドポイントでトラフィックを負荷分散するオプションを選択し、負荷分散された VM の名前を指定します。
<ul>
<li>SharePoint ファームを Azure の仮想マシンにデプロイする方法の詳細については、この <a href="http://channel9.msdn.com/Events/TechEd/NorthAmerica/2012/AZR327">TechEd North America 2012 のビデオ</a>をご覧ください。</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><em>System Center による SharePoint ファームの管理</em>:
<ul>
<li>Operations Manager エージェントと新しい Azure Integration Pack を使用して、内部設置型の System Center を Azure の仮想マシンに接続します。</li>
<li>管理機能としては、内部設置型の App Controller と Orchestrator を使用します。</li>
</ul>
</li>
</ol>

<h2>まとめ</h2>
クラウド コンピューティングは、組織における IT の方法に変革をもたらしています。これはクラウド コンピューティングが、コストの劇的な減少と並んで、IT の集中性、俊敏性、柔軟性の増加という新しい種類の利点をもたらすことができるためです。Azure は、クラウド コンピューティングの先頭に立って、簡単でオープンで柔軟で強力な仮想インフラストラクチャを実現しています。Azure の仮想マシンを活用すると、ハードウェアの必要性が減少するため、インフラストラクチャを規模に応じて構築することで、全面的なコントロールと合理的な管理を維持したまま、コストと複雑さを減らすことができます。

Azure の仮想マシンはありとあらゆる種類の SharePoint 展開を実現できます。他の Microsoft アプリケーションで最適な体験を提供できるように、全面的にサポートされ、テストがなされています。そのため、新しい SharePoint デプロイ用にインフラストラクチャをプロビジョニングする場合でも、または既存のインフラストラクチャを拡張する場合でも、SharePoint Server を Azure 内に簡単にセットアップしてデプロイできます。SharePoint インフラストラクチャは、ビジネス ワークロードが増大するにつれて迅速に拡張できます。同様に、ワークロードを減らす必要がある場合も、リソースをオンデマンドで契約して、使用する分だけ料金を支払うことができます。このホワイトペーパーで説明した 4 つの SharePoint ベースのシナリオで示したように、Azure の仮想マシンは、広範なビジネス要件を満たす並外れたインフラストラクチャになります。

SharePoint Server を Azure の仮想マシンに正常に展開するには、堅実な計画が必要であり、特に重大なファーム アーキテクチャと展開オプションの範囲を検討する必要があります。このペーパーで概略を示した洞察とベスト プラクティスは、情報を得たうえで SharePoint 展開を実装するにあたって意思決定の指針として役立ちます。

<h2>その他のリソース</h2>

<ul>
<li>
<p>Azure インフラストラクチャ サービスでの SharePoint</p>
<p><a href="http://msdn.microsoft.com/library/dn275955.aspx">http://msdn.microsoft.com/library/dn275955.aspx</a></p>
</li>
<li>
<p>Azure PowerShell の概要</p>
<p><a href="http://msdn.microsoft.com/library/windowsazure/jj156055">http://msdn.microsoft.com/library/windowsazure/jj156055</a></p>
</li>
<li>
<p>Azure の管理コマンドレット</p>
<p><a href="http://msdn.microsoft.com/library/windowsazure/jj152841">http://msdn.microsoft.com/library/windowsazure/jj152841</a></p>
</li>
<li>
<p>さまざまなオペレーティング システム用のコマンド ライン ツールと PowerShell コマンドレット</p>
<p><a href="/manage/downloads/">https://www.windowsazure.com/manage/downloads/</a></p>
</li>
<li>
<p>ハウツー ガイドとベスト プラクティスのドキュメント</p>
<p><a href="/manage/windows/">https://www.windowsazure.com/manage/windows/</a></p>
</li>
</ul>


<!--HONumber=47-->
