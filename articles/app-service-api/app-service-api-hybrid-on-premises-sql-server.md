<properties 
	pageTitle="ハイブリッド接続を使用して Azure App Service の API アプリからオンプレミスの SQL Server に接続する" 
	description="Microsoft Azure で API アプリを作成し、それをオンプレミスの SQL Server データベースに接続する"
	services="app-service\api" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="tarcher"/>

# ハイブリッド接続を使用して Azure App Service の API アプリからオンプレミスの SQL Server に接続する

ハイブリッド接続により、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) API アプリを、静的 TCP ポートを使用するオンプレミスのリソースに接続できます。サポートされているリソースには、Microsoft SQL Server、MySQL、HTTP Web APIs、Mobile Services、およびほとんどのカスタム Web サービスが含まれます。

このチュートリアルでは、新しいハイブリッド接続機能を使用してローカルのオンプレミスの SQL Server データベースに接続する App Service API アプリを[Azure プレビュー](http://go.microsoft.com/fwlink/?LinkId=529715)で作成する方法について説明します。このチュートリアルは、Azure または SQL Server を使用した経験がない読者を対象に作成されています。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、「[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 前提条件

このチュートリアルを完了するには、以下の製品が必要です。すべて無料版を利用できるため、Azure ソリューションの開発を完全に無料で始めることができます。

- **Azure サブスクリプション** - 無料サブスクリプションについては、「[1 か月間無料試用版](/pricing/free-trial/)」を参照してください。 

- **Visual Studio** - Visual Studio 2013 または Visual Studio 2015 の無料評価版のダウンロードについては、「[Visual Studio のダウンロード](http://www.visualstudio.com/downloads/download-visual-studio-vs)」をご覧ください。続行する前に、次のいずれかをインストールします。(このチュートリアルのスクリーン ショットは、Visual Studio 2013 を使用して取得しました)

- **SQL Server 2014 Express with Tools** - [Microsoft Web プラットフォーム データベースのページ](https://www.microsoft.com/ja-jp/download/details.aspx?id=42299)で Microsoft SQL Server Express の無料版をダウンロードします。このチュートリアルの後半で、[SQL Server をインストールして](#InstallSQLDB)これが適切に構成されていることを確認する方法を説明します。

- **SQL Server Management Studio Express** - これは、上記の SQL Server 2014 Express with Tools のダウンロードに含まれていますが、個別にインストールする必要がある場合は、[SQL Server Express のダウンロードのページ](https://www.microsoft.com/ja-jp/download/details.aspx?id=42299)からダウンロードしてインストールすることができます。

このチュートリアルでは、Azure サブスクリプションを持っている、Visual Studio 2013 をインストールしている、および .NET Framework 3.5 をインストールしているか有効にしていると想定しています。このチュートリアルでは、Azure のハイブリッド接続機能 (静的 TCP ポートの既定のインスタンス) を使用する構成に SQL Server 2014 Express をインストールする方法を説明します。SQL Server をインストールしていない場合は、チュートリアルを開始する前に、上記の場所から SQL Server 2014 Express with Tools をダウンロードしてください (ただし、インストールはしないでください)。

### メモ
ハイブリッド接続でオンプレミスの SQL Server または SQL Server Express のデータベースを使用するには、TCP/IP が静的ポートで有効になっている必要があります。SQL Server の既定のインスタンスは静的ポート 1433 を使用しますが、名前付きインスタンスは静的ポート 1433 を使用しません。

オンプレミス ハイブリッド接続マネージャーのエージェントをインストールするコンピューターの条件は、次のとおりです。

- 次を使用して Azure に送信接続できること

> <table border="1">
    <tr>
       <th><strong>ポート</strong></th>
        <th>理由</th>
    </tr>
    <tr>
        <td>80</td>
        <td>証明書の検証用の HTTP ポート (<strong>必須</strong>)。必要に応じて、データ接続に使用することもできます。</td>
    </tr>
    <tr>
        <td>使用します</td>
        <td>データ接続用 (<strong>任意</strong>)。443 への送信接続ができない場合は、TCP ポート 80 を使用します。</td>
    </tr>
	<tr>
        <td>5671 と 9352</td>
        <td>データ接続用の<strong>推奨</strong>ポート。ただし、必須ではありません。このモードでは通常、スループットが高くなります。これらのポートへの送信接続ができない場合は、TCP ポート 443 を使用します。</td>
	</tr>
</table>

- 内部設置型のリソースの *hostname*:*portnumber* に到達できること 

この記事の手順では、内部設置型のハイブリッド接続のエージェントをホストするコンピューターからブラウザーを使用していると想定しています。

上記の条件を満たす構成および環境に既に SQL Server をインストールしている場合は、この手順をスキップし、「[オンプレミスの SQL Server データベースを作成する](#CreateSQLDB)」から開始できます。

<a name="InstallSQL"></a>
## SQL Server Express をインストールし、TCP/IP を有効にして、オンプレミスの SQL Server データベースを作成する

このセクションでは、API アプリで [Azure プレビュー ポータル](https://portal.azure.com)を使用するために、SQL Server Express をインストールし、TCP/IP を有効にして、データベースを作成する方法を説明します。

<a name="InstallSQLDB"></a>
### SQL Server Express をインストールする

1. SQL Server Express をインストールするには、**SQLEXPRWT\_x64\_ENU.exe** (64 ビット バージョン) ファイルまたは **SQLEXPR\_x86\_ENU.exe** (32 ビット バージョン) ファイルのいずれかをダウンロードして、目的のフォルダーに抽出します。 

2. SQL Server Express のインストール ファイルを抽出したら、**setup.exe** を実行します。

3. **[SQL Server インストール センター]** が表示されたら、**[SQL Server の新規スタンドアロン インストールを実行するか、既存のインストールに機能を追加します]** をクリックします。

	![SQL Server インストール センター](./media/app-service-api-hybrid-on-premises-sql-server/sql-server-installation-center.png)

4. 指示に従って、**[インスタンスの構成]** ページが表示されるまで、既定の選択と設定を使用します。
	
5. **[インスタンスの構成]** ページで、**[既定のインスタンス]** を選択し、**[次へ]** をクリックします。
	
	![インスタンスの構成](./media/app-service-api-hybrid-on-premises-sql-server/instance-configuration.png)
	
	SQL Server の既定のインスタンスが静的ポート 1433 で SQL Server クライアントからの要求をリッスンします。ハイブリッド接続機能には静的ポート 1433 が必要です。名前付きインスタンスは動的ポートと UDP を使用します。これはハイブリッド接続ではサポートされません。
	
6. **[サーバーの構成]** ページの既定値をそのまま使用し、**[次へ]** をクリックします。
	
7. **[データベース エンジンの構成]** ページの **[認証モード]** で、**[混合モード (SQL Server 認証と Windows 認証)]** を選択して、パスワードを入力します。
	
	![データベース エンジンの構成](./media/app-service-api-hybrid-on-premises-sql-server/database-engine-configuration.png)
	
	このチュートリアルでは、SQL Server 認証を使用します。後で必要になるため、入力したパスワードを忘れないでください。
	
8. ウィザードの残りの手順を実行し (進みながら既定値をそのまま使用)、インストールを完了します。

9. **[SQL Server インストール センター]** ダイアログを閉じます。

### TCP/IP を有効にする
TCP/IP を有効にするには、SQL Server Express をインストールした際にインストールされた SQL Server 構成マネージャーを使用します。次に進む前に、「[SQL Server の TCP/IP ネットワーク プロトコルの有効化](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx)」の手順に従ってください。

<a name="CreateSQLDB"></a>
### オンプレミスの SQL Server データベースを作成する

1. **SQL Server Management Studio** で、インストールした SQL Server に接続します**[サーバーの種類]** には、**[データベース エンジン]** を選択します。**[サーバー名]** には、**[localhost]**、または使用しているコンピューターの名前を使用します。**[SQL Server 認証]** を選択し、前に作成した `sa` ユーザー名とパスワードでログインします。

	![サーバーへの接続](./media/app-service-api-hybrid-on-premises-sql-server/connect-to-server.png)
	
	**[サーバーへの接続]** ダイアログが自動的に表示されない場合は、左パネルの **[オブジェクト エクスプローラー]** に移動し、**[接続]**、**[データベース エンジン]** の順にクリックします。
	
2. SQL Server Management Studio を使用して新しいデータベースを作成するには、オブジェクト エクスプローラーで **[データベース]** を右クリックしてから、**[新しいデータベース]** をクリックします。
	
	![新しいデータベース メニューの作成](./media/app-service-api-hybrid-on-premises-sql-server/new-database-menu.png)
	
3. **[新しいデータベース]** ダイアログで、データベース名に「`LocalDatabase`」と入力し、**[OK]** をクリックします。
	
	![新しいデータベースを作成する](./media/app-service-api-hybrid-on-premises-sql-server/new-database.png)
	
### SQL Server テーブルを作成して設定する

1. **SQL Server Management Studio** の**オブジェクト エクスプローラー**で `LocalDatabase` エントリを展開します。

	![展開したデータベース](./media/app-service-api-hybrid-on-premises-sql-server/local-database-expanded.png)

2. **[テーブル]** を右クリックして、コンテキスト メニューから **[テーブル...]** オプションを選択します。

	![新しいテーブルのメニュー](./media/app-service-api-hybrid-on-premises-sql-server/new-table-menu.png)

3. テーブルのデザイナー グリッドが表示されたら、次の図のとおりに列情報を入力します。

	![新しいテーブルの列](./media/app-service-api-hybrid-on-premises-sql-server/table-def.png)

4. **Ctrl + S** キーを押して、新しいテーブルの定義を保存します。テーブル名の入力を求められます。「`Speakers`」と入力して、**[OK]** をクリックします。

	![新しいテーブルの保存](./media/app-service-api-hybrid-on-premises-sql-server/save-new-table.png)

5. **オブジェクト エクスプローラー**で、新しく作成した `Speakers` テーブルを右クリックして、コンテキスト メニューから **[上位 200 行の編集]** を選択します。

	![上位 200 行の編集](./media/app-service-api-hybrid-on-premises-sql-server/edit-top-200-rows.png)

6. テーブル データを入力/変更するグリッドが表示されたら、次の図に示すようなテスト データを入力します。

	![テスト データ](./media/app-service-api-hybrid-on-premises-sql-server/speakers-data.png)

## デモ API アプリを作成して Azure にデプロイする 

このセクションでは、デモ API アプリを作成する方法について説明します。

1. Visual Studio 2013 を開き、**[ファイル]、[新規]、[プロジェクト]** の順に選択します。 

2. **[Visual C#] > [Web] > [ASP.NET Web アプリケーション]** テンプレートを選択し、**[プロジェクトに Application Insights を追加する]** オプションをオフにして、プロジェクトに *SpeakersList* と名前を付け、**[OK]** をクリックします。

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project.png)

3. **[新しい ASP.NET プロジェクト]** ダイアログで、**[Azure API アプリ]** プロジェクト テンプレートを選択して、**[OK]** をクリックします。

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project-api-app.png)

4. **ソリューション エクスプローラー**で **[モデル]** フォルダーを右クリックし、コンテキスト メニューで **[追加]、[クラス]** の順にクリックします。

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-menu.png)

5. 新しいファイルに *Speaker.cs* と名前を付け、**[追加]** をクリックします。

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-class.png)

6. `Speaker.cs` ファイルの内容全体を次のコードに置き換えます。

		namespace SpeakersList.Models
		{
			public class Speaker
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

7. **ソリューション エクスプローラー**で **[コントローラー]** フォルダーを右クリックし、コンテキスト メニューで **[追加]、[コントローラー]** の順にクリックします。

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-controller.png)

8. **[スキャフォールディングの追加]** ダイアログで、**[Web API 2 コントローラー - 空]** オプションを選択し、**[追加]** をクリックします。

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-scaffold.png)

9. コントローラーに **SpeakersController** という名前を付け、**[追加]** をクリックします。

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-controller-name.png)

10. `SpeakersController.cs` ファイルのコードを次のコードに置き換えます。`connectionString` の <serverName> プレースホルダーと <password> プレースホルダーに独自の値が指定されていることを確認します。<serverName> の値は SQL Server があるマシンの名前、<password> の値は SQL Server をインストールおよび構成したときに設定したパスワードです。

	> [AZURE.NOTE]次のコード スニペットには、パスワードの情報が含まれています。これは、デモを簡潔にするためです。実際の運用環境では、コードに資格情報を格納しないでください。代わりに、「[Best practices for deploying passwords and other sensitive data to ASP.NET and Azure (ASP.NET および Azure へパスワードや機密データをデプロイするためのベスト プラクティス)](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)」をご覧ください。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using SpeakersList.Models;
		using System.Data.Sql;
		using System.Data.SqlClient;
		
		namespace SpeakersList.Controllers
		{
		    public class SpeakersController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Speaker> Get()
		        {
					// Instantiate List object that will be populated and returned
					// from this method.
		            List<Speaker> speakers = new List<Speaker>();
	
					// Build database connection string.
		            string connectionString = "Server=<serverName>;" +
		                                      "Initial Catalog=LocalDatabase;" +
		                                      "User Id=sa;Password=<password>;" +
		                                      "Asynchronous Processing=true;";
		
					// Build query string to select all three columns from the Speakers table.
		            string queryString = "SELECT Id, EmailAddress, Name FROM dbo.Speakers;";

					// Instantiate the SqlConnection object using the connection string.
		            using (SqlConnection connection = new SqlConnection(connectionString))
		            {
						// Instantiate the SqlCommand object using the connection and query strings.
		                SqlCommand command = new SqlCommand(queryString, connection);

						// Open the connection to the database.
		                connection.Open();

						// Execute the command and return a SqlDataReader object
						// that can be iterated.
		                SqlDataReader reader = command.ExecuteReader();
		                try
		                {
							// Read a row from the SqlDataReader object
		                    while (reader.Read())
		                    {
								// For each row, use the returned data to instantiate
								// and add to the List a new Speaker object.
		                        speakers.Add(new Speaker 
		                            { 
		                                Id = Convert.ToInt32(reader[0]), 
		                                EmailAddress = reader[1].ToString(), 
		                                Name = reader[2].ToString() 
		                            }
		                        );
		                    }
		                }
		                finally
		                {
							// Close the SqlDataReader object.
		                    reader.Close();
		                }
		            }
					// Return the List of Speaker objects.
		            return speakers;
		        }
		    }
		}

### Swagger UI の有効化

Swagger UI を有効にすると、呼び出すクライアント コードを記述しなくても、API アプリを簡単にテストできます。

1. *App\_Start/SwaggerConfig.cs* ファイルを開き、**EnableSwaggerUI** を探します。

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-disabled.png)

2. 次のコード行をコメント解除します。

	        })
	    .EnableSwaggerUi(c =>
	        {

3. 完了したら、ファイルが次の図のようになったことを確認します。

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-enabled.png)

### API アプリをテストする

1. API テスト ページを表示するには、**Ctrl + F5** キーを押してローカルでアプリを実行します。次のようなエラーが表示されます。

	![](./media/app-service-api-hybrid-on-premises-sql-server/error-forbidden.png)

2. ブラウザーのアドレス バーで、URL の末尾に `/swagger` を追加し、**Enter** キーを押します。これにより、前のセクションで有効にした Swagger UI が表示されます。

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui.png)

3. **[Speakers]** セクションをクリックして展開します。

4. **[GET /api/speakers]** をクリックしてそのセクションを展開します。

5. **[Try it out!]** をクリックして、前の手順でデータベースに入力したデータを表示します。

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out.png)

### API アプリのデプロイ

アプリをローカルでテストしたので、アプリを Azure にデプロイしましょう。

1. **ソリューション エクスプローラー**で、プロジェクト (ソリューションではありません) を右クリックしてから、**[発行]** をクリックします。 

	![プロジェクトの [発行] メニュー オプション](./media/app-service-api-hybrid-on-premises-sql-server/publish-menu.png)

2. **[プロファイル]** タブ、**[Microsoft Azure API Apps (プレビュー)]** の順にクリックします。

	![プロジェクトの [発行] メニュー オプション](./media/app-service-api-hybrid-on-premises-sql-server/publish-web.png)

3. **[新規]** をクリックして、Azure サブスクリプションに新しい API アプリをプロビジョニングします。

	![既存の API サービス選択ダイアログ](./media/app-service-api-hybrid-on-premises-sql-server/publish-to-existing-api-app.png)

4. **[API アプリの作成]** ダイアログで、次のように入力します。

	- **[API アプリの名前]** にアプリケーションの名前を入力します。 
	- 複数の Azure サブスクリプションがある場合は、使用するサブスクリプションを選択します。
	- **[App Service プラン]** で、既存の App Service プランを選択するか、**[App Service プランの新規作成]** を選択して新しいプランの名前を入力します。 
	- **[リソース グループ]** で、既存のリソース グループを選択するか、**[新しいリソース グループの作成]** を選択して名前を入力します。名前は一意にする必要があります。アプリ名をプレフィックスとして使用し、@ 記号を除いた Microsoft ID などの個人情報を追加することを検討してください。  
	- **[アクセス レベル]** で、**[すべてのユーザーが利用できる]** を選択します。これは API を完全にパブリックにするオプションですが、このチュートリアルでは問題ありません。[Azure プレビュー ポータル](https://portal.azure.com)を使用して後でアクセスを制限することができます。
	- リージョンを選択します。

	**[OK]** をクリックします。サブスクリプションに API アプリが作成されます。

	![Microsoft Azure Web アプリの構成ダイアログ](./media/app-service-api-hybrid-on-premises-sql-server/publish-new-api-app.png)

5. このプロセスには数分かかることがあります。プロセスが開始されたことを通知するダイアログが Visual Studio に表示されます。[確認] ダイアログで **[OK]** をクリックします。

	![API サービスの作成の開始の確認メッセージ](./media/app-service-api-hybrid-on-premises-sql-server/create-api-app-confirmation.png)

6. プロビジョニング プロセスが Azure サブスクリプションでリソース グループと API アプリを作成している間、Visual Studio の **[Azure App Service アクティビティ]** ウィンドウに進行状況が表示されます。

	![[Azure App Service アクティビティ] ウィンドウによる状態の通知](./media/app-service-api-hybrid-on-premises-sql-server/app-service-activity.png)

7. API アプリがプロビジョニングされたら、**ソリューション エクスプローラー**でプロジェクトを右クリックして **[発行]** を選択します。発行ダイアログが再び開きます。前の手順で作成した発行プロファイルが選択されます。**[発行]** をクリックしてデプロイ プロセスを開始します。

	![API アプリのデプロイ](./media/app-service-api-hybrid-on-premises-sql-server/publish2.png)

**[Azure App Service アクティビティ]** ウィンドウにデプロイの進行状況が表示され、いつ発行プロセスが完了するかが示されます。

## ハイブリッド接続および BizTalk サービスを作成する ##

1. ブラウザーで、[Azure プレビュー ポータル](https://portal.azure.com)に移動します。 

2. 左側の **[すべてを参照]** オプションをクリックします。

3. **[参照]** ブレードで、**[API アプリ]** を選択します。

4. **[API アプリ]** ブレードで、API アプリを検索してクリックします。

5. API アプリのブレードで、**[API アプリ ホスト]** の下の値をクリックします。
 
	![API App blade](./media/app-service-api-hybrid-on-premises-sql-server/api-app-blade-api-app-host.png)

6. **[API アプリ ホスト]** ブレードが表示されたら、**[ネットワーク]** セクションまで下にスクロールして、**[ハイブリッド接続]** をクリックします。
	
	![ハイブリッド接続](./media/app-service-api-hybrid-on-premises-sql-server/api-app-host-blade-hybrid-connections.png)
	
7. **[ハイブリッド接続]** ブレードで、**[追加]**、**[新しいハイブリッド接続]** の順にクリックします。
	
8. **[ハイブリッド接続の作成] ブレード**で、次の手順を実行します。
	- **[名前]** に、接続の名前を入力します。
	- **[ホスト名]** には、SQL Server ホスト コンピューターのコンピューター名を入力します。
	- **[ポート]** には、`1433` (SQL Server の既定のポート) を入力します。
	- **[BizTalk サービス]** をクリックし、BizTalk サービスの名前を入力します。
	
	![Create a hybrid connection](./media/app-service-api-hybrid-on-premises-sql-server/create-biztalk-service.png)
		
9. **[OK]** を 2 回クリックします。

	処理が完了すると、**[通知]** 領域に緑色の "**SUCCESS**" という文字が点滅します。**[ハイブリッド接続]** ブレードには、状態が **[未接続]** の新しいハイブリッド接続が表示されます。
	
	![作成したハイブリッド接続](./media/app-service-api-hybrid-on-premises-sql-server/hybrid-not-connected-yet.png)
	
これで、クラウド ハイブリッド接続のインフラストラクチャの重要な部分が完了しました。次に、対応するオンプレミスの部分を作成します。

<a name="InstallHCM"></a>
## オンプレミスのハイブリッド接続マネージャーをインストールして接続を完了する

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

ハイブリッド接続のインフラストラクチャが完成したので、アプリケーションをテストしましょう。

<a name="CreateASPNET"></a>
## 完成した API アプリを Azure でテストする

1. Azure プレビュー ポータルで、[API アプリ ホスト] ブレードに戻り、**[URL]** の下の値をクリックします。
	
2. ブラウザーに API アプリのホスト ページが表示されたら、ブラウザーのアドレス バーの URL に `/swagger` を追加して、**Enter** キーを押します。
	
3. **[Speakers]** セクションをクリックして展開します。

4. **[GET /api/speakers]** をクリックしてそのセクションを展開します。

5. **[Try it out!]** をクリックして、前の手順でデータベースに入力したデータを表示します。

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out-azure.png)
	
**ご利用ありがとうございます。** Azure で実行され、ハイブリッド接続を使用してローカルのオンプレミスの SQL Server データベースに接続する、API アプリを作成しました。

## 関連項目
[ハイブリッド接続の概要](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist introduces hybrid connections (Josh Twist によるハイブリッド接続の紹介) (Channel 9 のビデオ)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[ハイブリッド接続の概要](/services/biztalk-services/)

[BizTalk Services: [ダッシュボード]、[監視]、[スケール]、[構成]、および [ハイブリッド接続] タブ](../biztalk-dashboard-monitor-scale-tabs/)

[Building a Real-World Hybrid Cloud with Seamless Application Portability (シームレスなアプリケーションの移植性を使用して実際のハイブリッド クラウドをビルドする) (Channel 9 のビデオ)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[ハイブリッド接続を使用して Azure Mobile Services からオンプレミスの SQL Server に接続する](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[Connect to an on-premises SQL Server from Azure Mobile Services using Hybrid Connections (ハイブリッド接続を使用して Azure Mobile Services からオンプレミスの SQL Server に接続する) (Channel 9 のビデオ)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[ASP.NET の ID の概要](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!---HONumber=August15_HO6-->