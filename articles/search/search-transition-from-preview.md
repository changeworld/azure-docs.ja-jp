<properties 
	pageTitle="API バージョン 2014* から API バージョン 2015* へのプレビューの移行" 
	description="重大な変更、および 2014-07-31-Preview または 2014-10-20-Preview 向けに記述されたコードを Azure Search API バージョン 2015-02-28 に移行する方法を説明します。" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="07/08/2015" 
	ms.author="heidist"/>

#API バージョン 2014* から API バージョン 2015* へのプレビューの移行#

以下のガイダンスは、Azure Search のプレビュー バージョンでカスタム アプリケーションを構築し、現在一般提供されている 2015-02-28 リリースに移行しているユーザー向けです。

プレビューを利用されているユーザーは、次のいずれかのプレビュー バージョンを使用された可能性があります。

- [2014-07-31-Preview](search-api-2014-07-31-preview.md)
- [2014-10-20-Preview](search-api-2014-10-20-preview.md)

Azure Search は一般公開されているので、新しいリリースに移行されることをお勧めします。2015-02-28 は、一般提供されている Azure Search リリースの公式 API バージョンです。このバージョンについては、「[MSDN ](https://msdn.microsoft.com/library/azure/dn798933.aspx)」に記載されています。

次にリリースするプレビュー バージョン [2015-02-28-Preview](search-api-2015-02-28-preview.md) には、まだ開発段階にある機能が搭載されています。[Azure Search フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)または[フィードバック ページ](http://feedback.azure.com/forums/263029-azure-search)のいずれかでプレビュー API についてフィードバックすることができます。

###移行時のチェックリスト###

- 重大な変更によって、ご利用のソリューションが影響を受けるかどうかを検討する。
- API バージョンをロックされたバージョン `2015-02-28` に移行する。このバージョンは SLA でサポートされています。そのため、問題が発生した場合はより迅速に解決できます。
- 構築、デプロイ、テストする。重大な変更を除き、検索動作では 100% のパリティが必要です。
- 実稼働環境に展開する。
- 将来の機能導入を検討できるように、新機能を評価する。Microsoft 自然言語プロセッサまたは `morelikethis` をテスト ドライブするには、再び 2015-02-28-Preview に移行します。

##API バージョン 2015* での重大な変更##

この API の最初のリリースには、オート コンプリートまたは入力に先行した候補表示機能が含まれていました。この機能は便利でしたが、プレフィックスのマッチング機能に限られていたので、検索対象は検索用語の最初の数文字のみで、フィールド内の他の部分は対象になっていませんでした。この実装は `suggestions` と呼ばれるブール型プロパティで、特定フィールドでプレフィックスのマッチング機能を有効にする場合は `true` に設定する必要がありました。

この元の実装は、[インデックス](https://msdn.microsoft.com/library/azure/dn798941.aspx)機能で定義される新しい `Suggesters` 構造を優先するために推奨されなくなりました。この構造では、挿入辞一致とあいまい一致が可能になります。名称からわかるように、挿入辞一致とあいまい一致が可能になると一致検索機能は強化されます。挿入辞一致にはプレフィックスも含まれるため、先頭の文字による一致検索も引き続き可能ですが、文字列の残りの部分も一致検索対象に含まれるよう機能が拡張されています。

前の実装 (前述のブール型プロパティ) を中止することにしたのは、新しいソリューションを構築しているユーザーが誤って導入しないようにするためです。この実装は、いずれの 2015 バージョンにも含まれておらず、下位互換性もありません。`2015-02-28` または `2015-02-28-Preview` のいずれかを使用する場合は、 新しい `Suggesters` 構造を使用して、入力に先行した候補表示クエリを有効にする必要があります。

##既存コードの移行##

この候補表示プロパティが、唯一の重大な変更です。このプロパティを使用しない場合は、 `api-version` を `2014-07-31-Preview` または `2014-10-20-Preview` のいずれかから `2015-02-28` に移行した後に、リビルドおよび再プロイできます。アプリケーションは、以前と同様に動作します。

候補表示機能を実装したカスタム アプリケーションによって、以下を実行できます。

1. すべての NuGet パッケージを更新する。
1. API バージョンを `2015-02-28` に移行する。以下のコード サンプルを使用する場合、 API バージョンは **AzureSearchHelper** クラスに含まれます。
1. インデックスを定義する `Suggestions={true | false}` 属性を JSON スキーマから削除する。
1. インデックスの下部に `Suggesters` の構造を追加する (「[実装した後](#after)」 セクションを参照)。
1. ご利用サービスに発行できるかを確認する (名前の競合を回避するために、インデックスの名前を変更しなければならない場合があります)。
1. ソリューションをリビルドし、テスト環境にデプロイする。
1. すべてのテスト ケースを実行して、ソリューションが想定どおりに動作することを確認する。
1. 実稼働環境に展開する。

「[Codeplex に記載される Adventure Works のサンプル](https://azuresearchadventureworksdemo.codeplex.com/)」にあるコードの例に、元の `Suggestions` 実装が含まれています。このサンプルを使用して、サンプル コードを使用したコード移行を練習するのをお勧めします。

次のセクションで、候補表示機能 を「[実装する前](#before)」と「[実装した後](#after)」をご覧ください。**CreateCatalogIndex()** メソッドを「[実装した後](#after)」セクションのバージョンで置き換え、ソリューションを構築およびデプロイしてから新しい機能を試してください。

<a name="before"></a>
###実装する前###

見ておわかりになるように、 `Suggestions` は、各フィールドに設定されたブール型プロパティです。`Suggestions` 属性をすべて削除します。

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false }
                }
            };

<a name="after"></a>
###実装した後###

移行されたスキーマ定義によって `Suggestions` プロパティが削除され、一番下に `Suggesters` 構造が追加されます。

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true }
                },
                suggesters = new[]
                {
                new {
                    name = "sg",
                    searchMode = "analyzingInfixMatching",
                    sourceFields = new []{"name", "productNumber", "categoryName", "modelName", "description"}
                    }
                 }
            };

##新機能とアプローチの評価##

ソリューションを移行し、期待どおりに実行されることを確認できましたら、次に示すリンク先で新機能に関する情報を参照してください。

- [Azure Search の一般提供が開始 (ブログ投稿)](http://go.microsoft.com/fwlink/p/?LinkId=528211)
- [Azure Search の最新機能について](search-latest-updates.md)
- [Azure Search とは](search-what-is-azure-search.md)

##問い合わせ##

API バージョン `2015-02-28` は SLA でサポートされています。[こちらのページ](../support/options/)にあるサポート オプションおよびリンクを使用して、 サポート チケットを申請してください。

 

<!---HONumber=Oct15_HO3-->