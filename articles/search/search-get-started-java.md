<properties
	pageTitle="Java での Azure Search の使用 | Microsoft Azure"
	description="プログラミング言語として Java を使用してカスタム Azure Search アプリケーションを作成する方法を説明します。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="06/24/2015"
	ms.author="heidist"/>

# Java での Azure Search の使用

検索エクスペリエンスとして Azure Search を使用するカスタム Java 検索アプリケーションを作成する方法を説明します。このチュートリアルでは、[Azure Search サービス REST API](https://msdn.microsoft.com/library/dn798935.aspx) を使用して、この演習で使用するオブジェクトおよび操作を作成します。

このサンプルをビルドしてテストするには次のソフトウェアを使用しました。

- [Eclipse IDE for Java EE Developers](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar)。EE バージョンを必ずダウンロードしてください。検証手順の 1 つで、このエディションにしかない機能が必要です。

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

このサンプルを実行するには、Azure Search サービスが必要です。このサービスには、[Microsoft Azure 管理ポータル](https://portal.azure.com)でサインアップできます。

> [AZURE.TIP]このチュートリアルのソース コードは、Github の [Azure Search Java デモ](http://go.microsoft.com/fwlink/p/?LinkId=530197)からダウンロードしてください。

## データについて

このサンプル アプリケーションでは、[United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) からのデータをロードアイランド州でフィルター処理してデータサイズを削減して使用します。このデータを使用して、病院や学校などの目立つ建物および河川、湖沼、山などの地理的特徴を返す検索アプリケーションを作成します。

このアプリケーションでは、**SearchServlet.java** プログラムは [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx) コンストラクトを使用してインデックスを作成して読み込み、パブリック Azure SQL Database からフィルター処理された USGS データセットを取得します。オンライン データ ソースに対する定義済みの資格情報と接続情報は、プログラム コードで提供されます。データ アクセスに関しては、これ以上の構成は必要ありません。

> [AZURE.NOTE]このデータセットにフィルターを提供し、無料価格レベルのドキュメントを 10,000 件未満に制限しました。標準レベルを使用する場合は、この制限は適用されず、より大きなデータセットを使用するようにこのコードを変更できます。各価格レベルの容量の詳細については、「[制限および制約](https://msdn.microsoft.com/library/azure/dn798934.aspx)」を参照してください。

## プログラム ファイルについて

このサンプルに関連するファイルの一覧を次に示します。

- Search.jsp: ユーザー インターフェイスを提供します
- SearchServlet.java: メソッドを提供します (MVC のコントローラーに似ています)
- SearchServiceClient.java: HTTP 要求を処理します
- SearchServiceHelper.java: 静的メソッドを提供するヘルパー クラスです
- Document.java: データ モデルを提供します
- config.properties: Search サービスの URL と API キーを設定します
- Pom.xml: Maven が依存します


## サービスの作成

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. ジャンプバーで、**[新規]** > **[データ + ストレージ]** > **[検索]** をクリックします。

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
## Azure Search サービスのサービス名と API キーの取得

サービスを作成した後は、ポータルに戻って URL および `api-key` を取得できます。Search サービスに接続するには、URL に加えて、呼び出しを認証するための `api-key` が必要になります。

1. ジャンプ バーで **[ホーム]** をクリックし、Search サービスをクリックして、サービスのダッシュボードを開きます。

2. サービスのダッシュボードには、基本情報のタイルのほか、管理者キーにアクセスするためのキー アイコンが表示されます。

  	![][3]

3. サービスの URL と管理キーをコピーします。後で **config.properties** ファイルに追加するときに必要になります。

## サンプル ファイルのダウンロード

1. Github で [AzureSearchJavaDemo](http://go.microsoft.com/fwlink/p/?LinkId=530197) に移動します。

2. **[Download ZIP]** をクリックして .zip ファイルをディスクに保存した後、すべてのファイルをコンテナーに抽出します。後でプロジェクトを探しやすいように、Java ワークスペースにファイルを抽出してください。

3. サンプル ファイルは読み取り専用です。フォルダーのプロパティを右クリックし、読み取り専用の属性をオフにします。

以降のすべてのファイル変更および実行ステートメントは、このフォルダー内のファイルに対して行われます。

## プロジェクトのインポート

1. Eclipse で、**[File]**、**[Import]**、**[General]**、**[Existing Projects into Workspace]** の順に選択します。

    ![][4]

2. **[Select root directory]** で、サンプル ファイルを含むフォルダーを参照します。.project フォルダーが含まれるフォルダーを選択します。プロジェクトが選択項目として **[Projects]** の一覧に表示されます。

    ![][12]

3. **[完了]** をクリックします。

4. **Project Explorer** を使用して、ファイルを表示および編集します。まだ開いていない場合は、**[Window]**、**[Show View]**、**[Project Explorer]** の順にクリックするか、またはショートカットを使用して開きます。

## サービスの URL と API キーの構成

1. **Project Explorer** で、**config.properties** をダブルクリックして、サーバー名と API キーを含む構成設定を編集します。

2. この記事で前述の手順を参照し、[Azure ポータル](https://portal.azure.com) でサービスの URL と API キーを探して、**config.properties** に入力する値を取得します。

3. **config.properties** で、「API キー」をサービスの API キーに置き換えます。次に、サービス名 (URL http://servicename.search.windows.net の最初のコンポーネント) で同じファイルの「サービス名」を置き換えます。

	![][5]

## プロジェクト、ビルド、ランタイムの環境の構成

1. Eclipse の Project Explorer で、プロジェクトを右クリックし、**[Properties]**、**[Project Facets]** の順に選択します。

2. **[Dynamic Web Module]**、**[Java]**、**[JavaScript]** を選択します。

    ![][6]

3. **[Apply]** をクリックします。

4. **[Window]**、**[Preferences]**、**[Server]**、**[Runtime Environments]**、**[Add..]** の順に選択します。

5. Apache を展開し、前にインストールした Apache Tomcat サーバーのバージョンを選択します。この例では、バージョン 8 をインストールしました。

	![][7]

6. 次のページでは、Tomcat のインストール ディレクトリを指定します。Windows コンピューターでは、通常、C:\\Program Files\\Apache Software Foundation\\Tomcat *version* です。

6. **[完了]** をクリックします。

7. **[Window]**、**[Preferences]**、**[Java]**、**[Installed JREs]**、**[Add]** の順に選択します。

8. **[Add JRE]** で、**[Standard VM]** を選択します。

10. **[次へ]** をクリックします。

11. [JRE Definition] の [JRE home] で、**[Directory]** をクリックします。

12. **[Program Files]**、**[Java]** に移動し、先にインストールした JDK を選択します。JRE として JDK を選択することが重要です。

13. [Installed JREs] で、**[JDK]** を選択します。設定は次のスクリーン ショットのようになります。

    ![][9]

14. 必要に応じて、**[Window]**、**[Web Browser]**、**[Internet Explorer]** を選択し、外部のブラウザー ウィンドウでアプリケーションを開きます。外部のブラウザーを使用すると、Web アプリケーションのエクスペリエンスがよくなります。

    ![][8]

構成タスクが完了しました。次に、プロジェクトをビルドして実行します。

## プロジェクトのビルド

1. Project Explorer で、プロジェクト名を右クリックし、**[Run As]**、**[Maven build...]** の順に選択してプロジェクトを構成します。

    ![][10]

8. [Edit Configuration] の [Goals] に「clean install」と入力し、**[Run]** をクリックします。

ステータス メッセージがコンソール ウィンドウに出力されます。[BUILD SUCCESS] と表示されれば、プロジェクトはエラーなしでビルドされています。

## アプリの実行

この最後の手順では、ローカル サーバーのランタイム環境でアプリケーションを実行します。

まだ Eclipse でサーバーのランタイム環境を指定していない場合は、最初に行う必要があります。

1. Project Explorer で **[WebContent]** を展開します。

5. **Search.jsp** を右クリックし、**[Run As]**、**[Run on Server]** の順に選択します。Apache Tomcat サーバーを選択し、**[Run]** をクリックします。

> [AZURE.TIP]既定以外のワークスペースを使用してプロジェクトを保存した場合は、サーバー起動エラーを防ぐために、プロジェクトの場所を指し示すように **[Run Configuration]** を変更する必要があります。Project Explorer で、**Search.jsp** を右クリックし、**[Run As]**、**[Run Configurations]** の順に選択します。Apache Tomcat サーバーを選択します。**[Arguments]** をクリックします。**[Workspace]** または **[File System]** をクリックして、プロジェクトを含むフォルダーを設定します。

アプリケーションを実行すると、ブラウザー ウィンドウが開き、語句を入力するための検索ボックスが表示されます。

サービスがインデックスを作成して読み込めるように、1 分程度待ってから **[Search]** をクリックします。HTTP 404 エラーが発生する場合は、もう少し長く待ってから再び試すだけで十分です。

## USGS データの検索

USGS データ セットには、ロードアイランド州に関連するレコードが含まれています。検索ボックスが空の状態で **[Search]** をクリックすると、既定で、上位 50 のエントリが取得されます。

検索語句を入力すると、検索エンジンに処理するものが提供されます。地域の名前を入力してみてください。"Roger Williams" はロードアイランド州の最初の州知事でした。多数の公園、建造物、および学校に彼の名前が付けられています。

![][11]

次のような語句も試すことができます。

- Pawtucket
- Pembroke
- goose +cape

## 次のステップ

これは、Java と USGS データセットに基づく最初の Azure Search チュートリアルです。カスタム ソリューションで使用できる他の検索機能を紹介できるように、時間をかけてこのチュートリアルを拡張する予定です。

Azure Search についての知識が既にある場合は、このサンプルを基にして、さらに調べることができます。[検索ページ](search-pagination.md)を変更したり、[ファセット ナビゲーション](../search-faceted-navigation/)を実装したりしてみてください。また、件数を追加してドキュメントを一括処理することで検索結果の表示を改善し、ユーザーが結果をページ移動できるようにすることもできます。

Azure Search を初めて使用する場合は、 他のチュートリアルも試して、作成できるものについての理解を深めることをお勧めします。他のリソースについては、[ドキュメントのページ](http://azure.microsoft.com/documentation/services/search/)を参照してください。[ビデオとチュートリアルの一覧](https://msdn.microsoft.com/library/azure/dn798933.aspx)のリンクから、さらに多くの情報にアクセスすることもできます。

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png

<!---HONumber=August15_HO8-->