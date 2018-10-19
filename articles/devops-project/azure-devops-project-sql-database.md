---
title: Azure DevOps プロジェクトを使用して ASP.NET アプリと Azure SQL Database をデプロイする | Azure DevOps Services チュートリアル
description: DevOps プロジェクトを利用すると、Azure を使い始めるのが簡単になります。 Azure DevOps プロジェクトを使用すると、いくつかの簡単な手順でご自身の ASP.NET と Azure SQL Database を簡単にデプロイできます。
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: d9c7c94e344daee5af87ce40ddf4dcb686696ded
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297335"
---
# <a name="tutorial--deploy-your-aspnet-app-and-azure-sql-database-with-the-azure-devops-project"></a>チュートリアル: Azure DevOps プロジェクトを使用して ASP.NET アプリと Azure SQL Database をデプロイする

Azure DevOps プロジェクトにより、既存のコードと Git リポジトリが簡単に使用できるようになり、サンプル アプリケーションのいずれかを選択して Azure への継続的インテグレーション (CI) と継続的デリバリー (CD) のパイプラインを作成することができます。  この DevOps プロジェクトによって、Azure SQL Database などの Azure リソースの作成、CI のビルド パイプラインを含む Azure DevOps 内でのリリース パイプラインの作成と構成、CD のリリース パイプラインの設定、および監視用の Azure Application Insights のリソースの作成が自動的に行われます。

このチュートリアルの内容は次のとおりです。

> [!div class="checklist"]
> * ASP.NET アプリと Azure SQL Database 用の Azure DevOps プロジェクトを作成する
> * Azure DevOps Services と Azure サブスクリプションを構成する 
> * Azure DevOps Services CI パイプラインを確認する
> * Azure DevOps Services CD パイプラインを確認する
> * Azure DevOps Services への変更をコミットし、Azure に自動的にデプロイする
> * Azure SQL Server Database に接続する 
> * リソースのクリーンアップ

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。

## <a name="create-an-azure-devops-project-for-an-aspnet-app-and-azure-sql-database"></a>ASP.NET アプリと Azure SQL Database 用の Azure DevOps プロジェクトを作成する

Azure DevOps プロジェクトによって、Azure に CI/CD パイプラインが作成されます。  **新しい Azure DevOps Services** 組織を作成するか、**既存の組織**を使用できます。  Azure DevOps プロジェクトでは、選択した **Azure サブスクリプション**に Azure SQL Database などの **Azure リソース**も作成されます。

1. [Microsoft Azure portal](https://portal.azure.com) にサインインします。

1. 左側のナビゲーション バーで **[リソースの作成]** アイコンを選択し、**DevOps プロジェクト**を検索します。  **[作成]** を選択します。

    ![継続的デリバリーの開始](_img/azure-devops-project-github/fullbrowser.png)

1. **[.NET]** を選び、**[次へ]** を選択します。

1. **[アプリケーション フレームワークを選択します]** で **[ASP.NET]** を選択します。

1. **[データベースを追加する]** を選択し、**[次へ]** を選択します。  

1. 前の手順で選択したアプリケーション フレームワークによって、ここで使用可能な Azure サービスのデプロイ ターゲットの種類が決まります。  **[次へ]** を選択します。

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Azure DevOps Services と Azure サブスクリプションを構成する

1. **新しい** Azure DevOps Services 組織を作成するか、**既存**の組織を選択します。  ご自身の Azure DevOps プロジェクトの**名前**を選択します。  

1. **Azure サブスクリプション**を選択します。

1. 必要に応じて、**[変更]** リンクを選択してその他の Azure 構成設定を表示し、**[データベース サーバーのログインの詳細]** セクションで**ユーザー名**を確認します。  このチュートリアルの後の手順で使うので、**ユーザー名**を**保存**します。
 
1. 上記の手順を実行した場合は、Azure 構成領域を終了し、**[完了]** を選択します。  それ以外の場合は、単に **[完了]** を選択します。

1. このプロセスは、完了するまで数分かかります。  完了すると、Azure DevOps **プロジェクト ダッシュボード**が Azure portal に読み込まれます。  **Azure DevOps プロジェクト ダッシュボード**には、**Azure portal** の **[すべてのリソース]** から直接移動できます。  ダッシュボードの右側にある **[参照]** を選択して、実行中のアプリケーションを表示します。
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Azure DevOps Services CI パイプラインを確認する

Azure DevOps プロジェクトによって、ご自身の Azure DevOps Services 組織に完全な Azure CI/CD パイプラインが自動的に構成されます。  パイプラインを探索し、カスタマイズできます。  Azure DevOps Services ビルド パイプラインについて理解するには、次の手順に従います。

1. **Azure DevOps プロジェクト ダッシュボード**に移動します。

1. **Azure DevOps プロジェクト ダッシュボード**の**上部**から **[ビルド パイプライン]** を選択します。  このリンクによって、ブラウザー タブが開き、ご自身の新しいプロジェクトの Azure DevOps Services ビルド パイプラインが開きます。

1. **[状態]** フィールドの横にあるビルド パイプラインの右にマウス カーソルを移動します。 表示される**省略記号**を選択します。  このアクションにより、**キューへの新しいビルドの挿入**、**ビルドの一時停止**、**ビルド パイプラインの編集**などのいくつかのアクティビティを実行できるメニューが開きます。

1. **[編集]** を選択します。

1. このビューから、ビルド パイプラインの**さまざまなタスクについて調べます**。  ビルドでは、Azure DevOps Services Git リポジトリからのソースのフェッチ、依存関係の復元、デプロイに使用した出力の公開など、さまざまなタスクが実行されます。

1. ビルド パイプラインの上部で、**ビルド パイプラインの名前**を選択します。

1. ご自身のビルド パイプラインの**名前**をわかりやすい名前に変更します。  **[保存してキューに登録]** を選択し、**[保存]** を選択します。

1. ご自身のビルド パイプラインの名前の下で、**[履歴]** を選択します。  ビルドの最近の変更の監査証跡が表示されます。  Azure DevOps Services は、ビルド パイプラインに対する変更を追跡し、バージョンを比較できるようにします。

1. **[トリガー]** を選択します。  Azure DevOps プロジェクトでは、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。  必要に応じて、CI プロセスのブランチを含めるか除外するかを選択できます。

1. **[保持]** を選択します。  シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Azure DevOps Services CD パイプラインを確認する

Azure DevOps プロジェクトでは、Azure DevOps Services 組織から Azure サブスクリプションにデプロイするために必要な手順が自動的に作成され、構成されます。  これらの手順には、ご自身の Azure サブスクリプションで Azure DevOps Services を認証するための Azure サービス接続の構成が含まれます。  自動化により Azure DevOps Services リリース定義も作成され、このリリースでは Azure に CD が提供されます。  Azure DevOps Services リリース定義を詳しく調べるには、次の手順に従います。

1. **[ビルドとリリース]** を選択し、**[リリース]** を選択します。  Azure DevOps プロジェクトにより、Azure へのデプロイを管理する Azure DevOps Services リリース パイプラインが作成されました。

1. ブラウザーの左側で、リリース パイプラインの横にある**省略記号**を選択し、**[編集]** を選択します。

1. リリース パイプラインには、リリース プロセスを定義する**パイプライン**が含まれています。  **[成果物]** で、**[ドロップ]** を選択します。  前の手順で調べたビルド パイプラインでは、成果物に使用される出力が生成されます。 

1. **[ドロップ]** アイコンの右側にある **[継続的配置トリガー]** **アイコン** (稲妻として表示されます) を選択します。このリリース パイプラインには、有効になっている CD トリガーがあります。  トリガーは、新しいビルド成果物が使用可能になるたびにデプロイを開始します。  必要に応じて、トリガーを無効にできるので、デプロイでは手動での実行が必要です。 

1. Azure DevOps プロジェクトでは、ランダムな SQL パスワードが設定され、リリース パイプラインにこのパスワードが使用されます。  ブラウザーの左側の **[変数]** を選択します。 

1. **この手順は、SQL Server のパスワードを変更した場合にのみ実行します。**  **[パスワード]** 変数は 1 つだけあります。  **[値]** テキスト ボックスの右側にある**南京錠**アイコンを選択します。  新しいパスワードを**入力**し、**[保存]** を選択します。

1. ブラウザーの左側で、**[タスク]** を選択し、**環境**を選択します。  

1. タスクはデプロイ プロセスによって実行されるアクティビティであり、**フェーズ**にグループ化されます。  このリリース パイプラインにはフェーズが 1 つだけあります。  フェーズには、**[Azure App Service 配置]** タスクと **[Azure SQL Database の配置]** タスクが含まれています。

1. **[Azure SQL を実行します]** タスクを選択し、SQL のデプロイに使用されるさまざまなプロパティを確認します。  **[配置パッケージ]** で、タスクが **SQL DACPAC ファイル**を使用していることに注意してください。

1. ブラウザーの右側で、**[リリースの表示]** を選択します。  このビューには、リリースの履歴が表示されます。

1. リリースの 1 つの横にある**省略記号**を選択し、**[開く]** を選択します。  **リリース概要**、**関連付けられた作業項目**、**テスト**など、このビューから表示されるいくつかのメニューがあります。

1. **[コミット]** を選択します。  このビューには、特定のデプロイに関連付けられているコードのコミットが表示されます。 リリースを比較して、デプロイ間のコミットの相違を表示できます。

1. **[ログ]** を選択します。  ログには、デプロイ プロセスに関する有用な情報が含まれます。  これらは、デプロイ中とデプロイ後の両方に表示できます。

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Azure DevOps Services への変更をコミットし、Azure に自動的にデプロイする 

 > [!NOTE]
 > 次の手順では、テキストを少し変更して CI/CD パイプラインをテストします。  必要に応じて、テーブルの SQL Server スキーマを変更し、SQL のデプロイ プロセスをテストできます。

Web サイトに最新の作業を自動的にデプロイする CI/CD プロセスを使用して、アプリに対してチームで共同作業を行う準備ができました。  Azure DevOps Services Git リポジトリに対する各変更により、Azure DevOps Services でビルドが開始され、Azure DevOps Services CD パイプラインによって Azure へのデプロイが実行されます。  以下の手順に従うか、他の手法を使用して、リポジトリに変更をコミットします。  コードを変更すると CI/CD プロセスが開始され、新しい変更が Azure に自動的にデプロイされます。

1. [Azure DevOps Services] メニューから **[コード]** を選択し、リポジトリに移動します。

1. **SampleWebApplication\Views\Home** ディレクトリに移動し、**Index.cshtml** ファイルの横にある**省略記号**を選択し、**[編集]** を選択します。

1. **div タグ**の 1 つの内側にあるテキストなど、ファイルに変更を加えます。  右上で **[コミット]** を選択します。  もう一度 **[コミット]** を選択して変更をプッシュします。 

1. 数分後に **Azure DevOps Services でビルドが開始**され、リリースが実行されて変更がデプロイされます。  DevOps プロジェクト ダッシュボードを使用するか、ご自身の Azure DevOps Services 組織を使用してブラウザーで、**ビルドの状態**を監視することができます。

1. リリースが完了したら、ブラウザーで**アプリケーションを最新の情報に更新**し、変更内容を確認します。

## <a name="connect-to-the-azure-sql-server-database"></a>Azure SQL Server Database に接続する

Azure SQL Database に接続するには、適切なアクセスを許可が必要です。

1. Azure DevOps プロジェクト ダッシュボードで、**[SQL Database]** を選択して、SQL DB の管理ページに移動します。
   
1. **[サーバー ファイアウォールの設定]** を選択し、**[+ クライアント IP の追加]** を選択します。  

1. **[保存]** を選択します。  クライアントの IP アドレスが、**SQL Server Azure リソース**へのアクセスを許可されます。

1. **[SQL Database]** ブレードに戻ります。 

1. 画面の右側で、**サーバー名**を選択して **SQL Server** の構成ページに移動します。

1. **[パスワードのリセット]** を選択し、**SQL Server 管理者ログイン**のパスワードを入力して、**[保存]** を選択します。  このチュートリアルの後の手順で使うので、このパスワードを **[保持]** します。

1. 必要に応じて、**SQL Server Management Studio** や **Visual Studio** などのクライアント ツールを使用して、Azure SQL Server とデータベースに接続できます。  **[サーバー名]** プロパティを使用して接続します。

   DevOps プロジェクトの初期構成の間に DB ユーザー名を変更しなかった場合、ユーザー名はメール アドレスのローカル部分です。  たとえば、メール アドレスが johndoe@microsoft.com の場合、ユーザー名は johndoe です。

 > [!NOTE]
 > SQL ログインのパスワードを変更する場合は、「**Azure DevOps Services CD パイプラインを確認する**」セクションで説明されているように、Azure DevOps Services リリース パイプライン変数でパスワードを変更する必要があります

## <a name="clean-up-resources"></a>リソースのクリーンアップ

 > [!NOTE]
 > 次の手順では、リソースが完全に削除されます。  この機能は、画面の指示を注意深く読んだ後でのみ使用してください。

テスト中は、課金を回避するためにリソースをクリーンアップできます。  このチュートリアルで作成した Azure SQL Database および関連リソースは、不要になったら、Azure DevOps プロジェクト ダッシュボードの **[削除]** 機能を使用して削除できます。  削除機能では、Azure と Azure DevOps Services の両方で Azure DevOps プロジェクトにより作成されたデータが破棄され、破棄されたデータは取得できなくなるので、**注意してください**。

1. **Azure portal** から、**Azure DevOps プロジェクト**に移動します。
2. ダッシュボードの**右上**で、**[削除]** を選択します。  画面の指示を読んだ後、**[はい]** を選択するとリソースが**完全に削除**されます。

## <a name="next-steps"></a>次の手順

必要に応じて、チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。 この CI/CD パターンを他のプロジェクトのテンプレートとして使用することもできます。  以下の方法について学習しました。

> [!div class="checklist"]
> * ASP.NET アプリと Azure SQL Database 用の Azure DevOps プロジェクトを作成する
> * Azure DevOps Services と Azure サブスクリプションを構成する 
> * Azure DevOps Services CI パイプラインを確認する
> * Azure DevOps Services CD パイプラインを確認する
> * Azure DevOps Services への変更をコミットし、Azure に自動的にデプロイする
> * Azure SQL Server Database に接続する 
> * リソースのクリーンアップ

Azure パイプラインについて詳しくは、このチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [CD プロセスをカスタマイズする](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]