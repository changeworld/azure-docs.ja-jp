<properties title="Search Service: workflow for developers" pageTitle="検索サービス: 開発者のためのワークフロー" description="検索サービス: 開発者のためのワークフロー" metaKeywords="" services="" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="" ms.date="09/23/2014" ms.author="heidist" />

# Azure Search: 開発ワークフロー

[WACOM.INCLUDE [This article uses the Azure Preview portal](../includes/preview-portal-note.md)]

この記事では、検索サービスとそのインデックスを作成して維持するためのロードマップと、いくつかのベスト プラクティスについて説明します。 

既にサービスがプロビジョニングされていることが前提となっています。まだプロビジョニングしていない場合は、「[Azure Search の使用](../search-get-started/)」を参照してください。

+ [手順 1.インデックスの作成](#sub-1)
+ [手順 2.ドキュメントの追加](#sub-2)
+ [手順 3.インデックスのクエリ](#sub-3)
+ [手順 4.インデックスとドキュメントの更新または削除](#sub-4)
+ [ストレージの設計に関する考慮事項](#sub-5)


<h2 id="sub-1">手順 1.インデックスの作成</h2>

クエリは、検索データと属性が含まれる検索インデックスを対象にします。そのため、サービスをプロビジョニングした後の最初の手順は、JSON 形式でインデックス スキーマを定義し、HTTPS PUT 要求を実行してサービスにインデックスを作成することです。 

インデックスは、アプリケーション コードで構成されます。ユーザー インターフェイスでインデックスを定義するための組み込みツールやエディターはありません。さまざまなインデックスの構築方法を示す例には、Program.cs にスキーマが指定される「[Create your first search solution using Azure Search (Azure Search による最初の検索ソリューションの作成)](../search-create-first-solution/)」、およびスタンドアロンの JSON スキーマ ファイルにインデックスを提供する「[Azure Search のスコアリング プロファイルの使用](../search-get-started-scoring-profiles)」などがあります。インデックスの作成の詳細については、MSDN の「[Create Index (Azure Search API) (インデックスの作成 (Azure Search API))](http://msdn.microsoft.com/ja-jp/library/dn798941.aspx)」を参照してください。

<h2 id="sub-2">手順 2.ドキュメントの追加</h2>

検索インデックスを作成した後で、JSON 形式で POST を実行することにより、ドキュメントをインデックスに追加できます。各ドキュメントには、一意のキーと、検索可能なデータと検索不可能なデータが含まれたフィールドのコレクションが含まれている必要があります。ドキュメント データは各フィールドのキー/値ペアのセットとして表されます。

スループットを向上させるために、ドキュメントをバッチで追加することをお勧めします。ドキュメントの平均サイズが 1 ～ 2 KB として、最大 1,000 件のドキュメントをバッチで追加できます。

POST 要求に対する全体的なステータス コードがあります。ステータス コードは、HTTP 200 (Success) または HTTP 207 (Multi-Status) (成功したドキュメントと失敗したドキュメントの組み合わせがある場合) です。POST 要求に対するステータス コードに加えて、Azure Search では各ドキュメントのステータス フィールドが維持されます。バッチ アップロードの場合、各ドキュメントの挿入が成功したか失敗したかを示す、ドキュメントごとのステータスを取得する方法が必要です。その情報は、ステータス フィールドで提供されます。ドキュメントの読み込みが失敗すると、このフィールドは false に設定されます。

負荷が大きい場合、一部のアップロードが失敗することがよくあります。このような失敗が発生したときは、全体的なステータス コードが 207 となり、部分的な成功が示されます。インデックス作成に失敗したドキュメントでは、"status" プロパティが false に設定されます。

> [WACOM.NOTE] サービスがドキュメントを受け取ると、ドキュメントはインデックス作成のためにキューに配置され、即座に検索結果に含まれない場合があります。負荷が大きくない場合は、通常、ドキュメントのインデックスは数秒で作成されます。


<h2 id="sub-3">手順 3.インデックスのクエリ</h2>

ドキュメントのインデックスが作成された後で、検索クエリを実行できます。OData または単純なクエリ構文を使用して、一度に 1 つのインデックスを照会できます。

+	[OData expression syntax for Azure Search (Azure Search の OData 式構文)](http://msdn.microsoft.com/ja-jp/library/dn798921.aspx)
+	[Simple query syntax in Azure Search (Azure Search の単純なクエリ構文)](http://msdn.microsoft.com/ja-jp/library/dn798920.aspx)

<h2 id="sub-4">手順 4.インデックスとドキュメントの更新または削除</h2>

必要に応じて、検索インデックスに対するスキーマの変更、インデックス内からのドキュメントの更新や削除、インデックスの削除を実行できます。

インデックスを更新するときに、複数のアクション (挿入、マージ、削除) を同一のバッチに組み合わせることで、複数のラウンド トリップの必要がなくなります。現在、Azure Search では部分的な更新 (HTTP PATCH) がサポートされていないため、インデックスを更新する必要がある場合はインデックスの定義を再送信する必要があります。

<h2 id="sub-5">ストレージの設計に関する考慮事項</h2>

Azure Search は、検索操作で使用するインデックスおよびドキュメントの内部ストレージを使用します。テキスト分析およびインデックス解析は、すべての検索可能なフィールドおよび関連付けられた属性が既に利用可能であることを前提としています。

ドキュメント内のすべてのフィールドが検索可能ではありません。たとえば、アプリケーションが音楽やビデオのオンライン カタログである場合は、Azure BLOB サービスやその他のストレージ形式にバイナリ ファイルを格納することをお勧めします。バイナリ ファイル自体は検索可能ではないため、Azure Search ストレージにそれらを保存する必要はありません。その他のサービスや場所にイメージ、ビデオ、およびオーディオ ファイルを格納する必要がありますが、ファイルの場所への URL を参照するフィールドを含める必要があります。この方法で、検索結果の一部として、外部データを返すことができます。 

インデックスやドキュメントの作成の詳細については、「[Azure Search Rest API (Azure Search Rest API)](http://msdn.microsoft.com/ja-jp/library/dn798935.aspx)」を参照してください。


<!--Anchors-->
[手順 1.インデックスの作成]: #sub-1
[手順 2.ドキュメントの追加]: #sub-2
[手順 3.インデックスのクエリ]: #sub-3
[手順 4.インデックスとドキュメントの更新または削除]: #sub-4
[ドキュメント ストアの選択]: #sub-5


<!--Image references-->

<!--Link references-->
[Azure Search の使用]: ../search-get-started/
[Microsoft Azure で検索サービスを管理する]: ../search-manage/
[Azure Search による最初の検索ソリューションの作成]: ../search-create-first-solution/

