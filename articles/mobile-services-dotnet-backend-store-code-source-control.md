<properties pageTitle="ソース管理へのプロジェクト コードの保存 - Azure Mobile Services" description=".NET バックエンド プロジェクトをコンピューターのローカル Git リポジトリに格納し、リポジトリから発行する方法について説明します。" services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/5/2014" ms.author="glenga"/>

<div class="dev-center-tutorial-subselector">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/" title=".NET backend" class="current">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-store-scripts-source-control/"  title="JavaScript backend">JavaScript バックエンド</a>
</div>

# ソース管理へのプロジェクト コードの保存

このトピックでは、.NET バックエンド サービス プロジェクトの格納に Azure のモバイル サービスによって提供される、ソース管理を使用する方法を説明します。実稼働環境のモバイル サービスに、ローカルの Git リポジトリからアップロードすることで、プロジェクトをパブリッシュできます。 

このチュートリアルでは、以下の各手順について説明します。

1. [モバイル サービスでソース管理を有効にする]。
2. [Git をインストールし、ローカル リポジトリを作成する]。
3. [Git を使用して、プロジェクトを発行する]。

このチュートリアルを完了するには、[モバイル サービスの使用] または [既存のアプリにモバイル サービスを追加する] チュートリアルを完了し、モバイル サービスを作成しておく必要があります。

##<a name="enable-source-control"></a>モバイル サービスでソース管理を有効にする

[AZURE.INCLUDE [mobile-services-enable-source-control](../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Git をインストールし、ローカル リポジトリを作成する

1. ローカル コンピューターに Git をインストールします。 

	Git をインストールするために必要な手順は、オペレーティング システムによって異なります。オペレーティング システム固有の配布とインストールのガイダンスについては、「[Installing Git [Git のインストール]」を参照してください。

	> [AZURE.NOTE]
	> オペレーティング システムによっては、コマンド ラインと GUI の両方のバージョンの Git を使用できます。この記事で説明する手順では、コマンド ライン バージョンを使用します。

2. **GitBash** (Windows) や **Bash** (Unix シェル) などのコマンド ラインを開きます。OS X システムでは、**ターミナル** アプリケーションを使用してコマンド ラインにアクセスできます。

3. コマンド ラインで、スクリプトを格納するディレクトリに移動します。たとえば、 `cd SourceControl`です。

4. 次のコマンドを使用して、新しい Git リポジトリのローカル コピーを作成します。`<your_git_URL>` の部分は、モバイル サービスの Git リポジトリの URL に置き換えます。

		git clone <your_git_URL>

5. ユーザー名とパスワードの指定を求めるメッセージが表示されたら、モバイル サービスのソース管理を有効にしたときに設定したユーザー名とパスワードを入力します。認証が成功すると、次のような一連の応答が表示されます。

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. 実行する  `git clone` コマンド、およびモバイル サービスの名前で作成された新しいディレクトリの通知を参照します。.NET バックエンド モバイル サービスでは、git リポジトリははじめは空です。 

ローカル リポジトリを作成すると、このリポジトリから .NET バックエンド サービス プロジェクトを発行することができます。

##<a name="deploy-scripts"></a>Git を使用して、プロジェクトを発行する

1. Visual Studio 2013 で新しい .NET バックエンド モバイル サービス プロジェクトを作成するか既存のプロジェクトを新しいローカル リポジトリに移動します。  

	クイック テストでは、モバイル サービスのクイック スタート プロジェクトをダウンロードして、このフォルダーに保存します。

2. Packages.config ファイルを残して、すべての NuGet パッケージ フォルダーを削除します。

	モバイル サービスは、packages.confign ファイルに基づいて、NuGet パッケージを自動的に復元します。また、.gitignore ファイルを定義してパッケージのディレクトリが追加されないようにすることもできます。 
 
3. Git コマンド プロンプトで次のコマンドを入力し、新しいスクリプト ファイルの追跡を開始します。

		$ git add .
	
4. 次のコマンドを入力し、変更をコミットします。

		$ git commit -m "adding the .NET backend service project"

5. 変更をリモート リポジトリにアップロードするには、次のコマンドを入力し、資格情報を提供します。

		$ git push origin master
	
	モバイル サービスにプロジェクトが配置され、パッケージが追加され、サービスの再起動することを示す一連のコマンドが表示されます。

6. .NET バックエンドのモバイル サービスでの URL を参照すると、次が表示されます。

	![Mobile Services startup page](./media/mobile-services-dotnet-backend-store-code-source-control/mobile-service-startup.png)

モバイル サービス プロジェクトは、ソース管理で管理され、ローカル リポジトリから更新をプッシュすることで、サービスの更新を発行することができます。SQL データベースを使用して、.NET バックエンド モバイル サービスでデータ モデルの変更を行う方法の詳細については、[.NET バックエンド モバイル サービスにデータ モデルの変更を加える方法] を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、スクリプトをソース管理に保存する方法について説明しました。モバイル サービスの詳細を確認してください。 

+ [ データ モデルの変更を .NET バックエンド モバイル サービスに加える方法]
	<br/> Entity Framework Code First Migrations を使用して、既存のデータを失うことなく、既存の Azure SQL データベースにデータ モデルの変更を加える方法を説明します。 	

+ [クライアントからのカスタム API 呼び出し]
	<br/> クライアントから呼び出すことができるカスタム API の作成方法を説明します。

<!-- Anchors. -->
[モバイル サービスでソース管理を有効にする]: #enable-source-control
[Git をインストールし、ローカル リポジトリを作成する]: #clone-repo
[Git を使用して、プロジェクトを発行する]: #deploy-scripts

<!-- Images. -->

<!-- URLs. -->
[Git の web サイト]: http://git-scm.com
[ソース管理]: http://msdn.microsoft.com/ja-jp/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Git のインストール]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[既存のアプリにモバイル サービスを追加する]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[Azure 管理ポータル]: https://manage.windowsazure.com/
[ クライアントからのカスタム API 呼び出し]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api/
[ データ モデルの変更を .NET バックエンド モバイル サービスに加える方法]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations


<!--HONumber=42-->
