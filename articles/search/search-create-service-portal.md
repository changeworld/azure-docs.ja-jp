<properties
	pageTitle="ポータルでの Azure Search サービスの作成"
	description="管理ポータルを使用して、無料または標準の Azure Search サービスを既存のサブスクリプションに追加する"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# ポータルでの Azure Search サービスの作成

Microsoft Azure Search は、検索機能をカスタム アプリケーションに埋め込むことができる新しいサービスです。検索エンジンと検索データのストレージを提供し、ポータル、.NET SDK、または REST API を使用してアクセスおよび管理できます。主な機能には、オート コンプリート クエリ、あいまい一致、検索結果の強調表示、ファセット ナビゲーション、多言語のサポートなどがあります。Search の機能の詳細については、「[Introducing Azure Search (Azure Search の概要)](fundamentals-azure-search-chappell/)」を参照してください。

##Search をサブスクリプションに追加する  

管理者は、共有サービスを選択する場合は無料で、または専用のリソースを使用する場合は標準料金で、Search を既存のサブスクリプションに追加できます。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. ジャンプバーで、[**新規**] | [**データ + ストレージ**] | [**Search**] をクリックします。

     ![][1]

3. サービス名、価格レベル、リソース グループ、サブスクリプション、および場所を構成します。これらの設定は必須であり、サービスがプロビジョニングされた後は変更できません。

     ![][2]

	- **[サービス名]** はスペースなし、15 文字以下の小文字で、一意である必要があります。この名前は、Azure Search サービスのエンドポイントの一部になります。名前付け規則の詳細については、「[名前付け規則](https://msdn.microsoft.com/library/azure/dn857353.aspx)」を参照してください。

	- [**価格レベル**] では、容量と課金を決定します。どちらのレベルも同じ機能を提供しますが、リソース レベルが異なります。

		- **[無料]** レベルは、他のサブスクライバーと共有されているクラスター上で実行されます。無料版はチュートリアルを試用して概念実証コードを書くには十分な機能を提供しますが、運用アプリケーションには対応していません。無料サービスは、通常は数分で展開できます。
		- **[標準]** レベルは専用リソースで実行され、拡張性に優れています。最初、標準サービスは 1 つのレプリカと 1 つのパーティションを使用してプロビジョニングされますが、サービスを作成した後で容量を調整することができます。標準サービスをデプロイするには、通常は約 15 分かかります。

	- **リソース グループ**は、一般的な目的で使用するサービスとリソースのコンテナーです。たとえば、Azure Search、Azure Websites、Azure BLOB ストレージを使用してカスタム検索アプリケーションを構築する場合は、リソース グループを作成することで、これらのサービスをポータル管理ページにまとめておくことができます。

	- **[サブスクリプション]** では、複数のサブスクリプションがある場合に、複数のサブスクリプションから選択できます。

	- **[場所]** はデータ センターのリージョンです。現時点では、すべてのリソースは同じデータ センターで実行する必要があります。複数のデータ センターにリソースを分散させることはできません。

4. **[作成]** をクリックしてサービスをプロビジョニングします。

ジャンプバーで、通知を確認します。サービスが使用できるようになると、通知が表示されます。

<a id="sub-2"></a>
##Azure Search サービスのサービス名と API キーの取得

サービスを作成した後は、ポータルに戻って URL または `api-key` を取得できます。Search サービスに接続するには、URL に加えて、呼び出しを認証するための `api-key` が必要になります。

1. ジャンプバーで [**ホーム**] をクリック、検索サービスをクリックして、サービスのダッシュボードを開きます。

2. サービスのダッシュボードには、基本情報のタイルのほか、管理者キーにアクセスするためのキー アイコンが表示されます。

  	![][3]

3. サービスの URL と管理者キーをコピーします。これらは、次のタスク「[サービス操作のテスト](#sub-4)」で必要になります。

<a id="sub-3"></a>
##標準レベルにアップグレードする

多くのお客様は無料サービスから開始し、実行する必要のあるワークロードに対してクエリのパフォーマンスやストレージが不十分になったら、標準レベルにアップグレードします。標準レベルは、Azure データ センター内の本人だけが使用できる専用リソースを提供します。検索操作には、ストレージとサービス レプリカの両方が必要です。標準検索にサインアップすると、状況に応じて最も重要なリソースをより多く使用できるように、サービス構成を最適化できます。

標準レベルを使用するには、この記事の前の手順を繰り返し、標準価格レベルを選択して、新しい Search サービスを作成ます。専用リソースの設定には時間がかかる場合があります (最大 15 分以上)。

無料バージョンのインプレース アップグレードはありません。スケールの拡張を可能とする標準版に切り替えるには、新しいサービスが必要です。検索アプリケーションで使用するインデックスとドキュメントをリロードする必要があります。

標準レベルの Search サービスは 1 つのレプリカとパーティションから開始されますが、リソースの上位レベルで簡単に変更できます。

1.	サービスを作成してから、サービス ダッシュボードに戻ります。

2.	[**スケール**] タイルをクリックします。

3.	スライダーを使用して、レプリカ、パーティション、または両方を追加します。

追加のレプリカおよびパーティションは、検索単位で課金されます。リソースの追加に応じて、特定のリソース構成に必要な検索単位の合計数がページに表示されます。

「[料金の詳細](http://go.microsoft.com/fwlink/p/?LinkID=509792)」をチェックして、単位あたりの課金情報を確認できます。パーティションとレプリカをどのように構成するかについては、「[制限および制約](search-limits-quotas-capacity.md)」を参照してください。

<a id="sub-4"></a>
##サービス操作をテストする

Search 構成の最後の手順では、サービスがクライアント アプリケーションから操作およびアクセスできるかどうかを確認します。サービスの可用性を確認するためのコードを使用しないアプローチについては、以下のいずれかのリンクを使用できます。

- [Azure Search で Chrome Postman を使用する方法](search-chrome-postman.md)
- [Azure Search で Telerik Fiddler を使用する方法](search-fiddler.md)

<!--Next steps and links -->
<a id="next-steps"></a>
##次のステップ

次のその他の資料では、Azure Search を使用する検索アプリケーションを作成して管理する方法について説明しています。

- [.NET で Azure Search を使用する方法](search-howto-dotnet-sdk.md)

- [Microsoft Azure で検索サービスを管理する](search-manage.md)

- [MSDN の Azure Search](http://msdn.microsoft.com/library/dn798933.aspx)

- [Channel 9 video: Introduction to Azure Search (チャネル 9 ビデオ: Azure Search の概要)](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)


<!--Anchors-->
[Find the service name and api-keys of your Azure Search service]: #sub-2
[Upgrade to standard tier]: #sub-3
[Test service operations]: #sub-4
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG

<!---HONumber=Sept15_HO2-->