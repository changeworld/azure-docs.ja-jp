#Azure でのトラブルシューティング

*トラブルシューティング*とは、予期しないアプリケーションの
動作を特定して理解し、それを修正する一般的なタスクのことです。
アプリケーションの開発中、開発者はアプリケーションをテスト、実行、
およびデバッグしてから運用環境に展開します。これは、そのアプリ
ケーションがデスクトップ コンピューターで実行されるか、クラウドの
サーバーで実行されるかに関係なく当てはまります。ただし、スケールアウト
用に設計されたマルチインスタンスの広域分散型アプリケーションはデバッグ
が困難な場合があり、標準以上のツールやアプローチが必要です。

このため、クラウド アプリケーションは、トラブルシューティングを考慮
したうえで設計することをお勧めします。トラブルシューティング サポートを
アプリケーションの設計にどのように組み込むかに最も影響するのが
アプリケーションの実行場所です。次に、アプリケーションがベースに
している、または使用している言語またはランタイムに左右されます。

Azure の仮想マシンで実行されているアプリケーションを構築する
場合、そのアプリケーションが独自のサーバーで実行されていれば、ほとんどの
場合、オペレーティング システム上で行う場合と同様にトラブルシューティング
の設計とデバッグにアプローチできます。

Azure で実行されているアプリケーションは、マルチインスタンスの
広域分散型アプリケーションであり、デバッグが困難な場合があります。こうした
種類のアプリケーションでトラブルシューティングを行うには、標準以上のツール
やアプローチが必要です。このトピックでは、実績のあるトラブルシューティング 
プラクティスをいくつか取り上げて説明します。また、こうしたプラクティスの
詳細情報へのリンクも紹介します。

**注**: このトピックは、アプリケーションが設計中または Azure 
アプリケーションが正常に展開されている状態であり、予期しなかった問題が
発生していることを想定しています。アプリケーションを Azure に
展開する方法については説明しません。Azure アプリケーションの開発
と展開の詳細については、<a href="https://www.windowsazure.com/ja-jp/develop/overview/">https://www.windowsazure.com/ja-jp/develop/overview/</a> 
を参照してください。

このトピックでは、問題が発生したときに効果的にトラブルシューティングを行う
ためのアプリケーション設計に役立つベスト プラクティスをいくつか取り上げて
説明します (コード フローを追跡できるようにアプリケーションを設計して
おかないと、発生した問題を特定するのが非常に難しくなる場合があります)。
これらのベスト プラクティスは、使用されているアプリケーション モデルや言語
に関係なく、Azure で実行されているすべての種類のアプリケーション
で有効です。

次のセクションでは、アプリケーションの種類に関係なく、サポートしやすい 
Azure アプリケーションを開発するためのアプローチについて説明
します。たとえば、Web サイト、クラウド サービス、または仮想マシンに
関するアプローチを取り上げます。

各セクションには、ベスト プラクティスの概要と、こうしたベスト 
プラクティスの詳細が記載されているリソースへの参照情報、または
その実装方法が示されています。


##このドキュメントの内容

* [Azure でのトラブルシューティングのベスト プラクティス](#BestPractices)
* [Azure の Web サイト](#Websites)
* [Azure のクラウド サービス](#CloudServices)
* [Azure の仮想マシン](#Vms)
* [Azure サービス](#PlatformServices)
* [SQL データベースのトラブルシューティング](#SQLTroubleshooting)

<h2><a id="BestPractices"></a>Azure でのトラブルシューティングのベスト プラクティス</h2>

このセクションでは、使用しているホスティング モデルまたは言語に
関係なく、Azure アプリケーションに適用されるベスト 
プラクティスについて説明します。これらのプラクティスの詳細情報が
含まれるリソースも紹介します。

Azure で効率的なトラブルシューティングの基礎を築くには、
次の 3 つの領域に重点的に取り組んでください。

-   エラーを正しく処理 - *各コンポーネント サービスが、依存するサービス
    またはインフラストラクチャのエラーに耐えるものである必要があります。*
-   トレース、ログ、および監視 - *各コンポーネント サービスに、適切なデバッグ、
    トレース、イベントおよびエラーのログが必要です。*
-   可能な場合はエラーをデバッグ – *運用環境に移行する前に行います。ただし、
    実行中にコンポーネントおよびネットワーク レベルでも行います。*

これらを考慮してアプリケーションを設計することにより、予期しなかった
動作が発生した場合の追跡に必要な情報をアプリケーションから提供することが
可能になります。

###エラーを正しく処理するための設計

アプリケーションでは、エラー状態を可能な限り正しく処理する必要があります。
これは、分散型の性質を持つ Azure では特に重要です。
効果的なトラブルシューティングは、一時的なエラーの処理を適切に設計する
ことから始まります。一時的なエラーは、インターネット アプリケーション
固有の一時的なエラー状態が原因でクラウド アプリケーションが期待どおり
に動作しない主要領域の 1 つです。

一時的なエラーは、インターネットの共有リソースに固有の待機時間や
断続的なネットワーク接続に関連するエラーです。次に例をいくつか
示します。

-   Azure のクラウド サービス、SQL データベースなど、共有
    コンピューター リソースの応答性は場合によって異なる可能性があります。
-   サービスの持続性を提供するために、応答が遅れます。たとえば、
    SQL Azure では、持続性を提供する目的で複数のデータベース コピー
    の一貫性が保たれており、これが応答性に影響を及ぼします。
-   HTTP またはその他のプロトコル接続が作業完了の前に切断されることで
    遅延が生じます。たとえば、HTTP 要求がエンドポイントに到達できず、
    タイムアウト期間より前に返される可能性があります。


一時的なエラーの影響を軽減するために、Azure アプリケーション
では次のようにする必要があります。

-   内部設置型環境内より頻繁にエラーが発生するサービスにローカルで
    コンポーネントが依存しないように、アプリケーション間の関係を
    疎結合にする。
-   プロセスが迅速な応答に依存しないように、可能な限り非同期呼び出し
    を行う。
-   特定のカテゴリのエラーを検出し、そのエラーの再試行動作を構成可能
    な再試行ポリシーに基づいて実装できる、一時的なエラー処理のアプローチ
    を使用する。

サービスの呼び出しでは、一時的なエラー処理レイヤーを構築または使用して一般的なエラー シナリオを検出し、構成設定に基づいて呼び出しを再試行する必要があります。.NET 開発者向けの推奨ライブラリは [Microsoft Enterprise Library 5.0 Integration Pack for Azure] です。
Microsoft Enterprise Library は、エンタープライズ ソフトウェア開発に
おける分野横断的で一般的な問題に対処する再利用可能なアプリケーション 
ブロックのコレクションです。Microsoft Enterprise Library Integration 
Pack for Azure は Microsoft Enterprise Library 5.0 の拡張機能で
あり、Azure で使用できます。このライブラリには、オート
スケーリング アプリケーション ブロック、一時エラー処理アプリケーション 
ブロック、BLOB 構成ソース、保護された構成プロバイダー、教材などが
含まれます。また、これよりシンプルで機能が少ない .NET ライブラリ 
[Cloud Application Framework & Extensions (CloudFx)] もあります。
CloudFx には、実稼働品質のコンポーネントとビルディング ブロックが多数用意
されており、これらを利用すると、機能が豊富で信頼性と拡張性に優れた Windows 
Azure ベース ソリューションおよびサービスの実装を即座に開始できます。

###適切なトレースとログの実行

分散型スケールアウト アプリケーションは複雑であるため、1 つのプロセス
に対して動作する従来のデバッガーは、アプリケーションの実行中に発生した
問題の調査ではあまり役に立ちません。
したがって、トレースおよびログが非常に重要になってきます。アプリケーション
の実行とデータは、多数の異なるコンピューター上でホストされる多数のサービス
で共有されます。大規模な分散型アプリケーションでは、接続してデバッグする
サービス インスタンスを判断するのは困難です (不可能でない場合)。トレースと
ログを使用すると、アプリケーションの実行とデータ フローを追跡し、
アプリケーションの状態を明確に把握できます。

優れた Azure アプリケーションでは、ログとトレース戦略が
アプリケーション設計に最初から組み込まれています。これにより問題を
特定し修正するために必要な時間と労力を削減でき、Microsoft のサポート
への連絡も不要になります。

**注**: トレースとログの書き込みを大量に行うとパフォーマンスに影響
します。集中的に行うと、その影響はさらに大きくなります。このため、
アプリケーションの設計には、必要に応じて調整できる構成可能なトレース
とログ ポリシーを組み込むことをお勧めします。可能であれば、ログの
レベルは、再展開しなくても変更できるように、**ServiceConfiguration.cscfg** 
ファイルから調整できるようにしておきます。

大量のログがあっても、検出と修復のスピードが速くなるわけではありません。
データの量が多いと読み取りに時間がかかり、そのデータを収集する処理は
アプリケーションのパフォーマンスに影響を及ぼします。調整可能なログでは、
ログ データのサイズとストレージ コストの両方を制御できます。

MSDN マガジンの記事「[Azure でログ記録とトレースを制御する]」では、検討対象となる 4 つの種類の診断出力について、Mike Kelly 氏
が説明しています。

-   デバッグ出力 - デバッグ ビルドでのみ出力 (アサートなどが含まれる)
-   トレース - 実行時の制御フローを追跡
-   イベント ログ - プログラムの実行中の主なイベント
-   エラー ログ - 例外的な状況や危険を伴う可能性のある状況

他の言語、アプリケーション プラットフォーム、およびオペレーティング 
システムでは、トレースおよびログの用語が異なる場合があります。こうした
開発プラットフォームのいずれかを Azure で使用する場合は、
該当する言語またはプラットフォームに応じた戦略およびツールを使用
してください。

混合モードのアプリケーションとは、Azure の仮想マシン、
Web サイト、およびクラウド サービスを組み合わせた環境で実行されて
いるアプリケーションです。この種類のアプリケーションを構築する場合
は、トレースとログがますます重要になってきます。こうしたアプリケーション
はより広い範囲にわたって分散されているためです。これらの混合モード 
アプリケーションについてトラブルシューティングを行うには、データ全体と
実行フローを順に追跡して、問題を特定する必要があります。混合モード 
アプリケーションのトレースとログのメカニズムは、コンポーネントの
ホスティング モデルに依存します。

###アプリケーションの監視

トレースとログが含まれる、もう少し大きな領域が監視です。監視では、
次の操作を行うことができます。

-   Azure アプリケーションを探索する
-   各ロール インスタンスの状態を判断する
-   待機時間、スループットなどのパフォーマンス情報を収集して監視する
-   イベントを収集して監視する
-   トレース メッセージを収集して監視する
-   リソース使用量を監視する
-   サービス メトリックの質を監視する
-   容量計画を実施する
-   トラフィック解析を実行する (ユーザー、ビュー、ピーク時)
-   請求額を見積もる
-   監査を実行する

監視は 1 つのツールまたは一連のツールを使用して実行します。使用する
ツールは、アプリケーションで使用されているプラットフォームや言語、さらに監視の目標および要件によって異なります。

**Microsoft System Center Monitoring Pack for Azure アプリケーション**

この [Monitoring Pack] を使用すると、[Microsoft System Center 
Operations Manager] を使って Azure アプリケーションの
可用性とパフォーマンスを監視できます。

Microsoft System Center Operations Manager 2007 は、Windows 
Azure アプリケーションの正常性監視に最適なツールです。

**Azure Platform 管理ツール**

[Azure Platform 管理ツール (MMC)] というツールもあります。
このツールを使用すると、Azure のホステッド サービスおよび
ストレージ アカウントを管理できます。このツールには、サンプルとして
完成したソース コードが用意されているので、Azure の管理および
診断 API を使用して、さまざまな管理タスクや構成タスクを実行する方法を
確認できます。

**Cerebrata ツール**

Cerebrata は、Azure アプリケーションの監視および管理に使用
できるツールを多数提供しています。これには、[Azure Diagnostics 
Manager]、[Cloud Storage Studio]、および [Azure 管理コマンドレット] 
が含まれます。

Azure Diagnostics Manager 2 は、Azure 診断を管理するための 
Windows (WPF) ベースのクライアントです。このクライアントを使用すると、
Azure で実行中のアプリケーションによって収集された診断データを
表示、ダウンロード、および管理できます。これらの製品の詳細については、
[http://www.cerebrata.com](http://www.cerebrata.com) を参照してください。

Cloud Storage Studio 2 は、Azure のストレージを管理するための 
Windows (WPF) ベースのクライアントです。

Azure 管理コマンドレットは、Azure のストレージ、ホステッド 
サービス、SQL データベース インスタンス、および診断を管理するための
一連の Windows PowerShell コマンドレットです。ストレージ アカウント
をバックアップおよび復元するためのコマンドレットも用意されています。

**ネットワーク監視: AlertBot、Gomez、Keynote、Pingdom**

Compuware の [Gomez] Application Performance Management、
[Keynote]、[Pingdom]、および [AlertBot] は、Azure 
アプリケーションのリモート管理ソリューションです。こうした管理
ソリューションにより、アプリケーションの可用性を監視し、パフォーマンス
を最適化できます。Pingdom などの一部のサービスでは、エラーの検出時に
電子メール、SMS、またはデスクトップ アプリケーションによって通知する
ことができます。この種類の監視では、エンド ユーザーが適切にアプリケー
ションを監視するために行う動作がシミュレートされます。

**Apica ツール**

Apica は、Azure の Web アプリケーションを "外側から" 監視するツールを提供しています。詳細については、<a href="http://www.apicasystem.com/integration-partners/">http://www.apicasystem.com/integration-partners/</a> を参照してください。


**AVIcode**

AVIcode は現在、Microsoft の一部として Microsoft System Center に
所属しています。[AVIcode] は、.NET アプリケーションのパフォーマンス
監視機能と、包括的な一連のアプリケーション監視機能を提供します。

**パフォーマンスのプロファイル**

Azure アプリケーションが Azure で実行されている場合は、
そのアプリケーションの[プロファイル]を作成して、パフォーマンスの問題を
確認できます。Visual Studio から Azure アプリケーションを公開する
場合は、アプリケーションのプロファイル作成を選択し、必要なプロファイル
設定を指定できます。

**Azure VM Assistant**

[VM Assitant] ツールは、仮想マシンのインスタンスにリモート デスクトップ
接続するときに、トラブルシューティングの関連データすべてを 1 か所に
まとめる CodePlex プロジェクトです。**[VM Health]** ボタンを使用すると、
インスタンスの現在の状態を確認できます。

###可能な場合はエラーをデバッグ

アプリケーションを Azure に展開する前に、アプリケーションを
ローカルでデバッグすることをお勧めします。Azure SDK には、
Azure クラウド環境を模倣するエミュレーターが含まれるので、
アプリケーションを展開しなくても、アプリケーションを実行して基本的な
テストを行うことができます。使用するデバッグ ツールは、使用している
プログラミング言語と開発ツールによって異なります。

アプリケーションが展開されたら、Visual Studio などのデバッガーを使用してクラウド内でデバッグできます。そのためにはデバッグ ビルドを作成して展開する必要があります。これを行うには、特定のロール インスタンスに接続します。複数のロールとロール インスタンスが含まれる複雑なアプリケーションがある場合、どのロール インスタンスに接続するかを判断するのが非常に難しいことがあります。Visual Studio Ultimate では IntelliTrace がサポートされており、これにより .NET ロールでデバッグ情報を追跡できます。問題が発生したら、この情報をダウンロードし、Visual Studio に読み込むことができます。各ロール インスタンスの IntelliTrace ログを確認すると、問題が発生した場所がわかります。クラウドでのデバッグには多少の欠点があるものの、この方法が必要になる状況もいくつかあります。まず、すべての Azure サービスがエミュレーターを備えているとは限りません (サービス バスなど)。また、サポートされているすべての開発ツールにエミュレーターが付属するわけでもありません (Mac、Linux など)。

アプリケーションをローカルでデバッグしたら、ほとんどの場合は、
アプリケーションに組み込まれているインストルメンテーションを使って、
問題が発生している場所を確認する必要があります。

**Node.js デバッグ**

Node.JS アプリケーションのデバッグには Node-Inspector ツールを使用でき
ます。このツールは [GitHub](https://github.com/dannycoates/node-inspector) で入手できます。Node-Inspector は、
Azure のストレージ エミュレーターを使用して、開発用コンピューター
でローカル実行できます。詳細については、Jim Wang 氏のブログ
「[Debugging Node in the Azure Emulator (Azure エミュレーターでのノードのデバッグ)]」

Azure でアプリケーションをデバッグする場合は、IISNode の完全バージョンを 
[GitHub](https://github.com/windowsazure/iisnode/downloads) から Web 
ロール、worker ロール、または VM インスタンスにインストールします。前に
説明したように、運用環境にアプリケーションがあり、そのアプリケーションが
複数のインスタンスに拡張されている場合、この方法でのアプリケーションの
デバッグはお勧めしません。デバッグ対象のロール インスタンスまたは VM が
わからない可能性があるからです。

Web ロールで Node-Inspector を使用するには、その Web ロールにパッケージを
インストールしたうえで、通常どおりに Node-Inspector ツールを使用します。
Node-Inspector でのデバッグの詳細については、「[Debugging with 
Node-Inspector (Node-Inspector でのデバッグ)]」を参照してください。

**IntelliTrace**

Microsoft Visual Studio Ultimate には [IntelliTrace] が含まれています。
この機能を有効にすると、アプリケーションを運用環境に展開する前にデバッグ
できます。
IntelliTrace では ASP.NET および WCF アプリケーションがサポートされています。
IntelliTrace は、運用環境サービスで有効にしてもサポートされませんが、
Azure へのアプリケーションの展開後に例外を取得するために使用
することはできます。Jim Nakashima 氏のブログ記事では、[IntelliTrace を使用して Azure のクラウド サービスをデバッグする方法]について
説明されています。

**Fiddler**

[Fiddler] は、コンピューターとインターネットの間のすべての HTTP(S) 
トラフィックをログ記録する Web デバッグ プロキシです。Fiddler では、
トラフィックの調査、ブレークポイントの設定、送受信データの調整を行う
ことができます。Fiddler は、Azure のストレージのトラブル
シューティングに特に役に立ちます。

ローカル開発ファブリックに対して Fiddler を使用するには、127.0.0.1 では
なく ipv4.fiddler を次のように使用します。

-   Fiddler を起動します。
-   開発ファブリックでサービスを起動します。
-   http://ipv4.fiddler:/ に移動します。Fiddler が要求をトレースします。

ローカル開発ストレージに対して Fiddler を使用するには、Fiddler を指定する
ようにサービス構成ファイルを変更します。

ServiceConfiguration.cscfg ファイルを開き、接続文字列を次のように変更
します。

	Value="UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://ipv4.fiddler"

-   Fiddler を起動します。
-   サービスを起動します。Fiddler はすべてのストレージ要求をトレースします。

##トラブルシューティングと Azure ホスティング モデル##
このセクションでは、さまざまな Azure ホスティング モデルを使用したアプリケーション デバッグのベスト プラクティスについて説明します。
<h2><a id="Websites"></a>Azure の Web サイト</h2>

サポート可能な Azure の Web サイトを設計するときは、可能
であれば、このドキュメントで前に説明した推奨事項に従ってください。これ
には、エラーのチェックと処理、一時的な障害の再試行ロジック、トレース、
およびログが含まれます。Azure の Web サイトのトラブルシューティング
は、アプリケーション エラーが表示されるように Web サイトを構成し、Web 
サイト用に診断を構成したうえで、診断データを収集してそのデータを分析し、
問題を特定して解決することで完結します。

Azure の Web サイトでは、次の診断オプションを構成できます。

-   Web サーバーのログ記録
-   詳細なエラー メッセージ
-   失敗した要求トレース

これらのトピックの詳細については、「[Troubleshooting a Azure 
web site (Azure の Web サイトのトラブルシューティング)]」を参照
してください。

Azure の Web サイトの Web サーバー ログが有効になっている場合、
すべての HTTP トランザクションが [W3C 拡張ログ ファイル形式]を使用して
ログ ファイルに記録されます。このログ ファイルは、[Log Parser] を使用して
照会できます。[Log Parser Plus] および [TechNet の Log Parser サンプルページ]では、Log Parser のサンプル クエリをいくつか入手できます。Office 
2007/2010 が実行されているコンピューターで CHART 出力の種類を生成する
必要がある場合は、[Log Parser Plus](http://logparserplus.com/article/2) 
に記載されている手順に従って、[Office 2003 Web Components] を
インストールします。

Azure の Web サイトでは、IIS 7.0 以降で利用できるものと同じ、
"失敗した要求トレース" 機能を使用できます。ただし、これは IIS の "失敗
した要求トレース" のように構成可能ではありません。Azure の 
Web サイトに対して "失敗した要求トレース" を有効にすると、**すべて**の
失敗した要求がキャプチャされます。

<h2><a id="Cloudservices"></a>Azure のクラウド サービス</h2>

前に説明したように、分散型の性質を持つ Azure のクラウド サービス
で重要なのは、呼び出しを非同期的に行い、一時的なエラーの発生時に再試行を
処理することによる、アプリケーションの保護です。

Azure のクラウド サービスで使用されるデバッグ手法は、発生している問題の種類によって異なります。特定のロールまたはロール インスタンスに関連する問題、たとえば、ロールを開始できない、ロールが繰り返す、などの問題については、リモート デスクトップを使用して調査するのが最適です。この場合は、問題のあるロールまたはロール インスタンスが通知され、影響を受けるロールに接続できます。問題が発生しているが、原因となっているロール インスタンスがわからない場合は、トレースおよびログを使ってトラブルシューティングを行うことをお勧めします。Azure 診断には、トレースとログ情報を収集および管理するメカニズムが用意されています。

Azure SDK 1.7 には新しいデバッグ機能がいくつか追加されました。たとえば、例外の発生時にスタック トレースが見つけやすくなり、リモート デスクトップ接続も改善されています。スタック トレースは Windows イベント ログに含まれるようになり、不具合の確認が容易になりました。さらに、モート デスクトップ接続も向上しています。ロールの繰り返しまたは中断が発生した場合は、リモート デスクトップを使用して問題のあるロールに接続し、問題を調査することができます。

Azure ポータルではデータを監視できます。これは、IT 担当者や開発者が Azure のクラウド サービスでの問題を予測および診断するのに役立ちます。既定では、"CPU 使用率"、"受信データ"、"送信データ"、"ディスク読み取りのスループット"、"ディスク書き込みのスループット" などの値がホスト VM により収集されます。これらのメトリックをロール インスタンスに対して有効にするために必要な構成はありません。また、顧客のコストへの影響もありません。また、追加のパフォーマンス情報も収集できます。詳しい診断情報を収集する場合、この診断情報は Azure のストレージに保存されるため、有効な診断接続文字列が必要です。また、これによって追加のストレージ コストが発生します。ユーザーが詳細な監視を有効にすると、ポータルにより、既定のパフォーマンス カウンター セットを収集するようにロール インスタンスがリモートで構成されます。

###Azure 診断

最初の Azure SDK 1.0 には、診断データを収集し、そのデータを 
Azure のストレージに保存する機能が含まれていました。この機能はまとめて 
Azure 診断と呼ばれています。このソフトウェアは Event Tracing for 
Windows (ETW) フレームワークに基づいて構築されており、Azure 
スケールアウト アーキテクチャの次の 2 つの設計要件を満たしています。

-   インスタンスの再イメージ化中に失われる診断データを保存する。
-   複数のインスタンスからの診断用の中央リポジトリを提供する。

ロールで診断を構成したら、その特定のロールのすべてのインスタンスから診断
データが収集されます。診断データは、デバッグ、トラブルシューティング、
パフォーマンス測定、リソース使用状況の監視、トラフィック解析と容量計画、
および監査に使用できます。永続化を目的とした Azure のストレージ 
アカウントへの転送は、スケジュール設定することも、要求に応じて行うこと
もできます。

Azure 診断により、サーバー パラダイムが次の 4 つの点で大きく
変わっています。

-   アプリケーションの作成時に診断を有効にする必要がある。
-   診断結果の視覚化に特定のツール/手順が必要である。
-   診断データは、永続ストレージ (各インスタンスではなく、Azure 
    のストレージ) に書き込まれていない限りクラッシュによって失われる。
-   Azure のストレージに保存されている場合、診断ストレージによって
    月額料金が発生する。

Azure の重要なメリットの 1 つがコスト削減であるため、コストは
特に重要な点です。現時点で、診断の使用コストがかからないようにするには、
データを仮想マシンに置いたままにするしかありません。
これは小規模な展開では機能しますが、インスタンスの数が多い場合は現実的
ではありません。経済的な影響を最小限に抑える方法をいくつか次に示します。

-   ストレージ アカウントがアプリケーションと同じデータ センターにあること
    を確認します。なんらかの理由により同じデータ センターにない場合は、
    スケジュール設定された転送の間隔を効率的に指定します。転送間隔が短いと
    データの関連性が向上しますが、そのトレードオフとして発生する追加の
    帯域幅や処理のオーバーヘッドを補うほどのメリットは得られない可能性が
    あります。
-   Azure のストレージから定期的に診断データをコピーしてクリア
    します。診断データは Azure のストレージを介して転送され
    ますが、不必要にそこに置いておくことはありません。これを処理するための
    ツールは複数あります。たとえば、System Center Monitoring Pack for 
    Azure、Cerebrata の Azure Diagnostics Manager、Windows 
    Azure PowerShell コマンドレットを使用することができます。
-   アプリケーションのトラブルシューティングと監視に必要になる診断データ
    のみを選択します。キャプチャするデータが多すぎると、コストが大幅に
    増加するほか、トラブルシューティングがさらに困難になることも
    あります。
-   アプリケーションにオンデマンド切り替えを実装することで、診断データの
    収集と範囲を制御します。
-   ログ レベル (詳細、情報、警告、エラー) を活用してすべての情報を利用
    可能にし、展開後の診断構成を使用して、データを選択的に収集します。

<h2><a id="Vms"></a>Azure の仮想マシン</h2>

Azure の仮想マシンで実行されているアプリケーションのトラブル
シューティングでは、通常、使用中のオペレーティング システムやプラット
フォームに対するトラブルシューティングと同じ手法を使用します。
次に例を示します。

-   Windows Server 2008 R2 x64 (RTM および SP1)、Windows 8 Service x64 
    (Datacenter および Core)。この件に関して膨大な情報を利用できます。また、
    ジョブを迅速に行うためのツールも多数用意されています。

-   アプローチの詳細については、「[方法: Windows サービス アプリケーションをデバッグする]」を参照してください。

-   ベスト アプローチを説明するビデオについては、[Daniel Pearson 氏のプレゼンテーション「Windows Debugging and Troubleshooting」(Windows のデバッグとトラブルシューティング)]を参照してください。

-   Open Suse Linux。Suse Linux でのアプリケーションのトラブルシューティング
    に使用できる膨大なリソースが、[Suse Linux のドキュメント]とインターネット
    の両方に用意されています。
-   Ubuntu Linux。ここにも、大量の情報が存在します。製品のドキュメントを確認
するには、[https://help.ubuntu.com/](https://help.ubuntu.com/)https://help.ubuntu.com/ を参照してください。
-   CentOS Linux。詳細については、[http://centos.org/](http://centos.org/) を参照してください。

<h2><a id="PlatformServices"></a>Azure サービスのトラブルシューティング</h2>

Azure SQL データベース、Azure Active Directory、
Azure のストレージなど、Azure サービスの多くに、それぞれ
の用途に特化したトラブルシューティングに関するアドバイスがあります。
これらのアドバイスは、アプリケーションが Azure で実行されているか
どうか、どのプログラミング言語またはライブラリを使用して構築されているか、
または Microsoft オペレーティング システム以外で実行されているかどうかに
依存しません。これらのサービスの一部に関するベスト プラクティスを
以下に示します。

このドキュメントの設計に関するトピックで示したような非同期呼び出し、
トレース、およびイベント ログのベスト プラクティスが実装された、
サポートされているライブラリも多数あります。

####Azure のストレージのトラブルシューティング

次のリンクでは、トラブルシューティングを必要とする問題を緩和するための
設計または作業、あるいはトレースやログの作業を追加する必要がある場所
について説明します。

- ストレージのステータス コードとエラー コード: [http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179382.aspx](http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179382.aspx)

- Storage analytics: [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx) (下にある 3 つのリンクを参照)

- Overview of retry policies in the Azure Storage Client Library (Azure のストレージ クライアント ライブラリの再試行ポリシーの概要): [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx)

- How to get the most out of Azure Tables (Azure テーブルを最大限に活用する方法): [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx)

	- 次のセクションを参照してください: 「Best Practices (ベスト プラクティス)」、「Tips for Programming Azure Tables (Azure テーブルのプログラミングのヒント)」、「Timeouts and ServerBusy - Is it normal? (タイムアウトと ServerBusy - これって正常?)」

- Protecting your Blobs against application errors (アプリケーション エラーからの BLOB の保護): [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx)

**Azure ストレージ エクスプローラー**

Azure のストレージ内の詳細を確認する方法は複数あります。
Azure のストレージ チームは、[ストレージ エクスプローラーの一覧]
を用意しました。これを使用すると、診断ファイルおよび Azure の
Storage Analytics ファイルを確認できます。Cloudberry Lab の [Azure 
BlOB ストレージのエクスプローラー]には、**[Storage Settings]** 
をクリックしてアプリケーションで Storage Analytics を直接有効にする
ユーザー インターフェイスが用意されています。

<h2><a id="Misc"></a>Azure のサービス バスのトラブルシューティング</h2>

このセクションでは、Azure のサービス バスを堅牢かつ維持可能
な方法で使用するアプリケーションの開発方法について大まかに説明します。
これにより、一般的な問題の発生を最小限に抑えることができます。また、
一般的なサービス バス エラーを特定し、対処する方法について詳しく説明
します。

###一般的なガイダンス

サービス バスは、インターネット規模の[エンタープライズ サービス バス]で、リレー
型メッセージング機能と仲介型メッセージング機能がサポートされています。この両方
の種類のメッセージングに対して、クォータとしきい値がシステム レベルで実装されて
います。アプリケーションがクォータを超えると、アプリケーションが調整されるか、
要求またはメッセージが拒否されます。サービス バスのクォータと、そのクォータを
超えた場合の動作の詳細については、「[Azure のサービス バスのクォータ]」
を参照してください。クォータの中には、キューまたはトピックのサイズのように
ユーザー定義のものがあります。こうしたクォータは、エンティティの作成時に定義
します。

サービス バス メッセージング エンティティのデータと、そのデータがどのように処理
されているか確認するには、Azure Tools for Visual Studio (バージョン 
1.7 以上) の[サービス バス エクスプローラー]またはサーバー エクスプローラーを
使用します。こうしたツールにより、キュー、トピック、サブスクリプション、および
ルールを作成、削除、テストできます。実行中のアプリケーションが、期待される方法
でデータをまだ処理していない場合、アプリケーションのトラブルシューティング方法
として、これらのツールが適しています。これらのツールでは、キュー、トピック、
およびリレー エンティティのテスト、個人の送受信者のタスクによって実行された操作
のトレース、進行中のテスト実行の進捗状況とパフォーマンスの監視、およびエラー 
メッセージを含めた結果の詳細ログの生成を行う機能がサポートされています。

###サービス バス リレー

サービス バス "リレー" サービスはクラウドで実行され、SOAP、WS-**、REST など、
さまざまなトランスポート プロトコルと Web サービス標準をサポートしています。
サービス バス リレーをデリゲートとして使用すると、WCF サービスに送信された受信
セッションおよび受信要求をリッスンできます。リレー型メッセージング パターンでは、
内部設置型サービスまたはクラウドベースのサービスが送信ポートを介してリレー 
サービスに接続され、特定のランデブー アドレスに関連付けられた双方向通信用ソケット
が作成されます。クライアントはサービスの場所を把握している必要はありません。
また、内部設置型サービスについては、ファイアウォールで受信ポートを開いておく必要
はありません。ただし、ネットワーク構成によっては、ファイアウォールの背後から、
またはプロキシ サーバーを介してサービス バス リレーに接続するときに問題が発生する
場合があります。「[サービス バスでファイアウォール背後にホスト]」には、
このような接続の問題のトラブルシューティングおよび解決方法が記載されています。

###サービス バスのキューとトピック

サービス バスのキューとトピックでは、仲介型メッセージング機能が提供され
ます。この機能によりメッセージはサービス バスのキューまたはトピックに
プッシュされ、受信者がそのメッセージを使用する用意ができるまで確実に保持
されます。メッセージの送信者と受信者が同時にオンラインになっている必要は
ありません。このメッセージング インフラストラクチャでは、メッセージは、コン
シューマー側で受信できるようになるまで確実に保持されます。メッセージング 
API では、アプリケーションに影響する可能性があるさまざまなエラーに対処する
必要があります。これらのエラーを大まかに分類すると次のようになります。

-   ユーザー エラー - 例: 引数が無効だったことを示すコード。
    推奨アクション: 処理を実行する前にコードの修正を試みます。
-   セットアップ/構成エラー - 例: アクションに関連付けられたキューまたは
    トピックが存在しない、または削除されている。推奨アクション: 構成を確認
    し、必要に応じて変更します。
-   一時的なエラー - 例: 現在の時刻でサービスが要求を処理できなかったことを
    示す応答。推奨アクション: 操作をやり直すか、ユーザーに通知します。詳細
    については、「[一時的な通信エラーの処理]」を参照してください。
-   その他のエラー - 例: タイムアウト エラーまたはメッセージのロックが失われ
    たことを示すエラー。推奨アクション: 一般的に、クリーンアップまたは中止
    の目的でこれらの例外を処理することはありません。これらのエラーはトレース
    用に使用される可能性があります。

「[メッセージング例外]」では、.NET 用サービス バス クライアント ライブラリ
で扱う例外の概要と、その処理方法に関する推奨事項を例外の種類ごとに紹介して
います。.NET 用のクライアント ライブラリは他の言語のサービス バス 
ライブラリの構造に合わせて調整されているので、このガイダンスは、プログラ
ミングに .NET 言語を使わない場合でも役に立つことがあります。一時的なエラー
などのように、アクションを再試行できる場合もあります。一時的なエラーを
効率的に処理するには、この記事で前述した一時的なエラーの処理に関する
ガイドラインに従ってください。また、.NET アプリケーションの一時的な 
サービス バス エラーを処理する方法の詳細、ベスト プラクティス、および
サンプル コードについては、Azure 開発者ガイダンスの記事
「[Azure のサービス バスの仲介型メッセージング API を利用するためのベスト プラクティス]」の「[一時的な通信エラーの処理]」を参照してください。

仲介型メッセージングを使用するアプリケーションを開発する場合は、メッセージ
に示された異常性を正確かつ効率的に処理できるように、信頼性の高いメッセージ
受信ロジックを実装するという点にも集中して取り組む必要があります。
「[Azure のサービス バスの仲介型メッセージング API を利用するためのベスト プラクティス]」の「Implementing Reliable Message Receive Loops (信頼性の高いメッセージ受信ループの実装)」では、**PeekLock** 受信モードを使用するための手法がいくつか取り上げられています。このモードでは、メッセージ
が 1 回で適切に配信されなかった場合に、複数回メッセージ配信が実行されます。
この記事が推奨するベスト プラクティスは、アプリケーションがメッセージを
重複して処理しないようにするために役立ちます。また、ロックのタイムアウト
により発生する問題を回避し、メッセージを直ちに処理することで **PeekLock** 
モードの全体的なパフォーマンスを向上させるのにも役立ちます。この記事には、
.NET 用のサービス バス クライアント ライブラリを使用するサンプル コードも
用意されています。

###その他のトラブルシューティング リソース

一般的なサービス バス エラーの詳細と、そのエラーを調査て対処する方法に
ついては、「[サービス バスのトラブルシューティング]」を参照してください。


##Azure Active Directory アクセス制御サービス (ACS)

-   エラー コード: 
    [http://msdn.microsoft.com/ja-jp/library/windowsazure/gg185949.aspx](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg185949.aspx)
-   ACS サービスの制限事項: 
    [http://msdn.microsoft.com/ja-jp/library/windowsazure/gg185909.aspx](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg185909.aspx)

<h2><a id="SQLTroubleshooting"></a>Azure SQL データベースのトラブルシューティング</h2>

Azure SQL データベースを操作している場合、分散型の性質を持つ 
Azure SQL データベース アプリケーションを処理するときは特に注意
が必要です。このセクションでは、注意を要する点をいくつか取り上げて説明
します。ただしすべてを網羅しているわけではありません。サポートしやすい 
Azure SQL データベース コードを記述する鍵は、リターン コードを
調べて、確実にエラーを処理できる確かな再試行コードを用意することです。

アプリケーションは、ログイン エラーを正しく処理する必要があります。また、
SQL データベース インスタンスでは SQL 認証を使用する必要があります。正常に
ログインできない場合は、資格情報が有効でないか、要求したデータベースが利用
可能な状態ではないことが考えられます。

アプリケーションは、アクセスできないサービスを処理する必要があります。
サーバーが既にプロビジョニングされ、Azure SQL データベース 
サービスを利用できる場合 ([Azure の作動状態に関するページ]で確認できます)、
原因として考えられるのはオンサイト インストールにおける構成の問題です。
たとえば、名前を解決できない (tracert などのツールでテストできます)、SQL 
データベースが使用しているポート 1433 がファイアウォールによってブロック
されている、適切に構成されていないプロキシ サーバーを使用している、などが
考えられます。これらの問題のトラブルシューティングを行うには、SQL Server 
に対する手法と同じものを使用します。詳細については、「[SQL Database 
Connectivity Troubleshooting Guide]」(SQL データベース接続のトラブル
シューティング ガイド) および「[Azure SQL データベースのトラブルシューティング]」を参照してください。

アプリケーションは、一般的なネットワーク エラーを処理する必要があります。
Azure SQL データベースでは次の場合にユーザー接続が切断されるため、
一般的なネットワーク エラーが発生する可能性があります。

-   長時間にわたって接続がアイドル状態の場合
-   接続で過剰な量のリソースが使用されているか、長時間にわたって接続が
    特定のトランザクションに保持されている場合
-   サーバーがビジー状態の場合

Azure SQL データベースのパフォーマンスを向上させるには、SQL 
Server で使用する手法と同じものを使用します。詳細については、次のトピック
を参照してください。

-   [クエリに関するトラブルシューティング] (SQL Server オンライン ブック)
-   [Troubleshoot and Optimize Queries with SQL Database] (SQL データ
    ベースでのクエリのトラブルシューティングと最適化)
-   [SQL Database Performance Considerations and Troubleshooting] (SQL 
    データベースのパフォーマンスに関する考慮事項とトラブルシューティング)
-   [Improving Your I/O Performance] (I/O パフォーマンスの向上)
-   [SQL Server 用 System Center 管理パック]

Azure SQL データベースでは、SQL Server エラー メッセージのサブ
セットが使用されています。SQL Server エラーの詳細については、SQL Server 
オンライン ブックの「[エラーおよびイベント リファレンス (データベース 
エンジン)]」を参照してください。

ログイン名またはパスワードを復旧する必要がある場合は、サービス管理者に
問い合わせてください。サービス管理者は、サーバーおよびデータベースへの適切
なアクセスをユーザーに許可できます。また、サービス管理者は Azure 
の管理ポータルを使用してパスワードをリセットすることもできます。

SQL データベースのクエリは、形式が正しくない、ネットワークに問題がある
など、さまざまな理由で失敗します。一部のエラーは一時的なもので、この場合、
問題は自然に解消することがほとんどです。このようなエラーの場合は、少し間を
置いてからクエリを再試行します。何回か再試行し、まだクエリに失敗する場合
は、エラーをレポートします。もちろん、すべてのエラーが一時的なものである
とは限りません。SQL エラー 102 "構文が正しくありません" は、同じクエリを
何回送信しても解消されません。つまり、信頼性の高い再試行ロジックを実装する
にはある程度の配慮が必要です。ユーザー接続を切断する前に、可能であれば、
表形式データ ストリーム (TDS) エラー トークンが送信されます。アプリケー
ション エクスペリエンスを向上させるには、これらのエラーをキャッチするため
の再試行ロジックを SQL データベース アプリケーションに実装することをお勧め
します。エラーが発生したら、接続を再確立し、失敗したコマンドまたはクエリ
を再実行します。詳細については、次のリンクを参照してください。

-   [Retry Logic for Transient Failures in SQL Database] (SQL データベース
    の一時的な障害の再試行ロジック)
-   [SQL Database Retry Logic Sample] (SQL データベースの再試行ロジック
    のサンプル)
-   [The Transient Fault Handling Application Block] (一時的なエラー処理
    アプリケーション ブロック)

###Azure SQL のバックアップおよび復元戦略

Azure SQL データベースには独自のバックアップおよび復元戦略が必要
です。利用できる環境やツールは既にあるためです。データベースを Microsoft 
データ センターに置くことによって、いろいろな意味でリスクには既に対処済み
です。他のリスク要因については既存のツールでも対応できますが、さらに優れた
ツールが登場し、作業はますます容易になっています。SQL データベースの無料の
バックアップおよび復元ツールが、Red-gate から最近公開されました。この
ツールは、[http://www.red-gate.com/products/dba/sql-azure-backup/](http://www.red-gate.com/products/dba/sql-azure-backup/) 
から入手できます。

SQL データ同期を使用すると、データ同期 Web サイト内から双方向の同期を
簡単に作成およびスケジュール設定できます。1 行のコードも記述する必要は
ありません。詳細については、
[http://msdn.microsoft.com/ja-jp/library/windowsazure/hh456371.aspx](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh456371.aspx) 
を参照してください。

SQL データベースのバックアップおよび復元戦略の詳細については、次の記事を
参照してください。

-   SQL データベースのバックアップおよび復元戦略の概要に関するトピック: 
    [http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx](http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx)
-   データベースを同じサーバー上の他のデータベースにバックアップする方法に
    関するトピック: 
    [http://msdn.microsoft.com/ja-jp/library/windowsazure/ff951631.aspx](http://msdn.microsoft.com/ja-jp/library/windowsazure/ff951631.aspx)
-   指定されたストレージ アカウントの BLOB に既存の SQL データベース 
    インスタンスをエクスポートする方法に関するトピック: 
    [http://msdn.microsoft.com/ja-jp/library/windowsazure/hh335292.aspx](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh335292.aspx)
-   BLOB に保存されている bacpac ファイルから既存の SQL データベース 
    インスタンスをインポートする方法に関するトピック: 
    [http://msdn.microsoft.com/ja-jp/library/windowsazure/hh335291.aspx](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh335291.aspx)
-   SQL データベースのビジネス継続性機能に関するトピック: 
    [http://msdn.microsoft.com/ja-jp/library/windowsazure/hh852669.aspx](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh852669.aspx)

<h2><a id="Cache"></a>Azure の Caching</h2>
Azure のキャッシュには 2 つの種類があります。Azure の共有キャッシュとロール ベースの Azure のキャッシュ (プレビュー) です。共有キャッシュは、Caching サービスを提供するマルチテナント Azure サービスです。Azure の Caching (プレビュー) は、ロール インスタンスをホストしている仮想マシンからメモリの一部を使用して、ロールでキャッシュをホストします。Azure の Caching のトラブルシューティングを行うには、エラー コードを確認し、例外をキャッチして、キャッシュの動作を観察します。ロール ベースのキャッシュ (プレビュー) を使用している場合は、パフォーマンス カウンターも使用できます。キャッシュの問題は、通常、次のカテゴリのいずれかに分類されます。

-	クォータ関連のエラー - クォータを超えました (共有キャッシュ)
-	調整 - 追加でキャッシュされた項目をサポートするための物理メモリが十分にないときに発生します
-	削除 - 新しい項目のスペースを確保するために、アプリケーションのパフォーマンスを低下させる方法で、項目が強制的に削除されました
-	有効期限 - 有効期限が短すぎるか長すぎます

クォータ関連のエラーの詳細については、「[Azure 共有キャッシュでのクォータについて]」を参照してください。





















[監視パック]: http://www.microsoft.com/download/en/details.aspx?id=11324
[Microsoft System Center Operations Manager]: http://www.microsoft.com/ja-jp/server-cloud/system-center/operations-manager.aspx
[Azure Platform 管理ツール (MMC)]: http://wapmmc.codeplex.com/
[Azure Diagnostics Manager]: http://cerebrata.com/Products/AzureDiagnosticsManager/
[Cloud Storage Studio]: http://cerebrata.com/Products/CloudStorageStudio/
[Azure 管理コマンドレット]: http://cerebrata.com/Products/AzureManagementCmdlets/
[Gomez]: http://www.compuware.com/application-performance-management/
[Keynote]: http://www.keynote.com/solutions/
[Pingdom]: http://pingdom.com/
[AlertBot]: http://www.alertbot.com/products/website-monitoring/default.aspx
[IntelliTrace]: http://msdn.microsoft.com/ja-jp/library/dd264915.aspx
[スタートアップ タスク]: http://msdn.microsoft.com/ja-jp/library/gg456327.aspx
[AVIcode]: http://www.microsoft.com/ja-jp/server-cloud/system-center/avicode.aspx
[プロファイル]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh369930.aspx
[VM Assitant]: http://azurevmassist.codeplex.com/
[Debugging Node in the Azure Emulator (Azure エミュレーターでのノードのデバッグ)]: http://weblogs.asp.net/jimwang/archive/2012/04/17/debugging-node-node-inspector-in-the-azure-emulator.aspx
[Debugging with Node-Inspector (Node-Inspector でのデバッグ)]: http://howtonode.org/debugging-with-node-inspector

[IntelliTrace を使用して Azure のクラウド サービスをデバッグする方法]: http://blogs.msdn.com/b/jnak/archive/2010/06/07/using-intellitrace-to-debug-windows-azure-cloud-services.aspx

[Troubleshooting a Azure web site (Azure の Web サイトのトラブルシューティング)]: /ja-jp/develop/net/best-practices/troubleshooting-web-sites/
[W3C 拡張ログ ファイル形式]: http://go.microsoft.com/fwlink/?LinkID=90561
[Log Parser]: http://go.microsoft.com/fwlink/?LinkId=246619
[Log Parser Plus]: http://logparserplus.com/Examples
[TechNet の Log Parser サンプルページ]: http://technet.microsoft.com/ja-jp/library/ee692659.aspx
[Office 2003 Web Components]: http://www.microsoft.com/downloads/en/details.aspx?familyid=7287252C-402E-4F72-97A5-E0FD290D4B76&displaylang=enBlockquote
[方法: Windows サービス アプリケーションをデバッグする]: http://msdn.microsoft.com/ja-jp/library/7a50syb3%28v=vs.90%29.aspx
[Daniel Pearson 氏のプレゼンテーション「Windows Debugging and Troubleshooting」(Windows のデバッグとトラブルシューティング)]: http://technet.microsoft.com/ja-jp/edge/Video/hh867800
[Suse Linux のドキュメント]: https://www.suse.com/documentation/
[ストレージ エクスプローラーの一覧]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/17/windows-azure-storage-explorers.aspx
[Azure BlOB ストレージのエクスプローラー]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
[エンタープライズ サービス バス]: http://en.wikipedia.org/wiki/Enterprise_service_bus
[Azure のサービス バスのクォータ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee732538.aspx
[サービス バス エクスプローラー]: http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a
[サービス バスでファイアウォール背後にホスト]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee706729.aspx
[一時的な通信エラーの処理]: http://msdn.microsoft.com/ja-jp/library/hh851746(VS.103).aspx
[メッセージング例外]: http://msdn.microsoft.com/ja-jp/library/hh418082.aspx
[一時的な通信エラーの処理]: http://msdn.microsoft.com/ja-jp/library/hh851746(VS.103).aspx
[Azure のサービス バスの仲介型メッセージング API を利用するためのベスト プラクティス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh545245.aspx
[サービス バスのトラブルシューティング]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee706702.aspx
[Azure の作動状態に関するページ]: http://go.microsoft.com/fwlink/p/?LinkId=168847
[SQL Database Connectivity Troubleshooting Guide (SQL データベース接続のトラブルシューティング ガイド)]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-connectivity-troubleshooting-guide.aspx
[Azure SQL データベースのトラブルシューティング]: http://msdn.microsoft.com/ja-jp/library/ee730906.aspx
[クエリに関するトラブルシューティング]: http://msdn.microsoft.com/ja-jp/library/ms186351(SQL.100).aspx
[Troubleshoot and Optimize Queries with SQL Database (SQL データベースでのクエリのトラブルシューティングと最適化)]: http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-sql-azure.aspx
[SQL Database Performance Considerations and Troubleshooting (SQL データベースのパフォーマンスに関する考慮事項とトラブルシューティング)]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI314
[Improving Your I/O Performance (I/O パフォーマンスの向上)]: http://blogs.msdn.com/b/sqlazure/archive/2010/07/27/10043069.aspx?PageIndex=2#comments
[SQL Server 用 System Center 管理パック]: http://www.microsoft.com/ja-jp/download/details.aspx?id=10631
[エラーおよびイベント リファレンス (データベース エンジン)]: http://go.microsoft.com/fwlink/p/?LinkId=166622
[Retry Logic for Transient Failures in SQL Database (SQL データベースの一時的な障害の再試行ロジック)]: http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-sql-azure.aspx
[SQL Database Retry Logic Sample (SQL データベースの再試行ロジックのサンプル)]: http://code.msdn.microsoft.com/windowsazure/SQL-Azure-Retry-Logic-2d0a8401
[The Transient Fault Handling Application Block (一時的なエラー処理アプリケーション ブロック)]: http://msdn.microsoft.com/ja-jp/library/hh680934(PandP.50).aspx
[Microsoft Enterprise Library 5.0 Integration Pack for Azure]: http://msdn.microsoft.com/ja-jp/library/hh680918%28v=pandp.50%29.aspx
[Azure でログ記録とトレースを制御する]: http://msdn.microsoft.com/ja-jp/magazine/ff714589.aspx
[Cloud Application Framework & Extensions (CloudFx)]: http://nuget.org/packages/Microsoft.Experience.CloudFx
[Fiddler]: http://www.fiddler2.com/fiddler2/
[Azure 共有キャッシュでのクォータについて]: http://msdn.microsoft.com/ja-jp/library/gg185683.aspx
[ロール内キャッシュのトラブルシューティングと診断]: http://go.microsoft.com/fwlink/?LinkId=252730 

