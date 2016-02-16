<properties
	pageTitle="ポータルでの Azure Search サービスの作成 | Microsoft Azure | クラウド検索サービス"
	description="Azure ポータルを使用して、Free または Standard の Azure Search を既存のサブスクリプションに追加します。Azure Search は、カスタム アプリケーション向けのホスト型クラウド検索サービスです。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="02/05/2016"
	ms.author="heidist"/>

# Azure ポータルでの Azure Search サービスの作成

Microsoft Azure Search は、検索機能をカスタム アプリケーションに埋め込むことができるホスト型クラウド検索サービスです。検索エンジンと検索データのストレージを提供します。これは、Azure ポータル、.NET SDK、または REST API を使用してアクセスおよび管理できます。主な機能には、オートコンプリート クエリ、あいまい一致、検索結果の強調表示、ファセット ナビゲーション、スコアリング プロファイル、多言語のサポートなどがあります。Azure Search の機能の詳細については、「[Azure Search とは](search-what-is-azure-search.md)」を参照してください。

Azure Search は、Free (Shared) から Standard までの価格レベルで利用できます。費用は、サインアップした容量によって異なります。

## Azure Search を無料でサブスクリプションに追加する

Azure Search は、管理者が共有サービスを選択することによって無料で既存の Azure サブスクリプションに追加できます。[無料試用版サブスクリプション](../includes/free-trial-note.md)にサインアップしてご検討ください。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. ジャンプバーで、**[新規]** > **[データ + ストレージ]** > **[検索]** をクリックします。

     ![][1]

3. サービス名、価格レベル、リソース グループ、サブスクリプション、および場所を構成します。これらの設定は必須であり、サービスがプロビジョニングされた後は変更できません。

     ![][2]

	- **[サービス名]** はスペースなしで、60 文字以下の小文字で、一意である必要があります。この名前は、Azure Search サービスのエンドポイントの一部として使用されます (例: "https://**my-service-name**.search.windows.net")。名前付け規則の詳細については、「[名前付け規則](https://msdn.microsoft.com/library/azure/dn857353.aspx)」を参照してください。

	- [**価格レベル**] では、容量と課金を決定します。どちらのレベルも同じ機能を提供しますが、リソース レベルが異なります。

		- **[無料]** レベルは、他のサブスクライバーと共有されているクラスター上で実行されます。Free 版はチュートリアルを試用して概念実証コードを書くには十分な機能を提供しますが、運用アプリケーションには対応していません。Free サービスは、通常は数分でデプロイできます。
		- **[標準]** レベルは専用リソースで実行され、拡張性に優れています。最初、Standard サービスは 1 つのレプリカと 1 つのパーティションを使用してプロビジョニングされますが、サービスを作成した後で容量を調整することができます。Standard サービスをデプロイするには、通常は約 15 分かかります。

	- **リソース グループ**は、一般的な目的で使用するサービスとリソースのコンテナーです。たとえば、Azure Search、Azure App Service の Web Apps 機能、Azure BLOB ストレージを使用してカスタム検索アプリケーションを構築する場合は、リソース グループを作成することで、これらのサービスをポータル管理ページにまとめておくことができます。

	- **[サブスクリプション]** では、複数のサブスクリプションがある場合に、複数のサブスクリプションから選択できます。

	- **[場所]** はデータ センターのリージョンです。現時点では、すべてのリソースは同じデータ センターで実行する必要があります。複数のデータ センターにリソースを分散させることはできません。

4. **[作成]** をクリックしてサービスをプロビジョニングします。

ジャンプバーで、通知を確認します。サービスが使用できるようになると、通知が表示されます。

<a id="sub-3"></a>
## Standard レベルの Search サービスを追加して専用リソースを手に入れる

多くのお客様は、まず Free サービスから始め、ワークロードの増大に合わせて Standard レベルに切り替えます。Standard レベルは、Azure データ センター内の本人だけが使用できる専用リソースを提供します。

Azure Search の操作には、ストレージとサービス レプリカの両方が必要です。Standard レベルでは、リソースを追加するオプションがない Free サービスとは異なり、スケールアップしてストレージまたはクエリのサポートを追加することができます。自分のワークロードにとってより重要なリソースを増やせます。

Standard レベルを使うには、その価格レベルで新しい Search サービスを作成する必要があります。この記事の前の手順を繰り返せば、新しい Azure Search サービスを作成できます。専用リソースの設定には時間がかかる場合があります (最大 15 分以上)。

Free バージョンのインプレース アップグレードはありません。スケールの拡張を可能とする Standard に切り替えるには、新しいサービスが必要です。検索アプリケーションで使用するインデックスとドキュメントをリロードする必要があります。

Standard レベルの Azure Search サービスは 1 つのレプリカとパーティションから開始されますが、リソースの上位レベルで簡単に変更できます。

1.	サービスを作成してから、サービス ダッシュボードに戻ります。

2.	[**スケール**] タイルをクリックします。

3.	スライダーを使用して、レプリカ、パーティション、または両方を追加します。

追加のレプリカおよびパーティションは、検索単位で課金されます。リソースの追加に応じて、特定のリソース構成に必要な検索単位の合計数がページに表示されます。

「[価格の詳細](http://go.microsoft.com/fwlink/p/?LinkID=509792)」をチェックして、単位あたりの課金情報を確認できます。パーティションとレプリカの組み合わせの選び方については、「[サービスの制限](search-limits-quotas-capacity.md)」を参照してください。

<a id="sub-2"></a>
## Azure Search サービスのサービス名と API キーの取得

サービスを作成した後は、Azure ポータルに戻って URL または `api-key` を取得できます。Azure Search サービスに接続するには、URL に加えて、呼び出しを認証するための `api-key` が必要になります。

1. ジャンプ バーで **[ホーム]** をクリックし、Azure Search サービスをクリックして、サービスのダッシュボードを開きます。

2. サービスのダッシュボードには、基本情報のタイルのほか、管理者キーにアクセスするためのキー アイコンが表示されます。

  	![][3]

3. サービスの URL と管理者キーをコピーします。これらは、次のタスク「[サービスが稼働していることの確認](#sub-4)」で必要になります。


<a id="sub-4"></a>
## サービスが稼働していることの確認

Azure Search 構成の最後の手順では、サービスがクライアント アプリケーションから操作およびアクセスできるかどうかを確認します。[Fiddler と Azure Search](search-fiddler.md) を使用して、サービスの可用性を確認できます。

<!--Next steps and links -->
<a id="next-steps"></a>
## 次のステップ

以上でサービスの作成は終了です。引き続き、[インデックス](search-what-is-an-index.md)の作成、[インデックスの照会](search-query-overview.md)、Azure Search を使った検索アプリケーションの作成と管理に進むことができます。

- [Azure ポータルでの Azure Search インデックスの作成](search-create-index-portal.md)

- [Azure ポータルで Search エクスプローラーを使用して Azure Search インデックスに対してクエリを実行する](search-explorer.md)

- [.NET での Azure Search の使用](search-get-started-dotnet.md)

- [.NET で Azure Search を使用する方法](search-howto-dotnet-sdk.md)

- [Microsoft Azure で検索サービスを管理する](search-manage.md)




<!--Anchors-->
[Find the service name and api-keys of your Azure Search service]: #sub-2
[Upgrade to the standard tier]: #sub-3
[Test service operations]: #sub-4
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG

<!---HONumber=AcomDC_0211_2016-->