<properties
   pageTitle="Azure Data Catalog のよく寄せられる質問"
   description="Azure Data Catalog: カタログのよく寄せられる質問"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="08/19/2015"
   ms.author="maroche"/>

# Azure Data Catalog のよく寄せられる質問

この記事では、Microsoft Azure Data Catalog サービスに関連する、よく寄せられる質問の回答を示します。

## Q: Azure Data Catalog とは何ですか。

A: Microsoft Azure Data Catalog は、Microsoft Azure クラウドでホストされる完全に管理されたサービスで、エンタープライズ データ ソースの登録のシステムと検出のシステムとして機能します。Azure Data Catalog は、アナリストからデータ サイエンティスト、開発者まで、すべてのユーザーによるデータ ソースの登録、検出、理解、および使用を可能にする機能を備えています。

## Q: Azure Data Catalog はどのようなお客様の課題を解決しますか。

Azure Data Catalog は、ユーザーのエンタープライズ データ ソースの検出と理解を可能にすることで、データ ソースの検出と "ダーク データ" の課題に対処します。

## Q: Azure Data Catalog の対象ユーザーはだれですか。

Azure Data Catalog は、次の技術者と技術者以外のユーザー向けの機能を備えています。

- データ開発者、BI、および分析のプロフェッショナル: 他のユーザーが使用するデータおよび分析用コンテンツの生成を担当するユーザー
-	データ スチュワード: データの意味、意図された使用方法、目的などのデータに関する知識を持つユーザー
- データ コンシューマー: 選択したツールを使用して、ジョブの実行に必要なデータを簡単に検出し、理解し、接続できる必要があるユーザー
- 中央 IT: ビジネス ユーザー向けに、数百のデータ ソースを探索可能にする必要があり、データの使用方法と使用者を監視し続ける必要があるユーザー

## Q: Azure Data Catalog の提供先の地域はどこですか。

プレビュー期間中、Azure Data Catalog サービスは次のデータ センターでのみ使用できます。

- 米国西部
- 米国東部
- 西ヨーロッパ
- オーストラリア東部

## Q: Azure Data Catalog のデータ資産の数の制限はどのくらいですか。

無償エディションの Azure Data Catalog は、5,000 の登録済みデータ資産に制限されます。

Standard Edition の Azure Data Catalog は、最大 100,000 の登録済みのデータ資産をサポートします。

## Q: サポートされているデータ ソースと資産の種類は何ですか。

プレビュー期間中、Azure Data Catalog では現在、SQL Server リレーショナル (Azure SQL DB を含む) および SQL Server Analysis Services (多次元および表形式) データベースのほか、SQL Server Reporting Services (ネイティブ モードのみ) および Oracle Database をサポートしています。

プレビュー期間中、Azure Data Catalog は、次の資産の種類をサポートしています。

- SQL Server テーブル
- SQL Server ビュー
- SQL Server Analysis Services ディメンション
- SQL Server Analysis Services メジャー
- SQL Server Analysis Services KPI
- SQL Server Analysis Services テーブル
- SQL Server Reporting Services レポート  
- Oracle Database テーブル
- Oracle Database ビュー
- Azure Storage BLOB
- Azure Storage ディレクトリ

## Q: 別のデータ ソースのサポートを要求するにはどうすればよいですか。

機能要求やその他のフィードバックは [Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)で送信することができます。

## Q: Azure Data Catalog はどのように使用を開始するのですか。

使用を開始する最適な場所は、「Data Catalog の概要」の手順に従うことです。この記事は、パブリック プレビューでの機能のエンド ツー エンドのツアーです。

## Q: データの登録方法を教えてください。

データを Azure Data Catalog に登録するには、Azure Data Catalog ポータルの [発行] 領域から Azure Data Catalog 登録ツールを起動します。Azure Data Catalog 発行アプリケーションで、Azure Data Catalog ポータルへのアクセスに使用した同じ資格情報を使用して、ログインし、登録するデータ ソースと特定の資産を選択します。

## Q: 登録されているデータ資産に対し、どのようなプロパティが抽出されますか。

具体的なプロパティはデータ ソースごとに異なりますが、一般に、Azure Data Catalog 発行サービスでは次の情報が抽出されます。

- 資産名
- 資産の種類
- 資産の説明
- 属性/列名
- 属性/列データ型
- 属性/列の説明

> [AZURE.IMPORTANT]Azure Data Catalog はデータを cloudAzure に移動またはコピーしません。データ ソースから資産を登録すると、資産のメタデータが Azure にコピーされますが、データは、既存のデータ ソースの場所に残ります。このルールの唯一の例外は、ユーザーが資産の登録時に、プレビュー レコードをアップロードするように選択した場合です。この場合、最大 20 レコードが各資産からコピーされ、**Azure Data Catalog** にスナップショットとして格納されます。

<br/>

> [AZURE.NOTE]ファーストクラス **Description** プロパティを持つ SQL Server Analysis Services などのデータ ソースに対し、**Azure Data Catalog** 発行アプリケーションはそのプロパティ値を抽出します。ファーストクラス **Description** プロパティがない SQL Server リレーショナル データベースに対しては、**Azure Data Catalog** 発行アプリケーションはオブジェクトと列の ms\_description 拡張プロパティから値を抽出します。詳細については、TechNet 「[データベース オブジェクトでの拡張プロパティの使用](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx)」を参照してください。

## Q: 新しく登録された資産が Azure Data Catalog に表示されるまでにどのくらいの時間がかかりますか。

**Azure Data Catalog** に資産を登録した後に、それらが **Azure Data Catalog** ポータルに表示されるまで、5 ～ 10 秒の時間がかかる可能性があります。

## Q: 登録したデータ資産のメタデータに注釈を付け、強化するには、どうするのですか。

登録した資産にメタデータを指定する最も簡単な方法は、**Azure Data Catalog** ポータルで資産を選択し、選択したオブジェクトのプロパティ ペインまたはスキーマ ペインで、メタデータの値を入力することです。

エキスパートやタグなどのいくつかのメタデータは、登録プロセス中に指定することもできます。**Azure Data Catalog** 発行サービスに指定された値は、その時点で登録されているすべての資産に適用されます。ポータルに最近登録されたオブジェクトの追加の注釈を表示するには、**Azure Data Catalog** 発行アプリケーションの最終画面の [**ポータルの表示**] ボタンをクリックします。

## Q: 登録済みのデータ オブジェクトを削除するにはどうすればよいですか。

**Azure Data Catalog** からオブジェクトを削除するには、ポータルでオブジェクトを選択し、[**削除**] ボタンをクリックします。これにより、オブジェクトのメタデータが **Azure Data Catalog** から削除されますが、実際の基になるデータ ソースには影響しません。

## Q: エキスパートとは何ですか。

エキスパートは、データ オブジェクトに関して、情報に基づいた、ある観点を持つ個人です。オブジェクトには、複数のエキスパートを指定できます。エキスパートは、オブジェクトの "所有者" である必要はありません。エキスパートは単純に、データをどのように使用でき、使用すべきかを知っている者です。

## Q: プレビューの SLA はどうなっていますか。

**Azure Data Catalog** プレビュー期間中は、明示的なサービス レベル アグリーメントがありません。

## Q: 問題が発生した場合に、Azure Data Catalog チームに情報を伝えるにはどうすればよいですか。

**Azure Data Catalog** フォーラムを使用して、問題を報告し、情報を共有して、質問をしてください。フォーラムは、http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409 にあります

##Q: Azure Data Catalog は興味があるこの他のデータ ソースを処理しますか。
**Azure Data Catalog** へのデータ ソースの追加には積極的に取り組んでいるところです。サポートされることを期待するデータ ソースがある場合は、[Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)で、それを提案 (またはそれが既に提案されている場合は、その指示を表明) してください。

## Q: Azure Data Catalog は Power BI for Office 365 のデータ カタログとどのように関連しますか。

**Azure Data Catalog** はデータ カタログの進化と考えることができます。**Azure Data Catalog** は、データ ソースの発行と検出のための同様の機能を提供しますが、より広範なシナリオに焦点を合わせ、Office 365 に依存しません。Azure Data Catalog の一般提供開始後すぐに、2 つのカタログは 1 つのサービスに結合されます。

## Q: ユーザーは Azure Data Catalog に資産を登録するために、どのようなアクセス許可が必要ですか。

**Azure Data Catalog** 登録ツールを実行するユーザーは、データ ソースに対し、そのソースからメタデータを読み取ることができるアクセス許可が必要です。ユーザーがプレビューも含めるように選択した場合、登録されるオブジェクトからのデータの読み取りができるアクセス許可も必要です。

## Q: Azure Data Catalog は、オンプレミスのデプロイメントにも使用できますか。

**Azure Data Catalog**は、クラウドとオンプレミスの両方のデータ ソースを操作できるクラウド サービスであり、ハイブリッド データ ソース検出ソリューションを提供します。現在オンプレミスで実行する **Azure Data Catalog** のバージョンの計画はありません。

##Q: 登録するデータ ソースから抽出するメタデータを追加したり、強化したりできますか。

**Azure Data Catalog** の機能の拡張に積極的に取り組んでいるところです。登録時にデータ ソースから抽出したいと考える追加のメタデータがある場合は、[Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)で、それを提案して (または既に提案されている場合は、賛成票を投じて) ください。将来、サード パーティが、拡張 API によって、新しいデータ ソースの種類を追加できるようにします。

## Q: 特定のユーザーだけが検出できるように、登録されているデータ資産の可視性を制限するにはどうすればいいですか。

A: Azure Data Catalog で、データ資産を選択し、[所有権の取得] ボタンをクリックします。Azure Data Catalog のデータ資産の所有者は、可視性設定を変更して、すべてのカタログ ユーザーが、所有している資産を検出できるようにするか、または特定のユーザーに可視性を制限することができます。

## Q: データ ソースの変更がカタログに反映されるように、データ資産の登録を更新するにはどうすればいいですか。

A: カタログに既に登録されているデータ資産のメタデータを更新するには、その資産を含むデータ ソースを再登録するだけです。テーブルまたはビューの列の追加や削除などのデータ ソースのすべての変更は、カタログで更新されますが、ユーザーによって提供されたすべての注釈は維持されます。

## Q: Azure Data Catalog を操作しているときに、質問をしたり、ヘルプを参照したりするにはどうすればいいですか。

問題が発生した場合、または Azure Data Catalog プレビューのサポートが必要な場合は、[Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)に投稿してください。

## Q: ここに質問の答えがありません。どうすればいいですか。

[Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)にアクセスしてください。そこで寄せられている質問に、ここでの方法が見つかります。

<!---HONumber=August15_HO8-->