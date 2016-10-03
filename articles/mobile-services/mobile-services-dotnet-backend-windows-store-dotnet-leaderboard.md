<properties
	pageTitle=".NET バックエンドを使用した Windows ストアのランキング アプリの作成 | Azure Mobile Services"
	description="Azure Mobile Services の .NET バックエンドを使用して、Windows ストアのランキング アプリを作成する方法について説明します。"
	documentationCenter="windows"
	authors="rmcmurray"
	manager="wpickett"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="glenga"/>

# Azure Mobile Services の .NET バックエンドによるランキング アプリケーションの作成

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


このチュートリアルでは、Azure Mobile Services と .NET バックエンドを使用して Windows ストア アプリケーションを作成する方法について説明します。Azure Mobile Services は、組み込まれた認証、監視、プッシュ通知、その他の機能に加え、モバイル アプリケーション作成用のクロスプラットフォーム クライアント ライブラリによって、スケーラブルで、セキュリティで保護されたバックエンドを提供します。Mobile Services 用の .NET バックエンドは、[ASP.NET Web API](http://asp.net/web-api) に基づき、REST API を作成するための最も優れた方法を .NET 開発者に提供します。

## Overview

Web API は、オープン ソースのフレームワークであり、REST API を作成するための最も優れた方法を .NET 開発者に提供します。Web API ソリューションは Azure Websites でホストするか、.NET バックエンドを使用して Azure Mobile Services でホストするか、またはカスタム プロセスでセルフホストすることもできます。Mobile Services は、特にモバイル アプリケーション用に設計されたホスティング環境です。Web API サービスを Mobile Services でホストすると、データ ストレージに加えて次の利点が得られます。

- ソーシャル プロバイダーと Azure Active Directory (AAD) による組み込み認証。
- デバイス固有の通知サービスによるアプリケーションへのプッシュ通知。
- アプリケーションからサービスへのアクセスを簡単にする包括的なクライアント ライブラリのセット。
- 組み込みのログおよび診断機能。

このチュートリアルでは、次のことについて説明します。

- Azure Mobile Services を使用して REST API を作成する。
- サービスを Azure に発行する。
- サービスを使用する Windows ストア アプリケーションを作成する。
- Entity Framework (EF) を使用して外部キー関係とデータ転送オブジェクト (DTO) を作成する。
- ASP.NET Web API を使用してカスタム API を定義する。

このチュートリアルでは、[Visual Studio 2013 の最新のアップデート](http://go.microsoft.com/fwlink/p/?LinkID=390465)を使用します。


## サンプル アプリケーションについて

*ランキング*は、ゲームのプレーヤーの一覧に、各プレーヤーのスコアと順位を示したものです。ランキングは大きなゲームの一部である場合も、個別のアプリケーションの場合もあります。ランキングは実際に使用されているアプリケーションですが、構造が単純なのでチュートリアルに適しています。アプリケーションのスクリーン ショットを次に示します。

![][1]

アプリケーションを単純にするため、実際のゲームは作成しません。その代わりに、プレーヤーを追加して、各プレーヤーのスコアを送信できます。スコアを送信すると、モバイル サービスが新しいランキングを計算します。バックエンドでは、モバイル サービスが 2 つのテーブルを含むデータベースを作成します。

- Player。プレーヤーの ID と名前が格納されます。
- PlayerRank。プレーヤーのスコアと順位が格納されます。

PlayerRank には、Player への外部キーがあります。各プレーヤーには、0 個または 1 個の PlayerRank があります。

実際のランキング アプリケーションでは、プレーヤーが複数のゲームのスコアを送信できるように、PlayerRank にゲーム ID が格納される場合もあります。

![][2]

クライアント アプリケーションは、Player に対してすべての CRUD 操作を実行できます。既存の PlayerRank エンティティを読み取ったり削除したりできますが、それらを直接作成したり更新したりはできません。これは、ランク値がサービスによって計算されるためです。その代わり、クライアントがスコアを送信すると、サービスによってすべてのプレーヤーの順位が更新されます。

完成したプロジェクトは、[ここ](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af)からダウンロードできます。


## プロジェクトを作成する

Visual Studio を起動し、新しい ASP.NET Web アプリケーション プロジェクトを作成します。プロジェクトに "Leaderboard" という名前を付けます。

![][3]

Visual Studio 2013 では、ASP.NET Web アプリケーション プロジェクトに Azure Mobile Services 用のテンプレートが含まれています。このテンプレートを選択し、**[OK]** をクリックします。

![][4]

このプロジェクト テンプレートには、サンプルのコントローラーとデータ オブジェクトが含まれています。

![][5]

これらはチュートリアルには必要ないので、プロジェクトから削除してかまいません。また、WebApiConfig.cs および LeaderboardContext.cs の TodoItem への参照も削除します。

## データ モデルを追加する

[EF Code First](http://msdn.microsoft.com/data/ee712907#codefirst) を使用して、データベース テーブルを定義します。DataObjects フォルダーに、`Player` という名前のクラスを追加します。

	using Microsoft.WindowsAzure.Mobile.Service;

	namespace Leaderboard.DataObjects
	{
	    public class Player : EntityData
	    {
	        public string Name { get; set; }
	    }
	}

`PlayerRank` という名前の別のクラスを追加します。

	using Microsoft.WindowsAzure.Mobile.Service;
	using System.ComponentModel.DataAnnotations.Schema;

	namespace Leaderboard.DataObjects
	{
	    public class PlayerRank : EntityData
	    {
	        public int Score { get; set; }
	        public int Rank { get; set; }

	        [ForeignKey("Id")]
	        public virtual Player Player { get; set; }
	    }
	}

これらのクラスはいずれも **EntityData** クラスから継承しています。**EntityData** から派生することで、アプリケーションは Azure Mobile Services 用のクロスプラットフォーム クライアント ライブラリを使用して、データを簡単に使用できるようになります。また、**EntityData** によって、アプリケーションで[データベースの書き込み競合を処理する](mobile-services-windows-store-dotnet-handle-database-conflicts.md)のが簡単になります。

`PlayerRank` クラスには、関連する `Player` エンティティを指す[ナビゲーション プロパティ](http://msdn.microsoft.com/data/jj713564.aspx)があります。**[ForeignKey] **属性は、`Player` プロパティが外部キーを表すことを EF に示します。

## Web API コントローラーを追加する

次に、`Player` と `PlayerRank` に対する Web API コントローラーを追加します。通常の Web API コントローラーの代わりに、特に Azure Mobile Services 用に設計された "*table controller*" という名前の特殊なコントローラーを追加します。

Controllers フォルダーを右クリックし、**[追加]**、**[新しいスキャフォールディング項目]** の順に選択します。

![][6]

**[スキャフォールディングの追加]** ダイアログ ボックスで、左側の **[共通]** を展開し、**[Azure Mobile Services]** を選択します。次に、**[Azure Mobile Services テーブル コントローラー]** を選択します。**[追加]** をクリックします。

![][7]

**[コントローラーの追加]** ダイアログ ボックスで、次の手順を実行します。

1.	**[モデル クラス]** で、Player を選択します。
2.	**[データ コンテキスト クラス]** で MobileServiceContext を選択します。
3.	コントローラーに "PlayerController" という名前を付けます。
4.	**[追加]** をクリックします。


これにより、PlayerController.cs という名前のファイルがプロジェクトに追加されます。

![][8]

コントローラーは、**TableController<T>** から派生します。このクラスは **ApiController** を継承しますが、Azure Mobile Services 用に特化されています。

- ルーティング: **TableController** の既定のルートは `/tables/{table_name}/{id}` であり、*table\_name* はエンティティ名に一致します。したがって、Player コントローラーのルートは */tables/player/{id}* となります。このルーティング規則により、**TableController** は Mobile Services の [REST API](http://msdn.microsoft.com/library/azure/jj710104.aspx) との間に一貫性を持ちます。
- データ アクセス: **TableController** クラスは、データベース操作に対して、データ アクセスの抽象化を定義する **IDomainManager** インターフェイスを使用します。スキャフォールディングでは **EntityDomainManager** を使用します。これは、EF コンテキストをラップする **IDomainManager** の具象的な実装です。

次に、PlayerRank エンティティ用の第 2 のコントローラーを追加します。同じ手順を実行しますが、モデル クラスに PlayerRank を選択します。同じデータ コンテキスト クラスを使用し、新しいクラスは作成しません。コントローラーに "PlayerRankController" という名前を付けます。

## DTO を使用して関連エンティティを返す

`PlayerRank` には `Player` エンティティが関連付けられていることを思い出してください。

    public class PlayerRank : EntityData
    {
        public int Score { get; set; }
        public int Rank { get; set; }

        [ForeignKey("Id")]
        public virtual Player Player { get; set; }
    }

Mobile Services のクライアント ライブラリではナビゲーション プロパティがサポートされず、ナビゲーション プロパティはシリアル化されません。たとえば、GET `/tables/PlayerRank` に対する未加工の HTTP 応答は次のようになります。

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 97
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 17:58:43 GMT

	[{"id":"1","rank":1,"score":150},{"id":"2","rank":3,"score":100},{"id":"3","rank":1,"score":150}]

オブジェクト グラフに `Player` が含まれていないことに注意してください。Player を含めるには、*データ転送オブジェクト* (DTO) を定義して、オブジェクト グラフを平坦化します。

DTO は、データがネットワークでどのように送信されるかを定義するオブジェクトです。DTO は、ワイヤ形式をデータベース モデルとは異なる外観にする場合に便利です。`PlayerRank` に対する DTO を作成するには、DataObjects フォルダーに `PlayerRankDto` という名前の新しいクラスを追加します。

	namespace Leaderboard.DataObjects
	{
	    public class PlayerRankDto
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }
	}

`PlayerRankController` クラスで、LINQ の **Select** メソッドを使用して、`PlayerRank` のインスタンスを `PlayerRankDto` のインスタンスに変換します。`GetAllPlayerRank` および `GetPlayerRank` の各コントローラー メソッドを次のように更新します。

	// GET tables/PlayerRank
	public IQueryable<PlayerRankDto> GetAllPlayerRank()
	{
	    return Query().Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });
	}

	// GET tables/PlayerRank/48D68C86-6EA6-4C25-AA33-223FC9A27959
	public SingleResult<PlayerRankDto> GetPlayerRank(string id)
	{
	    var result = Lookup(id).Queryable.Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });

	    return SingleResult<PlayerRankDto>.Create(result);
	}

これらの変更により、2 つの GET メソッドはクライアントに `PlayerRankDto` オブジェクトを返します。`PlayerRankDto.PlayerName` プロパティはプレーヤー名に設定されます。この変更を加えた後の応答例を次に示します。

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 160
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 19:57:08 GMT

	[{"id":"1","playerName":"Alice","score":150,"rank":1},{"id":"2","playerName":"Bob","score":100,"rank":3},{"id":"3","playerName":"Charles","score":150,"rank":1}]

JSON ペイロードにプレーヤー名が含まれています。

LINQ の Select ステートメントを使用する代わりに、AutoMapper を使用することもできます。このオプションを使用するには追加のセットアップ コードが必要ですが、それによってドメイン エンティティから DTO への自動マッピングが可能になります。詳細については、「[Mapping between Database Types and Client Types in the .NET Backend using AutoMapper (.NET バックエンドで AutoMapper を使用したデータベース型とクライアント型の間のマッピング)](http://blogs.msdn.com/b/azuremobile/archive/2014/05/19/mapping-between-database-types-and-client-type-in-the-net-backend-using-automapper.aspx)」を参照してください。

## カスタム API を定義してスコアを送信する

`PlayerRank` エンティティには、`Rank` プロパティがあります。この値はサーバーによって計算され、クライアントがそれを設定する必要はありません。クライアントはその代わりにカスタム API を使用して、プレーヤーのスコアを送信します。サーバーは新しいスコアを取得すると、プレーヤーの順位をすべて更新します。

最初に、DataObjects フォルダーに、`PlayerScore` という名前のクラスを追加します。

	namespace Leaderboard.DataObjects
	{
	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

`PlayerRankController` クラスで、変数 `MobileServiceContext` をコンストラクターからクラス変数に移動します。

    public class PlayerRankController : TableController<PlayerRank>
    {
        // Add this:
        MobileServiceContext context = new MobileServiceContext();

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Delete this:
            // MobileServiceContext context = new MobileServiceContext();
            DomainManager = new EntityDomainManager<PlayerRank>(context, Request, Services);
        }


`PlayerRankController` から次のメソッドを削除します。

- `PatchPlayerRank`
- `PostPlayerRank`
- `DeletePlayerRank`

`PlayerRankController` に次のコードを追加します。

    [Route("api/score")]
    public async Task<IHttpActionResult> PostPlayerScore(PlayerScore score)
    {
        // Does this player exist?
        var count = context.Players.Where(x => x.Id == score.PlayerId).Count();
        if (count < 1)
        {
            return BadRequest();
        }

        // Try to find the PlayerRank entity for this player. If not found, create a new one.
        PlayerRank rank = await context.PlayerRanks.FindAsync(score.PlayerId);
        if (rank == null)
        {
            rank = new PlayerRank { Id = score.PlayerId };
            rank.Score = score.Score;
            context.PlayerRanks.Add(rank);
        }
        else
        {
            rank.Score = score.Score;
        }

        await context.SaveChangesAsync();

        // Update rankings
        // See http://stackoverflow.com/a/575799
        const string updateCommand =
            "UPDATE r SET Rank = ((SELECT COUNT(*)+1 from {0}.PlayerRanks " +
            "where Score > (select score from {0}.PlayerRanks where Id = r.Id)))" +
            "FROM {0}.PlayerRanks as r";

        string command = String.Format(updateCommand, ServiceSettingsDictionary.GetSchemaName());
        await context.Database.ExecuteSqlCommandAsync(command);

        return Ok();
    }

`PostPlayerScore` メソッドには、`PlayerScore` のインスタンスが入力されます(クライアントは `PlayerScore` を HTTP POST 要求で送信します)。 このメソッドは、次の処理を実行します。

1.	プレーヤーの新しい `PlayerRank` を追加する (データベースにまだ含まれていない場合)。
2.	プレーヤーのスコアを更新する。
3.	すべてのプレーヤーの順位をバッチ更新する SQL クエリを実行する。

このメソッドのカスタム ルートは **[Route]** 属性で定義されます。

	[Route("api/score")]

このメソッドは、別のコントローラーに配置することもできます。どちらの方法が優れているということはありません。お好みのコードの整理方法を選んでください。**[Route]** の詳細については、[Attribute Routing in Web API (Web API での属性のルーティング)](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) を参照してください。

## Windows ストア アプリケーションを作成する

ここでは、モバイル サービスを使用する Windows ストア アプリケーションについて説明します。ただし、XAML や UI についてではなく、主にアプリケーション ロジックについて取り上げます。完成したプロジェクトは、[ここ](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af)からダウンロードできます。

ソリューションに新しい Windows ストア アプリケーション プロジェクトを追加します。ここでは、空のアプリケーション (Windows) テンプレートを使用しました。

![][10]

NuGet Package Manager を使用して、Mobile Services クライアント ライブラリを追加します。Visual Studio で、**[ツール]** メニューの **[NuGet パッケージ マネージャー]** をクリックします。次に、**[パッケージ マネージャー コンソール]** を選択します。[パッケージ マネージャー コンソール] ウィンドウで、次のコマンドを入力します。

	Install-Package WindowsAzure.MobileServices -Project LeaderboardApp

-Project スイッチは、パッケージをインストールするプロジェクトを指定します。

## モデル クラスを追加する

Models という名前のフォルダーを作成し、次のクラスを追加します。

	namespace LeaderboardApp.Models
	{
	    public class Player
	    {
	        public string Id { get; set; }
	        public string Name { get; set; }
	    }

	    public class PlayerRank
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }

	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

これらのクラスは、モバイル サービス内のデータ エンティティに直接対応しています。

## ビュー モデルを作成する

Model-View-View Model (MVVM) は、Model-View-Controller (MVC) の一種です。MVVM パターンを使用すると、アプリケーション ロジックをプレゼンテーションから分離できます。

- モデルは、ドメイン データ (プレーヤー、プレーヤー順位、およびプレーヤー スコア) を表します。
- ビュー モデルは、ビューの抽象表現です。
- ビューは、ビュー モデルを表示し、ユーザー入力をビュー モデルに送信します。Windows ストア アプリケーションでは、ビューは XAML で定義されます。

![][11]

`LeaderboardViewModel` という名前のクラスを追加します。

	using LeaderboardApp.Models;
	using Microsoft.WindowsAzure.MobileServices;
	using System.ComponentModel;
	using System.Net.Http;
	using System.Threading.Tasks;

	namespace LeaderboardApp.ViewModel
	{
	    class LeaderboardViewModel : INotifyPropertyChanged
	    {
	        MobileServiceClient _client;

	        public LeaderboardViewModel(MobileServiceClient client)
	        {
	            _client = client;
	        }
	    }
	}

ビュー モデルがデータ バインドに参加できるように、ビュー モデルに **INotifyPropertyChanged** を実装します。

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        MobileServiceClient _client;

        public LeaderboardViewModel(MobileServiceClient client)
        {
            _client = client;
        }

        // New code:
        // INotifyPropertyChanged implementation
        public event PropertyChangedEventHandler PropertyChanged;

        public void NotifyPropertyChanged(string propertyName)
        {
            if (PropertyChanged != null)
            {
                PropertyChanged(this,
                    new PropertyChangedEventArgs(propertyName));
            }
        }
    }

次に、監視可能なプロパティを追加します。XAML では、これらのプロパティに対してデータ バインドが実行さます。

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // View model properties
        private MobileServiceCollection<Player, Player> _Players;
        public MobileServiceCollection<Player, Player> Players
        {
            get { return _Players; }
            set
            {
                _Players = value;
                NotifyPropertyChanged("Players");
            }
        }

        private MobileServiceCollection<PlayerRank, PlayerRank> _Ranks;
        public MobileServiceCollection<PlayerRank, PlayerRank> Ranks
        {
            get { return _Ranks; }
            set
            {
                _Ranks = value;
                NotifyPropertyChanged("Ranks");
            }
        }

        private bool _IsPending;
        public bool IsPending
        {
            get { return _IsPending; }
            set
            {
                _IsPending = value;
                NotifyPropertyChanged("IsPending");
            }
        }

        private string _ErrorMessage = null;
        public string ErrorMessage
        {
            get { return _ErrorMessage; }
            set
            {
                _ErrorMessage = value;
                NotifyPropertyChanged("ErrorMessage");
            }
        }
    }

サービスで非同期操作が保留中の間は、`IsPending` プロパティが true となります。`ErrorMessage` プロパティは、サービスからのエラー メッセージを保持します。

最後に、サービス レイヤーへの呼び出しを行うメソッドを追加します。

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // Service operations
        public async Task GetAllPlayersAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                Players = await table.OrderBy(x => x.Name).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task AddPlayerAsync(Player player)
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                await table.InsertAsync(player);
                Players.Add(player);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task SubmitScoreAsync(Player player, int score)
        {
            IsPending = true;
            ErrorMessage = null;

            var playerScore = new PlayerScore()
            {
                PlayerId = player.Id,
                Score = score
            };

            try
            {
                await _client.InvokeApiAsync<PlayerScore, object>("score", playerScore);
                await GetAllRanksAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task GetAllRanksAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<PlayerRank> table = _client.GetTable<PlayerRank>();
                Ranks = await table.OrderBy(x => x.Rank).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
         }
    }

## MobileServiceClient インスタンスを追加する

*App.xaml.cs* ファイルを開き、`App` クラスに **MobileServiceClient** のインスタンスを追加します。

	// New code:
	using Microsoft.WindowsAzure.MobileServices;

	namespace LeaderboardApp
	{
	    sealed partial class App : Application
	    {
	        // New code.
	        // TODO: Replace 'port' with the actual port number.
	        const string serviceUrl = "http://localhost:port/";
	        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl);


	        // ...
	    }
	}

ローカルでデバッグを行うときには、モバイル サービスが IIS Express で実行されます。Visual Studio はランダムなポート番号を割り当てるため、URL は http://localhost:*port* (*port* はポート番号) という形式になります。ポート番号を確認するには、F5 キーを押して、Visual Studio でサービスをデバッグ用に開始します。Visual Studio でブラウザーが起動し、サービス URL に移動します。ローカル URL は、プロジェクト プロパティの **[Web]** でも確認できます。

## メイン ページを作成する

メイン ページで、ビュー モデルのインスタンスを追加します。次に、ビュー モデルをページの **DataContext** として設定します。

    public sealed partial class MainPage : Page
    {
        // New code:
        LeaderboardViewModel viewModel = new LeaderboardViewModel(App.MobileService);

        public MainPage()
        {
            this.InitializeComponent();
            // New code:
            this.DataContext = viewModel;
        }

       // ...


前に述べたとおり、ここではアプリケーションの XAML のすべては示していません。MVVM パターンの利点の 1 つは、プレゼンテーションをアプリケーション ロジックから分離することであるため、サンプル アプリケーションが気に入らない場合でも、UI を簡単に変更できます。

プレーヤーの一覧は、**ListBox** に表示されます。

	<ListBox Width="200" Height="400" x:Name="PlayerListBox"
	    ItemsSource="{Binding Players}" DisplayMemberPath="Name"/>

ランキングは **ListView** に表示されます。

	<ListView x:Name="RankingsListView" ItemsSource="{Binding Ranks}" SelectionMode="None">
	    <!-- Header and styles not shown -->
	    <ListView.ItemTemplate>
	        <DataTemplate>
	            <Grid>
	                <Grid.ColumnDefinitions>
	                    <ColumnDefinition Width="*"/>
	                    <ColumnDefinition Width="2*"/>
	                    <ColumnDefinition Width="*"/>
	                </Grid.ColumnDefinitions>
	                <TextBlock Text="{Binding Path=Rank}"/>
	                <TextBlock Text="{Binding Path=PlayerName}" Grid.Column="1"/>
	                <TextBlock Text="{Binding Path=Score}" Grid.Column="2"/>
	            </Grid>
	        </DataTemplate>
	    </ListView.ItemTemplate>
	</ListView>

データ バインドはすべてビュー モデルをとおして実行されます。

## モバイル サービスを発行する

この手順では、モバイル サービスを Microsoft Azure に発行し、ライブ サービスを使用するようにアプリケーションを変更します。

ソリューション エクスプローラーで Leaderboard プロジェクトを右クリックし、**[発行]** をクリックします。

![][12]

**[発行]** ダイアログ ボックスで、**[Azure Mobile Services]** をクリックします。

![][13]

まだ Azure アカウントにサインインしていない場合は、**[サインイン]** をクリックします。

![][14]


既存のモバイル サービスを選択するか、または **[新規]** をクリックして新しく作成します。次に、**[OK]** をクリックして発行します。

![][15]

発行プロセスで、自動的にデータベースが作成されます。接続文字列を構成する必要はありません。

これで、ランキング アプリケーションをライブ サービスに接続する準備ができました。次の 2 つが必要です。

- サービスの URL
- アプリケーション キー

これらはいずれも Azure クラシック ポータルから取得できます。ポータルで **[Mobile Services]** をクリックし、モバイル サービスをクリックします。サービス URL はダッシュボード タブに表示されています。アプリケーション キーを取得するには、**[キーの管理]** をクリックします。

![][16]

**[アクセス キーの管理]** ダイアログ ボックスで、アプリケーション キーの値をコピーします。

![][17]


サービス URL とアプリケーション キーを **MobileServiceClient** コンストラクターに渡します。

    sealed partial class App : Application
    {
        // TODO: Replace these strings with the real URL and key.
        const string serviceUrl = "https://yourapp.azure-mobile.net/";
        const string appKey = "YOUR ACCESSS KEY";

        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl, appKey);

       // ...

アプリケーションを実行すると、実際のサービスとの通信が開始されます。

## 次のステップ

* [Azure Mobile Services の詳細]
* [Web API の詳細]
* [データベースの書き込み競合の処理]
* [プッシュ通知の追加] \(ユーザーが新しいプレーヤーを追加した場合やスコアを更新した場合など)
* [認証の使用]

<!-- Anchors. -->
[Overview]: #overview
[About the sample app]: #about-the-sample-app
[Create the project]: #create-the-project
[Add data models]: #add-data-models
[Add Web API controllers]: #add-web-api-controllers
[Use a DTO to return related entities]: #use-a-dto-to-return-related-entities
[Define a custom API to submit scores]: #define-a-custom-api-to-submit-scores
[Create the Windows Store app]: #create-the-windows-store-app
[Add model classes]: #add-model-classes
[Create a view model]: #create-a-view-model
[Add a MobileServiceClient instance]: #add-a-mobileserviceclient-instance
[Create the main page]: #create-the-main-page
[Publish your mobile service]: #publish-your-mobile-service
[Next Steps]: #next-steps

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/01leaderboard.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/02leaderboard.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/03leaderboard.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/04leaderboard.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/05leaderboard.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/06leaderboard.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/07leaderboard.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/08leaderboard.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/09leaderboard.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/10leaderboard.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/11leaderboard.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/12leaderboard.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/13leaderboard.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/14leaderboard.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/15leaderboard.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/16leaderboard.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/17leaderboard.png

<!-- URLs. -->

[Azure Mobile Services の詳細]: /develop/mobile/resources/
[Web API の詳細]: http://asp.net/web-api
[データベースの書き込み競合の処理]: mobile-services-windows-store-dotnet-handle-database-conflicts.md
[プッシュ通知の追加]: ../notification-hubs-windows-store-dotnet-get-started.md
[認証の使用]: /develop/mobile/tutorials/get-started-with-users-dotnet

<!---HONumber=AcomDC_0921_2016-->