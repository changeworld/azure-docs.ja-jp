<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande,tdykstra"></tags>

# Azure Email Service アプリケーションの構成とデプロイ - 2/5

この 2 番目のチュートリアル (全 5 回シリーズ) では、Azure Email Service サンプル アプリケーションをビルドしてデプロイする方法について説明します。このアプリケーションとチュートリアル シリーズの詳細については、[シリーズの最初のチュートリアル][]を参照してください。

このチュートリアルでは、次のことについて説明します。

-   Azure SDK をインストールして、Azure 向け開発用にコンピューターを構成する方法
-   ローカル コンピューター上で Azure Email Service アプリケーションを構成してテストする方法
-   Azure にアプリケーションを発行する方法
-   Visual Studio サーバー エクスプローラーを使用して、Azure のテーブル、キュー、BLOB を表示および編集する方法
-   トレース機能を構成し、トレース データを表示する方法
-   ワーカー ロール インスタンスの数を増やすことによって、アプリケーションの規模を拡張する方法

### チュートリアル セグメント

-   [開発環境を設定する][]
-   [完成したソリューションをダウンロードして実行する][]
-   [Visual Studio で開発ストレージを表示する][]
-   [Azure のストレージ アカウントの作成][]
-   [クラウド サービスを作成する][]
-   [Azure ストレージを使用するためにアプリケーションを構成する][]
-   [SendGrid を使用するようにアプリケーションを構成する][]
-   [Azure にアプリケーションを展開する][]
-   [ステージング環境から運用環境にアプリケーションを昇格する][]
-   [トレース データを構成および表示する][]
-   [増加した負荷の処理用に別のワーカー ロール インスタンスを追加する][]

[WACOM.INCLUDE [install-sdk-2013-only][]]

## <a name="downloadcnfg"></a><span class="short-header">ダウンロードと実行</span>完成したソリューションをダウンロードして実行する

1.  [完成したソリューション][]をダウンロードして解凍します。

2.  Visual Studio を起動します。

3.  **[ファイル]** メニューの **[プロジェクトを開く]** を選択し、ソリューションをダウンロードした場所に移動して、ソリューション ファイルを開きます。

4.  Ctrl + Shift + B キーを押して、ソリューションをビルドします。

    既定では、*.zip* ファイルに含まれていなかった NuGet パッケージのすべての内容が Visual Studio によって自動的に復元されます。パッケージが復元されない場合は、**[ソリューションの NuGet パッケージの管理]** ダイアログの右上にある **[復元]** ボタンをクリックしてパッケージを手動でインストールします。

5.  **ソリューション エクスプローラー**で、**AzureEmailService** がスタートアップ プロジェクトとして選択されていることを確認します。

6.  Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。

    アプリケーションのホーム ページがブラウザーに表示されます。

7.  メニュー バーの **[Mailing Lists]** をクリックします。

    (これらのスクリーン ショットでは Visual Studio 2012 プロジェクト テンプレートの Web ページ スタイルが示されていますが、Visual Studio 2013 でも内容は同じです)。

    ![Run the App.][]

8.  **[Create New]** をクリックします。

9.  何らかのテスト データを入力し、**[Create]** をクリックします。

    ![Run the App.][1]

10. いくつかの追加のメーリング リスト エントリを作成します。

    ![Mailing List Index Page][]

11. **[Subscribers]** をクリックし、いくつかの登録者を追加します。**[Verified]** を `true` に設定します。

    ![Subscriber Index Page][]

12. 送信する電子メールの本文を保存した *.txt* ファイルを作成して、メッセージを追加する準備をします。その後、同じテキストといくつかの HTML (メッセージのいずれかの単語を太字や斜体にするタグなど) を保存した *.htm* ファイルを作成します。これらのファイルはこの後の手順で使用します。

13. **[Messages]** をクリックし、何らかのメッセージを追加します。前の手順で作成したファイルを選択します。[Scheduled Date] の値を変更しないでください。既定で 1 週間後の日付に設定されています。SendGrid を構成するまで、このアプリケーションでメッセージを送信することはできません。

    ![Message Create Page][]

    ![Message Index Page][]

    入力および表示しているデータは Azure ストレージ エミュレーターによって管理されています。ストレージ エミュレーターでは、SQL Server Express の LocalDB データベースを使用して、クラウド内での Azure Storage の機能をエミュレートします。このアプリケーションでストレージ エミュレーターが使用されているのは、ダウンロード時にそのストレージ エミュレーターを使用するようにプロジェクトを構成したためです。この設定は **AzureEmailService** プロジェクトの *.cscfg* ファイルに保存されています。*ServiceConfiguration.Local.cscfg* ファイルにより、Visual Studio でアプリケーションをローカルで実行するときに使用される設定が決まります。*ServiceConfiguration.Cloud.cscfg* ファイルにより、アプリケーションをクラウドに展開するときに使用される設定が決まります。この後で、Azure Storage のアカウントを使用するようにアプリケーションを構成する方法について説明します。

14. ブラウザーを閉じます。

## <a name="StorageExpVS"></a>Visual Studio で開発ストレージを表示する

**サーバー エクスプローラー**の **Azure Storage** ブラウザーを使用すると、Azure Storage リソースを簡単に直接操作できます。

1.  Visual Studio で **[表示]** メニューの **[サーバー エクスプローラー]** を選択します。

2.  **[Azure]** ノード、**[ストレージ]** ノードの順に展開し、**[Azure Storage]** ノードの下の **[(開発)]** ノードを展開します。

    Visual Studio でまだ Azure にログインしていない場合は、Azure の資格情報の入力が求められます。クラウド サービスを実行するサブスクリプションがあるアカウントの資格情報を入力します。

3.  **[テーブル]** を展開して、前の手順で作成したテーブルを表示します。

    ![Server Explorer][]

4.  **[MailingList]** テーブルをダブルクリックします。

    ![VS storage explorer][]

    このウィンドウにテーブル内のさまざまなスキーマがどのように表示されているかに注目します。`MailingList` エンティティには `Description` プロパティと `FromEmailAddress` プロパティがあります。また、`Subscriber` エンティティには `Verified` プロパティがあります (この画像では見えていませんが `SubscriberGUID` プロパティもあります)。テーブルにはすべてのプロパティに対応する列があります。指定したテーブルの行に対応するエンティティにプロパティがない場合、そのセルは空白になります。

Azure Storage リソースを操作するためのもう 1 つのツールとして、[Azure ストレージ エクスプローラー][]があります。

## <a name="createWASA"></a>Azure ストレージ アカウントの作成

Visual Studio でサンプル アプリケーションを実行するとき、Azure ストレージ エミュレーターで、またはクラウド内の Azure Storage アカウントで、テーブル、キュー、BLOB にアクセスできます。チュートリアルのこのセクションでは、Azure のストレージ アカウントを作成します。後のセクションで、Visual Studio での実行時にこのアカウントを使用するようにアプリケーションを構成します。

1.  ブラウザーで、[Azure の管理ポータル][]を開きます。

2.  [Azure の管理ポータル][]で、**[ストレージ]** をクリックし、**[新規]** をクリックします。

![New Storage][]

1.  **[簡易作成]** をクリックします。

![Quick Create][]

1.  [URL] 入力ボックスに、URL プレフィックスを入力します。

    このプレフィックスに、このボックスの下に表示されているテキストを連結したものが、ストレージ アカウントに固有の URL になります。入力したプレフィックスが他のだれかによって既に使用されている場合は、"そのストレージ名は既に使用中です" がこのボックスの上に表示されます。その場合は、別のプレフィックスを選択する必要があります。

2.  リージョンのボックスで、アプリケーションを展開する場所を設定します。

3.  **[レプリケーション]** ボックスを **[ローカル冗長]** に設定します。

    Geo レプリケーションをストレージ アカウントに対して有効にすると、1 次拠点で重大な障害が発生した場合に備えて、保存したコンテンツは 2 次拠点に複製されて、フェイルオーバーが可能になります。Geo レプリケーションには追加費用が発生する場合があります。また、テストおよび開発アカウントの場合は、一般的に Geo レプリケーションに対する課金は避けたいと考えるでしょう。詳細については、[ストレージ アカウントの管理方法に関するページ][]を参照してください。

4.  **[ストレージ アカウントの作成]** をクリックします。

    次の画像では、ストレージ アカウントは `aestest3.core.windows.net` という URL で作成されます。

    ![create storage with URL prefix][]

    この手順は完了までに数分かかる場合があります。完了を待つ間にここまでの手順を繰り返して、運用ストレージ アカウントを作成できます。多くの場合に便利なのは、ローカル開発用にテスト ストレージ アカウントを、Azure 上でのテスト用に別のテスト ストレージ アカウントを作成し、さらに運用ストレージ アカウントを作成することです。

5.  前の手順で作成したテスト アカウントをクリックし、**[アクセス キーの管理]** アイコンをクリックします。

    ![Manage Keys][]

    ![Keys GUID][]

    ストレージ アカウントを選択すると、これらのキーのいずれかで接続文字列が自動的に構成されます。接続文字列は手動で更新することもできます。

    キーが 2 つあるのは、ライブ アプリケーションのサービスを中断することなく、使用中のキーを定期的に変更できるようにするためです。使用中でないキーを再生成した後、アプリケーションの接続文字列を、再生成したキーを使用するように変更できます。キーが 1 つのみの場合では、キーの再生成時にアプリケーションでストレージ アカウントへの接続が失われることになります。画像に示しているキーは既に有効ではありません。画像のキャプチャ後に再生成されたためです。

## <a name="createcloudsvc"></a><span class="short-header">クラウド サービスの作成</span>クラウド サービスを作成する

1.  [Azure の管理ポータル][]で、**[クラウド サービス]** をクリックし、**[新規]** アイコンをクリックします。

    ![Quick Cloud][]

2.  **[簡易作成]** をクリックします。

3.  [URL] 入力ボックスに、URL プレフィックスを入力します。

    ストレージの URL と同様、この URL は一意であることが必要です。選択したプレフィックスが他のだれかによって既に使用されている場合は、エラー メッセージが表示されます。

4.  リージョンのボックスで、アプリケーションを展開する場所を設定します。

    ストレージ アカウントを作成した同じリージョンでクラウド サービスを作成する必要があります。クラウド サービスとストレージ アカウントが異なるデータ センター (異なるリージョン) にあると、遅延時間が長くなり、データ センター外の帯域幅に対して課金されることになります。データ センター内の帯域幅は無料です。

    Azure アフィニティ グループには、データ センター内のリソース間の距離を最短にする機能が備わっており、それにより遅延時間を短縮できます。このチュートリアルでは、アフィニティ グループを使用しません。詳細については、[Azure でアフィニティ グループを作成する方法に関するページ][]を参照してください。

5.  **[クラウド サービスを作成する]** をクリックします。

    次の画像では、クラウド サービスは aescloud.cloudapp.net という URL で作成されます。

    ![create storage with URL prefix][2]

## <a name="conf4azureStorage"></a><span class="short-header">ストレージ アカウントの使用</span>Azure Storage アカウントを使用するようにアプリケーションを構成する

次のステップでは、Visual Studio での実行時に開発ストレージではなく Azure のストレージ アカウントを使用するようにアプリケーションを構成する方法について説明します。

1.  **ソリューション エクスプローラー**で、**AzureEmailService** プロジェクトの **[ロール]** の下にある **MvcWebRole** を右クリックし、**[プロパティ]** をクリックします。

    ![[プロパティ] を右クリック][]

2.  **[MvcWebRole [ロール]]** ウィンドウで、**[設定]** タブをクリックします。

3.  **[サービス構成]** ボックスの一覧で **[ローカル]** を選択します。

4.  **StorageConnectionString** エントリを選択すると、行の右端に省略記号 **[...]** ボタンが表示されます。省略記号ボタンをクリックすると、**[ストレージ アカウント接続文字列]** ダイアログ ボックスが開きます。

    ![Right Click Properties][]

5.  **[ストレージ接続文字列の作成]** ダイアログで、**[サブスクリプション]** をクリックし、**サブスクリプション**とストレージの**アカウント名**を選択します。

    ![Right Click Properties][3]

6.  `StorageConnectionString` 接続文字列の場合と同じ手順を使用して、`Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 接続文字列を設定します。

7.  MvcWebRole ロールの 2 つの接続文字列に使用した同じ手順に従って、WorkerRoleA ロールと WorkerRoleB ロールの接続文字列を設定します。

8.  **AzureEmailService** プロジェクトにある **ServiceConfiguration.Local.cscfg** ファイルを開きます。

    `MvcWebRole` の `Role` 要素には、`ConfigurationSettings` 要素が含まれています。この要素は、Visual Studio UI を使用して更新した設定を含みます。

          <Role name="MvcWebRole">
            <Instances count="1" />
            <ConfigurationSettings>
              <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
              <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
            </ConfigurationSettings>
          </Role>

    2 つのワーカー ロールの `Role` 要素には、同じ 2 つの接続文字列が表示されます。

    これらのファイルは、Visual Studio **[ロール]** ウィンドウを使わずに直接編集できます。構成ファイルの詳細については、「[Azure プロジェクトの構成][]」を参照してください。

### ストレージ アカウントを使用するように構成されたアプリケーションをテストする

1.  Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。このチュートリアルの前の手順で行ったように、**[Mailing Lists]**、**[Subscribers]**、**[Messages]** のリンクをクリックして、何らかのデータを入力します。

2.  Visual Studio で、**サーバー エクスプローラー**を開きます。

3.  **[Azure]** ノードの下の **[ストレージ]** ノードを展開し、アプリケーションで使用するように構成したストレージ アカウントのノードを展開します。

4.  **[テーブル]** を展開し、`MailingList` テーブルをダブルクリックして、アプリケーションの **[Mailing List]** および **[Subscriber]** ページで入力したデータを表示します。

### Azure ストレージ エミュレーターの自動起動を無効にする (省略可能な手順)

ストレージ エミュレーターを使用しない場合は、Azure のストレージ エミュレーターの自動起動を無効にすることで、プロジェクトの起動時間を短縮し、使用するローカル リソースを節約できます。

1.  **ソリューション エクスプローラー**で **AzureEmailService** クラウド プロジェクトを右クリックし、**[プロパティ]** をクリックします。

    ![Selecting cloud project properties][]

2.  **[開発]** タブを選択します。

3.  **[Azure ストレージ エミュレーターの起動]** を **[False]** に設定します。

    ![Disabling the storage emulator automatic startup][]

    **メモ**:[False] に設定する必要があるのは、ストレージ エミュレーターを使用しない場合のみです。

    このウィンドウでは、アプリケーションをローカル実行するときに使用される **[サービス構成]** ファイルを **[ローカル]** から **[クラウド]** に (*ServiceConfiguration.Local.cscfg* から *ServiceConfiguration.Cloud.cscfg* に) 変更することもできます。

4.  Windows システム トレイで、コンピューティング エミュレーターのアイコンを右クリックし、**[ストレージ エミュレーターのシャットダウン]** をクリックします。

    ![ASE][]

## <a name="sendGrid"></a><span class="short-header">SendGrid</span>SendGrid を使用するようにアプリケーションを構成する

サンプル アプリケーションでは、SendGrid を使用して電子メールを送信します。SendGrid を使用して電子メールを送信するには、SendGrid アカウントを設定し、SendGrid 資格情報で構成ファイルを更新する必要があります。

<div class="note"><p><strong>注: </strong>SendGrid を使用したくない場合や使用できない場合は、任意の電子メール サービスに簡単に置き換えることができます。SendGrid を使用するコードはワーカー ロール B の 2 つのメソッド内に分離されています。[Tutorial 5][tut5] で、別の方法を使用して電子メールを送信するために必要な変更点について説明しています。別の方法を使用する場合は、この手順をスキップして、このチュートリアルを続行してもかまいません。電子メールの実際の送信以外、アプリケーションの他のすべての機能 (Web ページ、電子メール スケジューラなど) は動作します。</p></div>

### SendGrid アカウントを作成する

1.  「[How to Send Email Using SendGrid with Azure (SendGrid を使用した Azure での電子メールの送信方法)][]」の指示に従って、無料アカウントにサインアップします。

### SendGrid 資格情報でワーカー ロールのプロパティを更新する

このチュートリアルの前の手順で、Web ロールと 2 つのワーカー ロールのストレージ アカウントの資格情報を設定するとき、ワーカー ロール B に、Web ロールまたはワーカー ロール A にはない 3 つの設定がありました。同じ UI を使用して (**[サービス構成]** ドロップダウン リストで **[クラウド]** を選択して) これらの 3 つの設定を構成できます。

次のステップではその代替方法として、構成ファイルを編集してそれらのプロパティを設定する方法について説明します。

1.  `AzureEmailService` プロジェクトで *ServiceConfiguration.Cloud.cscfg* ファイルを編集し、前の手順で取得した SendGrid ユーザー名とパスワードの値を、これらの設定がある `WorkerRoleB` 要素に入力します。次のコードは WorkerRoleB 要素のものです。

    ![SendGridSettings][]

2.  AzureMailServiceURL 設定もあります。この値には Azure クラウド サービスの作成時に選択した URL を設定します。たとえば、""<http://aescloud.cloudapp.net>" を設定します。

クラウド構成ファイルを更新することで、アプリケーションがクラウド内で実行されるときに使用される設定を構成することになります。ローカルでの実行中に電子メールを送信するようにアプリケーションを構成する場合は、*ServiceConfiguration.Local.cscfg* ファイルも更新する必要があります。

## <a name="deployAz"></a><span class="short-header">Azure へのデプロイ</span>アプリケーションを Azure にデプロイする

アプリケーションをデプロイするには、Visual Studio でパッケージを作成し、Azure の管理ポータルからアップロードできます。または、Visual Studio から直接発行することもできます。このチュートリアルでは発行による方法を使用します。

まずアプリケーションをステージング環境に発行し、後で展開をステージングから運用に昇格します。

### IP 制限を実装する

ステージングに展開すると、そのアプリケーションには URL を知っているだれもがパブリックにアクセスできるようになります。したがって、最初の手順は IP 制限を実装して、許可されていない人がそのアプリケーションを使用できないようにすることです。運用アプリケーションでは、ASP.NET Identity のような認証と権限承認のメカニズムを実装します。ただしこれらの機能は、セットアップ、デプロイ、テストがシンプルになるように、サンプル アプリケーションからは省略されています。

1.  `MvcWebRole` プロジェクトの root フォルダーにある *Web.Release.config* ファイルを開き、**ipAddress** 属性値 127.0.0.1 を自身の IP アドレスに置き換えます (**ソリューション エクスプローラー**で **Web.Release.config** ファイルを参照するには、*Web.config* ファイルを展開する必要があります)。

    [Bing][] やその他の検索エンジンで "Find my IP" を検索することで、自身の IP アドレスを調べることができます。

    アプリケーションが発行されると、*Web.release.config* ファイルで指定した変換が適用され、クラウド内に展開されている *web.config* ファイルの IP 制限要素が更新されます。変換された *web.config* ファイルは、パッケージの作成後、*AzureEmailService\\MvcWebRole\\obj\\Release\\TransformWebConfig\\transformed* フォルダーで確認できます。

### クラウド内での実行時にストレージ アカウントを使用するようにアプリケーションを構成する

このチュートリアルの前の手順で、Web ロールと 2 つの Worker ロールのストレージ アカウントの資格情報を設定するとき、アプリケーションのローカルでの実行に使用する資格情報を設定しました。次のステップでは、アプリケーションのクラウド内での実行に使用するストレージ アカウントの資格情報を設定する必要があります。

このテストの実行には、ローカルでの実行に使用しているクラウド用の同じ資格情報を使用します。運用アプリケーションを展開する場合は通常、テストに使用するものとは別のアカウントを運用に使用することになります。また、運用に関するベスト プラクティスは、診断 connectionString にストレージ接続文字列とは別のアカウントを使用することです。ただしこのテストの実行では、同じアカウントを使用します。

同じ UI を使用して (**[サービス構成]** ドロップダウン リストで **[クラウド]** を選択して) 接続文字列を構成できます。次のステップではその代替方法として、構成ファイルを編集してそれらの接続文字列を構成する方法について説明します。

1.  **AzureEmailService** プロジェクトで *ServiceConfiguration.Local.cscfg* ファイルを開き、`StorageConnectionString` および `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.`Setting` の要素をコピーします。

2.  **AzureEmailService** プロジェクトで *ServiceConfiguration.Cloud.cscfg* ファイルを開き、コピーした要素を `MvcWebRole`、`WorkerRoleA`、および `WorkerRoleB` の `Configuration Settings` 要素に貼り付けて、既存の `Setting` 要素を置き換えます。

3.  Web ロールと 2 つの Worker ロール要素がすべて同じ接続文字列を定義していることを確認します。

### アプリケーションの発行

1.  Visual Studio をまだ開いていない場合は起動し、**AzureEmailService** ソリューションを開きます。

2.  **AzureEmailService** クラウド プロジェクトを右クリックし、**[発行]** を選択します。

    ![Package][]

    **[Azure アプリケーションの発行]** ダイアログ ボックスが表示されます。

    ![Cloud Package][]

3.  前の手順でストレージ アカウントの資格情報のインポートに自動による方法を使用した場合は、Azure サブスクリプションがドロップダウン リストにあります。そのサブスクリプションを選択し、**[次へ]** をクリックできます。それ以外の場合は、**[サインインして資格情報をダウンロードする]** をクリックし、「[Azure のストレージ用にアプリケーションを構成する][Azure ストレージを使用するためにアプリケーションを構成する]」の指示に従って、発行設定をダウンロードしてインポートします。

4.  **[共通設定]** タブの **[クラウド サービス]** ボックスで、自分のクラウド サービスが選択されていることを確認します。

5.  **[環境]** ボックスの一覧の **[運用]** を **[ステージング]** に変更します。

    ![Dashboard][]

6.  **[ビルド構成]** には既定の **[リリース]** 設定を、**[サービス構成]** には **[クラウド]** をそのまま使用します。

    **[詳細設定]** タブの既定の設定はこのチュートリアルではそのまま使用します。**[詳細設定]** タブには開発やテストに便利な設定がいくつかあります。[詳細設定] タブの詳細については、「[Windows Azure アプリケーションの発行ウィザード][]」を参照してください。

7.  **[次へ]** をクリックします。

8.  ウィザードの **[概要]** 手順で、**[保存]** アイコン ([ターゲット プロファイル] ボックスの右側に表示されているフロッピー ディスクのアイコン) をクリックして、発行設定を保存します。

    次回にアプリケーションを発行するときは、保存した設定が使用されるため、発行ウィザードの一連の手順を再度実行する必要はありません。

9.  設定を確認し、**[発行]** をクリックします。

    ![pub][]

**[Azure のアクティビティ ログ]** ウィンドウが Visual Studio で開きます。

1.  右矢印のアイコンをクリックして展開の詳細を展開します。

    ![pub][4]
    ![pub][5]

    展開は完了までに 5 分程度かかる場合があります。

2.  展開が完了したら、**Web サイトの URL** をクリックしてアプリケーションを起動します。

    ![ダッシュボード][]

3.  **[Mailing List]**、**[Subscriber]**、**[Message]** の Web ページで何らかのデータを入力して、アプリケーションをテストします。

    **メモ**:アプリケーションはテストの終了後に削除して、使用していないリソースに対して課金されないようにしてください。[Azure の無料評価版のアカウント][]を使用している場合、3 つのデプロイされたロールは数週間で月間の上限に達します。Azure 管理ポータルを使用してデプロイを削除するには、クラウド サービスを選択し、ページの下部にある **[削除]** をクリックしてから、運用またはステージングのデプロイを選択します。

    ![pub][6]

4.  Visual Studio の [Azure のアクティビティ ログ] で、**[サーバー エクスプローラーで開く]** を選択します。

    **サーバー エクスプローラー**の **[Azure コンピューティング]** でデプロイを監視できます。**Azure アプリケーションの発行**ウィザードで **[すべてのロールのリモート デスクトップを有効にする]** を選択した場合は、ロール インスタンスを右クリックし、**[リモート デスクトップを使用して接続]** を選択できます。

    ![pub][7]

## <a name="swap"></a>ステージング環境から運用環境にアプリケーションを昇格する

1.  [Azure の管理ポータル][]の左側のウィンドウで **[クラウド サービス]** アイコンをクリックし、目的のクラウド サービスを選択します。次に、**[ダッシュボード]** タブをクリックします。

2.  **[スワップ]** をクリックします。

3.  **[はい]** をクリックして VIP (仮想 IP) スワップを完了します。この手順は完了までに数分かかる場合があります。

    ![Dashboard][8]

4.  スワップを完了したら、**[運用]** デプロイメントの **[ダッシュボード]** タブで、ページの右下部分にある **[概要]** セクションまで下にスクロールします。**[サイトの URL]** で GUID プレフィックスからクラウド サービスの名前に変更されていることに注目してください。

    ![Dashboard][9]

5.  **[サイトの URL]** の下に表示されているリンクをクリックするか、コピーしてブラウザーに貼り付けて、運用環境でアプリケーションをテストします。

    ストレージ アカウントの設定を変更しなかった場合、クラウド内でアプリケーションを実行すると、アプリケーションのステージング バージョンのテスト中に入力したデータが表示されます。

## <a name="trace"></a>トレース データを構成および表示する

トレースはクラウド アプリケーションのデバッグに便利なツールです。チュートリアルのこのセクションでは、トレース データの表示方法について説明します。

1.  開発ストレージではなく Azure Storage アカウントを使用するように診断接続文字列が構成されていることを確認します。

    このチュートリアルでの前の手順に従うと、ストレージ アカウントは同一になります。Visual Studio の UI (ロールの **[プロパティ]** の **[設定]** タブ) を使用するか、*ServiceConfiguration.\*.cscfg* ファイルを参照すると、ストレージ アカウントが同一になっていることを確認できます。

**注:** ベスト プラクティスは、運用データに使用されるストレージ アカウントとは別のストレージ アカウントをデータのトレースに使用することです。ただしこのチュートリアルでは、シンプルになるように、トレースに同じアカウントを構成しています。

1.  Visual Studio で、**WorkerRoleA** プロジェクトの *WorkerRoleA.cs* を開き、`ConfigureDiagnostics` を検索して、`ConfigureDiagnostics` メソッドを調べます。

        private void ConfigureDiagnostics()
        {
            DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
            config.ConfigurationChangePollInterval = TimeSpan.FromMinutes(1d);
            config.Logs.BufferQuotaInMB = 500;
            config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
            config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

            DiagnosticMonitor.Start(
                "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
                config);
        }

    このコードで、`DiagnosticMonitor` は、トレース情報を 500 MB まで保存するように構成されています (500 MB を超えた後は、最も古いデータから上書きされます)。また、すべてのトレース メッセージを保存するように構成されています (LogLevel.Verbose)。`ScheduledTransferPeriod` は、トレース データを 1 分単位でストレージに転送するように構成されています。トレース データを保存するには、`ScheduledTransferPeriod` を設定する必要があります。

    `ConfigureDiagnostics` メソッドで、Trace API の呼び出し時にデータを記録するようにトレース リスナーを構成します。詳細については、「[Using Trace in Windows Azure Cloud Applications (Windows Azure クラウド アプリケーションでのトレースの使用)][]」を参照してください。

2.  **サーバー エクスプローラー**で、前に追加したストレージ アカウントに対応する **WADLogsTable** (**[Azure]** / **[ストレージ]** / **yourstorageaccountname** / **[テーブル]** を展開) をダブルクリックします。[WCF Data Services フィルター][]を入力して、表示されるエンティティを制限できます。次の画像では、警告およびエラー メッセージのみ表示されています。

    ![Dashboard][10]

## <a name="addRole"></a>増加した負荷の処理用に別の worker ロール インスタンスを追加する

Azure ロール内のコンピューティング リソースのスケーリングには 2 つの方法があります。[仮想マシンのサイズ][]を指定することと、実行中の仮想マシンのインスタンス数を指定することです。

仮想マシン (VM) のサイズは、*ServiceDefinition.csdef* ファイル内の `WebRole` または `WorkerRole` 要素の `vmsize` 属性で指定します。既定の設定は `Small` で、1 コアと 1.75 GB RAM の VM が提供されます。マルチスレッドのアプリケーションや、大量のメモリ、ディスク、帯域幅を使用するアプリケーションでは、パフォーマンスの向上のために VM のサイズを増やすことができます。たとえば `ExtraLarge` では、8 CPU コアと 14 GB RAM の VM が提供されます。1 つのマシン上のメモリ、CPU コア、ディスク、帯域幅を増やすことは、*スケール アップ*として知られています。スケール アップの良い候補は、[非同期メソッド][]を使用する ASP.NET Web アプリケーションなどです。VM のサイズ別に提供されるリソースの説明については、「[クラウド サービスのサイズを構成する][仮想マシンのサイズ]」を参照してください。

このアプリケーションの Worker ロール B は、電子メールの送信処理を行うため高負荷下にある限定コンポーネントです (ワーカー ロール A は、リソースを集中的に使用しないキュー メッセージの作成処理のみ行います)。ワーカー ロール B はマルチスレッドではなく、メモリ フットプリントも大きくないため、スケール アップの良い候補ではありません。ワーカー ロール B は、インスタンス数を増やすことで直線的にスケーリングできます (インスタンス数を 2 倍にすると、パフォーマンスもほぼ 2 倍になります)。コンピューティング インスタンスの数を増やすことは、*スケール アウト*として知られています。各インスタンスにはコストがあるため、スケール アウトはアプリケーションに必要なときのみ行う必要があります。

Visual Studio の UI で設定を更新するか、*ServiceConfiguration.\*.cscfg* ファイルを直接編集することで、Web ロールまたは worker ロールをスケール アウトできます。インスタンス数はロールの**プロパティ** ウィンドウの **[構成]** タブ、および </em>.cscfg</em> ファイルの `Instances` 要素で指定します。設定を更新するときは、更新した構成ファイルを展開して、変更を有効にする必要があります。または、負荷の増加が一時的な場合は、ロール インスタンスの数を手動で変更できます。さらに、指定した条件に基づいてロール インスタンスの数を自動的に変更するように Azure を構成することもできます。オートスケーリングの詳細については、[このシリーズの最終チュートリアル][]を参照してください。

チュートリアルのこのセクションでは、管理ポータルを使用してワーカー ロール B をスケール アウトする方法について説明しますが、その前に Visual Studio でスケール アウトする方法を紹介します。

Visual Studio でスケール アウトを行うには、クラウド プロジェクトの **[ロール]** の下にある目的のロールを右クリックし、**[プロパティ]** を選択します。

![Right Click Properties][[プロパティ] を右クリック]

その後、左側の **[構成]** タブを選択し、**[サービス構成]** ボックスの一覧の **[クラウド]** を選択します。

![Instance Count][]

このタブで VM のサイズも構成できることに注目してください。

次のステップでは、Azure 管理ポータルを使用してスケール アウトする方法について説明します。

1.  Azure の管理ポータルで、目的のクラウド サービスを選択し、**[スケール]** をクリックします。

2.  Worker ロール B のインスタンスの数を増やし、**[保存]** をクリックします。

    ![increase instances][]

    新しい VM がプロビジョニングされるまで数分かかる場合があります。

3.  **[インスタンス]** タブを選択して、アプリケーションの各ロール インスタンスを表示します。

    ![view instances][]

## <a name="nextsteps"></a>次のステップ

ここまで、完成したアプリケーションの構成、展開、スケーリング方法を見てきました。この後の一連のチュートリアルでは、アプリケーションを新しくビルドする方法について説明します。[次のチュートリアル][]では、Web ロールを作成します。

Azure Storage のテーブル、キュー、BLOB に関する参考情報については、[このシリーズの最終チュートリアル][11]を参照してください。

<div><a href="/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/" class="site-arrowboxcta download-cta">チュートリアル 3</a></div>

  [シリーズの最初のチュートリアル]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
  [開発環境を設定する]: #setupdevenv
  [完成したソリューションをダウンロードして実行する]: #downloadcnfg
  [Azure のストレージ アカウントの作成]: #createWASA
  [クラウド サービスを作成する]: #createcloudsvc
  [Azure ストレージを使用するためにアプリケーションを構成する]: #conf4azureStorage
  [SendGrid を使用するようにアプリケーションを構成する]: #sendGrid
  [Azure にアプリケーションを展開する]: #deployAz
  [ステージング環境から運用環境にアプリケーションを昇格する]: #swap
  [トレース データを構成および表示する]: #trace
  [増加した負荷の処理用に別のワーカー ロール インスタンスを追加する]: #addRole
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [完成したソリューション]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [Run the App.]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png
  [Mailing List Index Page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png
  [Subscriber Index Page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png
  [Message Create Page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png
  [Message Index Page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png
  [Server Explorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png
  [VS storage explorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png
  [Azure ストレージ エクスプローラー]: http://azurestorageexplorer.codeplex.com/
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [New Storage]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png
  [Quick Create]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png
  [ストレージ アカウントの管理方法に関するページ]: /en-us/manage/services/storage/how-to-manage-a-storage-account/
  [create storage with URL prefix]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png
  [Manage Keys]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png
  [Keys GUID]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG
  [Quick Cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png
  [Azure でアフィニティ グループを作成する方法に関するページ]: http://msdn.microsoft.com/en-us/library/jj156209.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png
  [[プロパティ] を右クリック]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png
  [Right Click Properties]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png
  [3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png
  [Azure プロジェクトの構成]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405486.aspx
  [Selecting cloud project properties]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png
  [Disabling the storage emulator automatic startup]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png
  [ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png
  [How to Send Email Using SendGrid with Azure (SendGrid を使用した Azure での電子メールの送信方法)]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid"
  [SendGridSettings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png
  [Bing]: http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "find my IP"
  [Package]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png
  [Cloud Package]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png
  [Dashboard]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png
  [Windows Azure アプリケーションの発行ウィザード]: http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "発行ウィザード"
  [pub]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png
  [4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png
  [5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png
  [ダッシュボード]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png
  [Azure の無料評価版のアカウント]: http://www.windowsazure.com/en-us/pricing/free-trial/ "無料評価版"
  [6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png
  [Using Trace in Windows Azure Cloud Applications (Windows Azure クラウド アプリケーションでのトレースの使用)]: http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Windows Azure でのトレースの使用"
  [WCF Data Services フィルター]: http://msdn.microsoft.com/en-us/library/windowsazure/ff683669.aspx "WCF フィルター"
  [10]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png
  [仮想マシンのサイズ]: http://msdn.microsoft.com/en-us/library/windowsazure/ee814754.aspx "VM のサイズ"
  [非同期メソッド]: http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "非同期 MVC"
  [このシリーズの最終チュートリアル]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
  [Instance Count]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png
  [increase instances]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png
  [view instances]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png
  [次のチュートリアル]: /en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [11]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
