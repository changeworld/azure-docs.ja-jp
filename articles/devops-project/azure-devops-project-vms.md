---
title: Azure DevOps プロジェクトを使用して ASP.NET アプリを Azure Virtual Machines にデプロイする | Azure DevOps Services チュートリアル
description: DevOps プロジェクトを利用すると、Azure を使い始めるのが簡単になります。 Azure DevOps プロジェクトを使用すると、いくつかの簡単な手順で ASP.NET アプリを Azure Virtual Machines に簡単にデプロイできます。
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b05e2c2c46aa9bfa8c92d3d3c5c83d018c547b9f
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299136"
---
# <a name="tutorial--deploy-your-aspnet-app-to-azure-virtual-machines-with-the-azure-devops-project"></a>チュートリアル: Azure DevOps プロジェクトを使用して ASP.NET アプリを Azure Virtual Machines にデプロイする

Azure DevOps プロジェクトにより、既存のコードと Git リポジトリが簡単に使用できるようになり、サンプル アプリケーションのいずれかを選択して Azure への継続的インテグレーション (CI) と継続的デリバリー (CD) のパイプラインを作成することができます。  この DevOps プロジェクトによって、Azure 仮想マシンなどの新しい Azure リソースの作成、CI のビルド パイプラインを含む Azure DevOps 内でのリリース パイプラインの作成と構成、CD のリリース パイプラインの設定、および監視用の Azure Application Insights のリソースの作成が自動的に行われます。

このチュートリアルの内容は次のとおりです。

> [!div class="checklist"]
> * ASP.NET アプリ用の Azure DevOps プロジェクトを作成する
> * Azure DevOps Services と Azure サブスクリプションを構成する 
> * Azure DevOps Services CI パイプラインを確認する
> * Azure DevOps Services CD パイプラインを確認する
> * Azure DevOps Services への変更をコミットし、Azure に自動的にデプロイする
> * Azure Application Insights の監視を構成する
> * リソースのクリーンアップ

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。

## <a name="create-an-azure-devops-project-for-an-aspnet-app"></a>ASP.NET アプリ用の Azure DevOps プロジェクトを作成する

Azure DevOps プロジェクトによって、Azure に CI/CD パイプラインが作成されます。  **新しい Azure DevOps Services** 組織を作成するか、**既存の組織**を使用できます。  Azure DevOps プロジェクトでは、選択した **Azure サブスクリプション**に仮想マシンなどの **Azure リソース**も作成されます。

1. [Microsoft Azure portal](https://portal.azure.com) にサインインします。

1. 左側のナビゲーション バーで **[+新規]** アイコンを選択し、**DevOps プロジェクト**を検索します。  **[作成]** を選択します。

    ![継続的デリバリーの開始](_img/azure-devops-project-github/fullbrowser.png)

1. **[.NET]** を選び、**[次へ]** を選択します。

1. **[Choose an application Framework]\(アプリケーション フレームワークの選択\)** で **[ASP.NET]** を選択し、**[次へ]** を選択します。 

1. 前の手順で選択したアプリケーション フレームワークによって、ここで使用可能な Azure サービスのデプロイ ターゲットの種類が決まります。  **[仮想マシン]** を選び、**[次へ]** を選択します。

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Azure DevOps Services と Azure サブスクリプションを構成する

1. **新しい** Azure DevOps Services 組織を作成するか、**既存**の組織を選択します。  ご自身の Azure DevOps プロジェクトの**名前**を選択します。  

1. ご自身の **Azure サブスクリプション サービス**を選択します。  必要に応じて、**[変更]** リンクを選択し、Azure リソースの場所の変更などの他の構成詳細を入力できます。
 
1. 新しい Azure 仮想マシン リソースの **[仮想マシン名]**、**[ユーザー名]**、**[パスワード]** を入力し、**[完了]** を選択します。

1. Azure 仮想マシンの準備が完了するまで数分かかります。  サンプル ASP.NET アプリケーションがご自身の Azure DevOps Services 組織内のリポジトリに設定され、ビルドとリリースが実行され、ご自身のアプリケーションが新規に作成された Azure VM にデプロイされます。  

    完了すると、Azure DevOps **プロジェクト ダッシュボード**が Azure portal に読み込まれます。  **Azure DevOps プロジェクト ダッシュボード**には、**Azure portal** の **[すべてのリソース]** から直接移動できます。  

    このダッシュボードでは、Azure DevOps Services **コード リポジトリ**、**Azure CI/CD パイプライン**、およびご自身の実行中の **Azure アプリケーション**が可視化されます。    

    ![ダッシュボード ビュー](_img/azure-devops-project-vms/dashboardnopreview.png)

1. Azure DevOps プロジェクトでは、ご自身のリポジトリに対するコード変更を自動的にデプロイする CI ビルドおよびリリース トリガーが自動的に構成されます。  さらに、Azure DevOps で追加のオプションを構成できます。  ダッシュボードの右側にある **[参照]** を選択して、実行中のアプリケーションを表示します。
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Azure DevOps Services CI パイプラインを確認する
 
Azure DevOps プロジェクトによって、ご自身の Azure DevOps Services 組織に完全な Azure CI/CD パイプラインが自動的に構成されます。  パイプラインを探索し、カスタマイズできます。  Azure DevOps Services ビルド パイプラインについて理解するには、次の手順に従います。

1. **Azure DevOps プロジェクト ダッシュボード**の**上部**から **[ビルド パイプライン]** を選択します。  このリンクによって、ブラウザー タブが開き、ご自身の新しいプロジェクトの Azure DevOps Services ビルド パイプラインが開きます。

1. **[状態]** フィールドの横にあるビルド パイプラインの右にマウス カーソルを移動します。 表示される**省略記号**を選択します。  このアクションにより、**キューへの新しいビルドの挿入**、**ビルドの一時停止**、**ビルド パイプラインの編集**などのいくつかのアクティビティを実行できるメニューが開きます。

1. **[編集]** を選択します。

1. このビューから、ビルド パイプラインの**さまざまなタスクについて調べます**。  ビルドでは、Azure DevOps Services Git リポジトリからのソースのフェッチ、依存関係の復元、デプロイに使用した出力の公開など、さまざまなタスクが実行されます。

1. ビルド パイプラインの上部で、**ビルド パイプラインの名前**を選択します。

1. ご自身のビルド パイプラインの**名前**をわかりやすい名前に変更します。  **[保存してキューに登録]** を選択し、**[保存]** を選択します。

1. ご自身のビルド パイプラインの名前の下で、**[履歴]** を選択します。  ビルドの最近の変更の監査証跡が表示されます。  Azure DevOps Services は、ビルド パイプラインに対する変更を追跡し、バージョンを比較できるようにします。

1. **[トリガー]** を選択します。  Azure DevOps プロジェクトでは、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。  必要に応じて、CI プロセスのブランチを含めるか除外するかを選択します。

1. **[Retention]\(リテンション期間\)** を選択します。  シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Azure DevOps Services CD パイプラインを確認する

Azure DevOps プロジェクトでは、Azure DevOps Services 組織から Azure サブスクリプションにデプロイするために必要な手順が自動的に作成され、構成されます。  これらの手順には、ご自身の Azure サブスクリプションで Azure DevOps Services を認証するための Azure サービス接続の構成が含まれます。  自動化により Azure DevOps Services CD パイプラインも作成され、これにより Azure 仮想マシンに CD が提供されます。  Azure DevOps Services CD パイプラインを詳しく調べるには、次の手順に従います。

1. **[ビルドとリリース]** を選択し、**[リリース]** を選択します。  Azure DevOps プロジェクトにより、Azure へのデプロイを管理する Azure DevOps Services リリース パイプラインが作成されました。

1. ブラウザーの左側で、リリース パイプラインの横にある**省略記号**を選択し、**[編集]** を選択します。

1. リリース パイプラインには、リリース プロセスを定義する**パイプライン**が含まれています。  **[成果物]** で、**[ドロップ]** を選択します。  前の手順で調べたビルド パイプラインでは、成果物に使用される出力が生成されます。 

1. **[ドロップ]** アイコンの右側にある **[継続的配置トリガー]** **アイコン** (稲妻として表示されます) を選択します。このリリース パイプラインには、有効になっている CD トリガーがあります。  トリガーは、新しいビルド成果物が使用可能になるたびにデプロイを開始します。  必要に応じて、トリガーを無効にできるので、デプロイでは手動での実行が必要です。 

1. ブラウザーの左側で、**[タスク]** を選択し、**環境**を選択します。  

1. タスクはデプロイ プロセスによって実行されるアクティビティであり、**フェーズ**にグループ化されます。  このリリース パイプラインには 2 つの**フェーズ**があります。  最初のフェーズには、デプロイ用の VM を構成し、新しい VM を **Azure DevOps デプロイ グループ**に追加する **Azure リソース グループの配置**タスクが含まれます。  Azure DevOps 内の VM デプロイ グループでは、**デプロイ ターゲット** マシンの論理グループが管理されます。

1. この 2 番目のフェーズでは、VM に IIS Web サイトを作成するための **IIS Web アプリの管理**タスクが作成されました。  サイトをデプロイするための 2 番目の **IIS Web アプリの配置**タスクが作成されました。

1. ブラウザーの右側で、**[リリースの表示]** を選択します。  このビューには、リリースの履歴が表示されます。

1. リリースの 1 つの横にある**省略記号**を選択し、**[開く]** を選択します。  **リリース概要**、**関連付けられた作業項目**、**テスト**など、このビューから表示されるいくつかのメニューがあります。

1. **[コミット]** を選択します。  このビューには、特定のデプロイに関連付けられているコードのコミットが表示されます。 リリースを比較して、デプロイ間のコミットの相違を表示します。

1. **[ログ]** を選択します。  ログには、デプロイ プロセスに関する有用な情報が含まれます。  これらは、デプロイ中とデプロイ後の両方に表示できます。

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Azure DevOps Services への変更をコミットし、Azure に自動的にデプロイする 

Web サイトに最新の作業を自動的にデプロイする CI/CD プロセスを使用して、アプリに対してチームで共同作業を行う準備ができました。  Azure DevOps Services Git リポジトリに対する各変更により、Azure DevOps Services でビルドが開始され、Azure DevOps Services CD パイプラインによってご自身の Azure VM へのデプロイが実行されます。  以下の手順に従うか、他の手法を使用して、リポジトリに変更をコミットします。  コード変更により、CI/CD プロセスが開始され、Azure VM 上の IIS Web サイトに新しい変更が自動的にデプロイされます。

1. [Azure DevOps Services] メニューから **[コード]** を選択し、リポジトリに移動します。

1. **Views\Home** ディレクトリに移動し、**Index.cshtml** ファイルの横にある**省略記号**を選択し、**[編集]** を選択します。

1. **div タグ**の 1 つの内側にあるテキストなど、ファイルに変更を加えます。  右上で **[コミット]** を選択します。  もう一度 **[コミット]** を選択して変更をプッシュします。 

1. 数分後に **Azure DevOps Services でビルドが開始**し、リリースが実行されて変更がデプロイされます。  DevOps プロジェクト ダッシュボードを使用するか、ご自身の Azure DevOps Services 組織を使用してブラウザーで、**ビルドの状態**を監視します。

1. リリースが完了したら、ブラウザーで**アプリケーションを最新の情報に更新**し、変更内容を確認します。

## <a name="configure-azure-application-insights-monitoring"></a>Azure Application Insights の監視を構成する

Azure Application Insights を使うと、アプリケーションのパフォーマンスと利用状況を簡単に監視できます。  Azure DevOps プロジェクトによって、アプリケーションの Application Insights リソースが自動的に構成されます。  必要に応じて、さまざまなアラートと監視機能をさらに構成できます。

1. **Azure portal** で、**Azure DevOps プロジェクト**のダッシュボードに移動します。  ダッシュボードの右下で、アプリの **[Application Insights]** リンクを選択します。

1. Azure portal の **Application Insights** ブレードが開きます。  このビューには、アプリの利用状況、パフォーマンス、可用性の監視情報が含まれています。

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. **[時間範囲]** を選択し、**[過去 1 時間]** を選択します。  **[更新]** を選択して結果をフィルター処理します。  これで、過去 60 分間のすべてのアクティビティが表示されます。  **[x]** を選択して時間範囲を終了します。

1. ダッシュボードの上部付近には、**[アラート]** およびその他いくつかの有用なリンクがあります。  **[アラート]** を選択し、**[+ メトリック アラートの追加]** を選択します。

1. アラートの **[名前]** を入力します。

1. 既定のアラートの対象は、**1 秒を超えるサーバー応答時間**です。  **[メトリック]** ドロップダウンを選択して、さまざまなアラート メトリックを確認します。  たとえば、ASP.NET アプリの **ASP.NET 要求の実行時間**を構成できます。  アプリの監視機能を向上させるためにさまざまなアラートを簡単に構成できます。

1. **[Notify via Email owners, contributors, and readers]\(電子メール、所有者、共同作成者、および閲覧者による通知\)** チェック ボックスをオンします。  必要に応じて、Azure ロジック アプリを実行することによってアラートが発生したときに、追加のアクションを実行できます。

1. **[OK]** を選択してアラートを作成します。  数分後に、アラートがダッシュボードでアクティブとして表示されます。  [アラート] 領域を**終了**し、**Application Insights ブレード**に戻ります。

1. **[可用性]** を選択し、**[+ テストの追加]** を選択します。 

1. **[テスト名]** を入力し、**[作成]** を選択します。  これにより、アプリケーションの可用性を確認する簡単な ping テストが作成されます。  数分後にテスト結果が使用可能になり、Application Insights ダッシュボードに可用性の状態が表示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

 > [!NOTE]
 > 次の手順では、リソースが完全に削除されます。  この機能は、画面の指示を注意深く読んだ後でのみ使用してください。

テスト中は、課金を回避するためにリソースをクリーンアップできます。  このチュートリアルで作成した Azure 仮想マシンおよび関連リソースは、不要になったら、Azure DevOps プロジェクト ダッシュボードの **[削除]** 機能を使用して削除できます。  削除機能では、Azure と Azure DevOps の両方で Azure DevOps プロジェクトにより作成されたデータが破棄され、破棄されたデータは取得できなくなるので、**注意してください**。

1. **Azure portal** から、**Azure DevOps プロジェクト**に移動します。
2. ダッシュボードの**右上**で、**[削除]** を選択します。  画面の指示を読んだ後、**[はい]** を選択するとリソースが**完全に削除**されます。

## <a name="next-steps"></a>次の手順

必要に応じて、チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。 この CI/CD パターンを他のプロジェクトのテンプレートとして使用することもできます。  以下の方法について学習しました。

> [!div class="checklist"]
> * ASP.NET アプリ用の Azure DevOps プロジェクトを作成する
> * Azure DevOps Services と Azure サブスクリプションを構成する 
> * Azure DevOps Services CI パイプラインを確認する
> * Azure DevOps Services CD パイプラインを確認する
> * Azure DevOps Services への変更をコミットし、Azure に自動的にデプロイする
> * Azure Application Insights の監視を構成する
> * リソースのクリーンアップ

Azure CI/CD パイプラインについて詳しくは、このチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [CD プロセスをカスタマイズする](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
