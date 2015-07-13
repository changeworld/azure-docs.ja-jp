<properties
	pageTitle="Azure AD で AngularJS シングル ページ アプリケーションを構築する方法"
	description="AngularJS ベースのシングル ページ アプリケーションのセキュリティ保護のために、ASP.NET Web API のバックエンドで実装された Active Directory Authentication Library (ADAL) for JavaScript を使用する方法を示します。これは、CORS を使用する別の ASP.NET Web API を呼び出します。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/11/2015"
	ms.author="justinha"/>


# Azure AD で AngularJS シングル ページ アプリケーションを構築する方法

このチュートリアルでは、AngularJS ベースのシングル ページ アプリケーションのセキュリティ保護のために、ASP.NET Web API のバックエンドで実装された Active Directory Authentication Library (ADAL) for JavaScript を使用する方法を示します。これは、CORS を使用する別の ASP.NET Web API を呼び出します。このチュートリアルで使用するコード サンプルを参照するには、GitHub で「[AzureADSamples/SinglePageApp-AngularJS-DotNet](https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet)」をご覧ください。

ADAL for Javascript はオープン ソース ライブラリです。配布オプション、ソース コード、貢献については、[ADAL JS のリポジトリ](https://github.com/AzureAD/azure-activedirectory-library-for-js)に関するページをご確認ください。

このシナリオおよびその他のシナリオでプロトコルが機能するしくみの詳細については、「[Azure AD の認証シナリオ](http://go.microsoft.com/fwlink/?LinkId=394414)」をご覧ください。

## このサンプルを実行する方法

開始するのは簡単です。 このサンプルを実行するには、次のものが必要です。

- Visual Studio 2013
- インターネット接続
- Azure サブスクリプション ([無料評価版](https://account.windowsazure.com/organization)で十分です)

それぞれの Azure サブスクリプションには、関連付けられた Azure Active Directory (Azure AD) テナントがあります。このサンプルで使用する Azure AD のすべての機能は無料で使用できます。

## このリポジトリの複製またはダウンロード

シェルまたはコマンド ラインから、`git clone https://github.com/AzureADSamples/SinglePageApp-WebAPI-AngularJS-DotNet.git` を実行します。

## Azure Active Directory テナントへの To Go API サービスの登録

1. [Azure 管理ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側のナビゲーションで **[Active Directory]** をクリックします。
3. サンプル アプリケーションを登録するディレクトリをクリックします。
4. **[アプリケーション]** タブをクリックします。
5. ドロアーで **[追加]** をクリックします。
6. **[組織で開発中のアプリケーションを追加]** をクリックします。
7. "To Go API" など、アプリケーションのフレンドリ名を入力し、**[Web アプリケーション]** または **[Web API]** を選択して、**[次へ]** をクリックします。
8. サインオン URL には、サンプルのベース URL を入力します。既定では `https://localhost:44327/` です。
9. アプリケーション ID URI には、「`https://<your_directory_name>/ToGoAPI`」と入力します。`<your_directory_name>` は、Azure AD ディレクトリの名前に置き換えます。構成を保存します。

すべて完了しました。 次の手順に進む前に、アプリケーションのアプリケーション ID URI を見つける必要があります。

1. 引き続き Azure ポータルで、アプリケーションの **[構成]** タブをクリックします。
2. アプリケーション ID URI の値を見つけ、クリップボードにコピーします。

## Azure Active Directory テナントを使用するための To Go API の構成

1. Visual Studio 2013 でソリューションを開きます。
2. ToGoAPI プロジェクトで、`web.config` ファイルを開きます。
3. アプリケーション キー `ida:Tenant` を見つけ、値を Azure AD テナント名に置き換えます。
4. アプリケーション キー `ida:Audience` を見つけ、値を Azure ポータルからコピーしたアプリケーション ID URI に置き換えます。
5. ToGoAPI プロジェクトで、`Controllers/ToGoListController.cs` ファイルを開きます。`[EnableCors...]` 属性で、To Do SPA クライアントの場所を入力します。既定では、`https://localhost:44326` です。末尾のスラッシュは省略してください。
5. TodoSPA プロジェクトで、`App/Scripts/App.js` ファイルを開き、`endpoints` オブジェクトの宣言を見つけます。
6. リソース識別子に対する To Go API エンドポイントの場所、またはアプリケーション ID URI のマッピングを入力します。`endpoints` オブジェクトのプロパティの名前は、To Go API の場所になります。既定では、`https://localhost:44327/` です。このプロパティの値は、ポータルからコピーしたアプリケーション ID URI (例: `https://<your_tenant_name>/ToGoAPI`) になります。
8. このファイルの他の構成の値についてはまだ気にする必要はありません。またすぐに戻ってきます。
9. TodoSPA プロジェクトで、`App/Scripts/toGoListSvc.js` ファイルを開きます。`apiEndpoint` 変数の値を To Go API の場所に置き換えます。既定では、`https://localhost:44327/` です。

## Azure Active Directory テナントへの To Do シングル ページ アプリケーションの登録

1. [Azure 管理ポータル](https://manage.windowsazure.com)にもう一度サインインします。
2. 左側のナビゲーションで **[Active Directory]** をクリックします。
3. サンプル アプリケーションを登録するテナントをクリックします。
4. **[アプリケーション]** タブをクリックします。
5. ドロアーで **[追加]** をクリックします。
6. **[組織で開発中のアプリケーションを追加]** をクリックします。
7. "To Do SPA" など、アプリケーションのフレンドリ名を入力し、**[Web アプリケーション] または [Web API]** を選択して、**[次へ]** をクリックします。
8. サインオン URL には、サンプルのベース URL を入力します。既定では `https://localhost:44326/` です。
9. アプリケーション ID URI には、「`https://<your_directory_name>/ToDoSPA`」と入力します。`<your_directory_name>` は、Azure AD ディレクトリの名前に置き換えます。
10. **[その他のアプリケーションに対するアクセス許可]** セクションで、**[アプリケーションの追加]** をクリックします。**[表示]** ボックスの一覧の **[その他]** を選択し、上のチェック マークをクリックします。To Go API を見つけてクリックし、下のチェック マークをクリックしてアプリケーションを追加します。**[委任されたアクセス許可]** ボックスの一覧の **[To Go API へのアクセス]** を選択し、構成を保存します。

すべて完了しました。 次の手順に進む前に、アプリケーションのクライアント ID を見つける必要があります。

1. 引き続き Azure ポータルで、アプリケーションの **[構成]** タブをクリックします。
2. クライアント ID の値を見つけ、クリップボードにコピーします。


## アプリケーションでの OAuth2 の暗黙的権限付与を有効にする

既定では、Azure AD でプロビジョニングされたアプリケーションで OAuth2 の暗黙的権限付与は使用できません。このサンプルを実行するには、明示的にオプトインする必要があります。

1. 前の手順の後、ブラウザーでは Azure 管理ポータルが引き続き開いた状態になっています。具体的には、アプリケーションのエントリの **[構成]** タブが表示されています。
2. ドロアーの **[マニフェストの管理]** ボタンを使用して、アプリケーションのマニフェスト ファイルをダウンロードし、ディスクに保存します。
3. マニフェスト ファイルをテキスト エディターで開きます。`oauth2AllowImplicitFlow` プロパティを検索します。このプロパティが `false` に設定されていることを確認したら、`true` に変更してファイルを保存します。
4. **[マニフェストの管理]** ボタンを使用して、更新したマニフェスト ファイルをアップロードします。アプリケーションの構成を保存します。

## Azure Active Directory テナントを使用するための To Do SPA の構成

1. Visual Studio 2013 でソリューションを開きます。
2. TodoSPA プロジェクトで、`web.config` ファイルを開きます。
3. アプリケーション キー `ida:Tenant` を見つけ、値を Azure AD ディレクトリ名に置き換えます。
4. アプリケーション キー `ida:Audience` を見つけ、値を Azure ポータルからのクライアント ID に置き換えます。
5. TodoSPA プロジェクトで、`App/Scripts/App.js` ファイルをもう一度開き、`adalAuthenticationServiceProvider.init(` という行を見つけます。
6. `tenant` の値を Azure AD ディレクトリ名に置き換えます。
7. `clientId` の値を Azure ポータルからのクライアント ID に置き換えます。

## サンプルの実行

ソリューションのクリーン、ソリューションの再構築、ソリューションの実行

右上隅のサインイン リンクをクリックするか、**[To Do リスト]** タブまたは **[To Go リスト]** タブを直接クリックすることにより、サインイン操作をトリガーできます。サインインし、項目を To Do リストに追加し、ユーザー アカウントを削除し、もう一度開始することで、サンプルを調べます。場所を To Go リストに追加し、CORS を使用する To Go API に対して CRUD 操作を実行します。

## このサンプルを Azure にデプロイする方法

To Do SPA と To Go API を Azure の Web サイトにデプロイするには、2 つの Web サイトを作成し、それぞれのプロジェクトを Web サイトに発行し、両方のプロジェクトを更新して、IIS Express ではなく新しい場所を参照するようにします。

### To Go API の Azure の Web サイトの作成

1. [Azure 管理ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側のナビゲーションで **[Web サイト]** をクリックします。
3. 左下隅の **[新規]** をクリックし、**[コンピューティング]**、**[Web サイト]**、**[カスタム作成]** の順に選択します。ホスティング プランとリージョンを選択し、Web サイトに togo-contoso.azurewebsites.net などの名前を付けます。使用するデータベースを選択するか、新しいデータベースを作成します。**[Web サイトの作成]** をクリックします。
4. Web サイトが作成されたら、クリックして管理します。この一連の手順について、.publishsettings ファイルをダウンロードして保存します。ソース管理からなどの別のデプロイ メカニズムも使用できます。.publishsettings ファイルの使用方法の詳細については、「[方法: サブスクリプションへの接続](../install-configure-powershell.md#Connect)」をご覧ください。

### To Do SPA の Azure の Web サイトの作成

1. [Azure 管理ポータル](https://manage.windowsazure.com)に移動します。
2. 左側のナビゲーションで **[Web サイト]** をクリックします。
3. 左下隅の **[新規]** をクリックし、**[コンピューティング]**、**[Web サイト]**、**[カスタム作成]** の順に選択します。ホスティング プランとリージョンを選択し、Web サイトに togo-contoso.azurewebsites.net などの名前を付けます。使用するデータベースを選択します。To Go API の場合と同じデータベースでかまいません。**[Web サイトの作成]** をクリックします。
4. Web サイトが作成されたら、クリックして管理します。ここでも、この一連の手順の .publishsettings ファイルをダウンロードして保存します。

### Azure の Web サイトを使用するための両方のプロジェクトの更新

1. Visual Studio で、TodoSPA プロジェクトに移動します。
2. 2 つの変更が必要です。`App\Scripts\app.js` で、`endpoints` オブジェクトのプロパティ名を To Go API の新しい場所 (例: `https://togo-contoso.azurewebsites.net/`) に置き換えます。`App\Scripts\toGoListSvc.js` で、`apiEndpoint` 変数を同じ値に置き換えます。
3. ToGoAPI プロジェクトでは、必要な変更は 1 つだけです。`Controllers\ToGoListController.cs` で、`[EnableCors...]` 属性を更新して、To Do SPA の新しい場所 (例: `https://todo-contoso.azurewebsites.net`) が反映されるようにします。ここでも、末尾のスラッシュを省略してください。

### Azure の Web サイトに対する To Go API の発行

1. Visual Studio に切り替え、ToGoAPI プロジェクトに移動します。ソリューション エクスプローラーでプロジェクトを右クリックし、**[発行]** を選択します。**[インポート]** をクリックし、ダウンロードした To Go API 発行プロファイルをインポートします。
6. **[接続]** タブで、ターゲット URL を https (例: https://togo-constoso.azurewebsites.net) に更新します。**[次へ]** をクリックします。
7. **[設定]** タブで、**[組織認証を有効にする]** が選択されていないことを確認します。**[発行]** をクリックします。
8. Visual Studio では、プロジェクトが発行され、自動的にブラウザーが開いてプロジェクトの URL にアクセスします。プロジェクトの既定の Web ページが表示されたら、発行は成功しました。

### Azure の Web サイトに対する To Do SPA の発行

1. Visual Studio に切り替え、TodoSPA プロジェクトに移動します。ソリューション エクスプローラーでプロジェクトを右クリックし、**[発行]** を選択します。**[インポート]** をクリックし、ダウンロードした To Do SPA .publishsettings ファイルをインポートします。
6. **[接続]** タブで、ターゲット URL を https (例: https://todo-contoso.azurewebsites.net) に更新します。**[次へ]** をクリックします。
7. **[設定]** タブで、**[組織認証を有効にする]** が選択されていないことを確認します。**[発行]** をクリックします。
8. Visual Studio では、プロジェクトが発行され、自動的にブラウザーが開いてプロジェクトの URL にアクセスします。プロジェクトの既定の Web ページが表示されたら、発行は成功しました。

### ディレクトリ テナントの To Do SPA の構成の更新

1. [Azure 管理ポータル](https://manage.windowsazure.com)に移動します。
2. 左側のナビゲーションで **[Active Directory]** をクリックし、テナントを選択します。
3. **[アプリケーション]** タブで、**To Do SPA** アプリケーションを選択します。
4. **[構成]** タブで、**[サインオン URL]** フィールドと **[応答 URL]** フィールドを SPA のアドレス (例: https://todo-contoso.azurewebsites.net) に更新します。構成を保存します。

## コードについて

認証ロジックを含む主要なファイルは次のとおりです。

- **App.js** - ADAL モジュールの依存関係を挿入し、Azure AD とのプロトコルのやり取りを促進するために ADAL で使用されるアプリケーション構成値を提供します。また、事前の認証なしではアクセスが許可されないルートを示します。

- **index.html** - adal.js への参照を含みます。

- **HomeController.js** - ADAL で login() メソッドと logOut() メソッドを使用する方法を示します。

- **UserDataController.js** - キャッシュされた id_token からユーザー情報を取得する方法を示します。

このチュートリアルの作成にあたり、@matvelloso のご協力に感謝します。


## 次のステップ

Azure AD を使用して認証と承認を Web アプリケーションや Web API に追加する際に役立つその他のリソースは次のとおりです。

+ [Azure Active Directory のコード例](https://msdn.microsoft.com/library/azure/dn646737.aspx)
+ [Azure AD の認証シナリオ](https://msdn.microsoft.com/library/azure/dn499820.aspx)
 

<!---HONumber=62-->