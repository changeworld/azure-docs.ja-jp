---
title: Azure DevOps プロジェクトを使用して既存のコードの CI/CD パイプラインを作成する | VSTS チュートリアル
description: DevOps プロジェクトを利用すると、Azure を使い始めるのが簡単になります。 独自のコードと GitHub リポジトリを使用して、いくつかの簡単な手順で任意の Azure サービス上でアプリを起動できます。
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 192992917432a64c2f9f81761e22bf7d9205703a
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205563"
---
# <a name="create-a-cicd-pipeline-for-your-existing-code-with-the-azure-devops-project"></a>Azure DevOps プロジェクトを使用して既存のコードの CI/CD パイプラインを作成する

Azure DevOps プロジェクトにより、既存のコードと Git リポジトリが簡単に使用できるようになり、サンプル アプリケーションのいずれかを選択して Azure への継続的インテグレーション (CI) と継続的デリバリー (CD) のパイプラインを作成することができます。

このチュートリアルの内容は次のとおりです。

> [!div class="checklist"]
> * Azure DevOps プロジェクトを作成する
> * GitHub リポジトリへのアクセスを構成し、フレームワークを選択する
> * VSTS と Azure サブスクリプションを構成する 
> * GitHub への変更をコミットし、Azure に自動的にデプロイする
> * VSTS CI/CD パイプラインを調べる
> * Azure Application Insights の監視を構成する

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。
* GitHub、または .NET、Java、PHP、Node、Python、静的 Web コードのいずれかを含む外部 Git リポジトリにアクセスします。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

Azure DevOps プロジェクトによって、VSTS に CI/CD パイプラインが作成されます。  **新しい VSTS** アカウントを作成することも、**既存のアカウント**を使用することもできます。  Azure DevOps プロジェクトでは、選択した **Azure サブスクリプション**に **Azure リソース**も作成されます。

1. [Microsoft Azure portal](https://portal.azure.com) にサインインします。

1. 左側のナビゲーション バーで **[+新規]** アイコンを選択し、**DevOps project** を検索します。  **[作成]** を選択します。

    ![継続的デリバリーの開始](_img/azure-devops-project-github/fullbrowser.png)

1. **[Bring Your Own Code]** を選択します。  完了したら、**[次へ]** を選びます。

## <a name="configure-access-to-your-github-repository-and-choose-a-framework"></a>GitHub リポジトリへのアクセスを構成し、フレームワークを選択する

1. **[GitHub]** を選択します。  必要に応じて、**外部 Git リポジトリ**を選択できます。  アプリケーションが格納されている**リポジトリ**と**ブランチ**を選択します。

1. **Web フレームワーク**を選びます。  完了したら、**[次へ]** を選びます。

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

1. 前の手順で選択したアプリケーション フレームワークによって、ここで使用可能な Azure サービスのデプロイ ターゲットの種類が決まります。  任意の**ターゲット サービス**を選択します。  完了したら、**[次へ]** を選びます。

## <a name="configure-vsts-and-an-azure-subscription"></a>VSTS と Azure サブスクリプションを構成する 

1. **新しい** VSTS アカウントを作成することも、**既存の**アカウントを選択することもできます。  VSTS オブジェクトの**名前**を選択します。  アプリケーションの **Azure サブスクリプション**、**場所**、**名前**を選択します。  完了したら、**[完了]** を選びます。

    ![VSTS 情報の入力](_img/azure-devops-project-github/vstsazureinfo.png)

1. 数分で、**プロジェクト ダッシュボード**が Azure portal に読み込まれます。  サンプル アプリケーションが VSTS アカウント内のリポジトリに設定され、ビルドが実行され、アプリケーションが Azure にデプロイされます。  このダッシュボードでは、GitHub **コード リポジトリ**、**VSTS CI/CD パイプライン**、**Azure のアプリケーション**が可視化されます。  ダッシュボードの右側にある **[参照]** を選択して、実行中のアプリケーションを表示します。

    ![ダッシュボード ビュー](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps プロジェクトによって、CI ビルドおよびリリース トリガーが自動的に構成されます。  コードは、GitHub またはその他の外部リポジトリに残ります。  

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>GitHub への変更をコミットし、Azure に自動的にデプロイする 

Web サイトに最新の作業を自動的にデプロイする CI/CD プロセスを使用して、アプリに対してチームで共同作業を行う準備ができました。  GitHub リポジトリに対する各変更により、VSTS でビルドが開始し、VSTS Release Management 定義によって Azure へのデプロイが実行されます。

1.  アプリケーションに変更を加え、変更を GitHub リポジトリに**コミット**します。
2.  しばらくすると、VSTS でビルドが開始します。  DevOps プロジェクト ダッシュボードを使用するか、VSTS アカウントを使用してブラウザーで、ビルドの状態を監視することができます。
3.  ビルドが完了したら、ブラウザーで**アプリケーションを最新の情報に更新**し、変更内容を確認します。

## <a name="examine-the-vsts-cicd-pipeline"></a>VSTS CI/CD パイプラインを調べる

Azure DevOps プロジェクトによって、VSTS アカウントに完全な VSTS CI/CD パイプラインが自動的に構成されました。  パイプラインを探索し、必要に応じてカスタマイズします。  VSTS のビルドおよびリリース定義について理解するには、次の手順に従います。

1. Azure DevOps プロジェクト ダッシュボードの**上部**から **[ビルド パイプライン]** を選択します。  このリンクは、ブラウザー タブを開き、新しいプロジェクトの VSTS ビルド定義を開きます。

1. **[状態]** フィールドの横にあるビルド定義の右にマウス カーソルを移動します。 表示される**省略記号**を選択します。  このアクションにより、キューへの新しいビルドの挿入、ビルドの一時停止、ビルド定義の編集などのいくつかのアクティビティを開始できるメニューが開きます。

1. **[編集]** を選択します。

1. このビューから、ビルド定義の**さまざまなタスクについて調べます**。  ビルドでは、Git リポジトリからのソースのフェッチ、依存関係の復元、デプロイに使用した出力の公開など、さまざまなタスクが実行されます。

1. ビルド定義の上部で、**ビルド定義名**を選択します。

1. ビルド定義の**名前**をわかりやすい名前に変更します。  **[保存してキューに登録]** を選択し、**[保存]** を選択します。

1. ビルド定義名で、**[履歴]** を選択します。  ビルドの最近の変更の監査証跡が表示されます。  VSTS は、ビルド定義に対する変更を追跡し、バージョンを比較できるようにします。

1. **[トリガー]** を選択します。  Azure DevOps プロジェクトでは、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。  必要に応じて、CI プロセスのブランチを含めるか除外するかを選択できます。

1. **[保持]** を選択します。  シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

1. **[ビルドとリリース]** を選択し、**[リリース]** を選択します。  Azure DevOps プロジェクトにより、Azure へのデプロイを管理する VSTS リリース定義が作成されました。

1. ブラウザーの左側で、リリース定義の横にある**省略記号**を選択し、**[編集]** を選択します。

1. リリース定義には、リリース プロセスを定義する**パイプライン**が含まれています。  **[成果物]** で、**[ドロップ]** を選択します。  前の手順で調べたビルド定義では、成果物に使用される出力が生成されます。 

1. **[ドロップ]** アイコンの右側にある **[継続的配置トリガー]** を選択します。  このリリース定義では、新しいビルド成果物が使用可能になるたびにデプロイを実行する有効な CD トリガーがあります。  必要に応じて、トリガーを無効にできるので、デプロイでは手動での実行が必要です。 

1. ブラウザーの左側の **[タスク]** を選択します。  タスクは、デプロイ プロセスによって実行されるアクティビティです。  この例では、**Azure App Service** にデプロイするタスクが作成されました。

1. ブラウザーの右側で、**[リリースの表示]** を選択します。  このビューには、リリースの履歴が表示されます。

1. リリースの 1 つの横にある**省略記号**を選択し、**[開く]** を選択します。  リリース概要、関連付けられた作業項目、テストなど、このビューから表示されるいくつかのメニューがあります。

1. **[コミット]** を選択します。  このビューには、特定のデプロイに関連付けられているコードのコミットが表示されます。 

1. **[ログ]** を選択します。  ログには、デプロイ プロセスに関する有用な情報が含まれます。  これらは、デプロイ中とデプロイ後の両方に表示できます。

## <a name="configure-azure-application-insights-monitoring"></a>Azure Application Insights の監視を構成する

Azure Application Insights を使うと、アプリケーションのパフォーマンスと利用状況を簡単に監視できます。  Azure DevOps プロジェクトによって、アプリケーションの Application Insights リソースが自動的に構成されます。  必要に応じて、さまざまなアラートと監視機能をさらに構成できます。

1. Azure portal で、**Azure DevOps プロジェクト** ダッシュボードに移動します。  ダッシュボードの右下で、アプリの **[Application Insights]** リンクを選択します。

1. Azure portal の **Application Insights** ブレードが開きます。  このビューには、アプリの利用状況、パフォーマンス、可用性の監視情報が含まれています。

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. **[時間範囲]** を選択し、**[過去 1 時間]** を選択します。  **[更新]** を選択して結果をフィルター処理します。  これで、過去 60 分間のすべてのアクティビティが表示されます。  **[x]** を選択して時間範囲を終了します。

1. **[アラート]** を選択し、**[+ メトリック アラートの追加]** を選択します。  

1. アラートの **[名前]** を入力します。

1. **[Source Alter on]\(変更するソース\)** のドロップダウン リストを選択します。  自分の **App Service リソース**
<!-- Could you please confirm if this should be "Source Alter on" instead of "Source Alert on"? -->を選択します。
1. 既定のアラートの対象は、**1 秒を超えるサーバー応答時間**です。  **[メトリック]** ドロップダウンを選択して、さまざまなアラート メトリックを確認します。  アプリの監視機能を向上させるためにさまざまなアラートを簡単に構成できます。

1. **[Notify via Email owners, contributors, and readers]\(電子メール、所有者、共同作成者、および閲覧者による通知\)** チェック ボックスをオンします。  必要に応じて、Azure ロジック アプリを実行することによってアラートが発生したときに、追加のアクションを実行できます。

1. **[OK]** を選択してアラートを作成します。  数分後に、アラートがダッシュボードでアクティブとして表示されます。  [アラート] 領域を**終了**し、**Application Insights ブレード**に戻ります。

1. **[可用性]** を選択し、**[+ テストの追加]** を選択します。 

1. **[テスト名]** を入力し、**[作成]** を選択します。  アプリケーションの可用性を確認する簡単な ping テストが作成されます。  数分後にテスト結果が使用可能になり、Application Insights ダッシュボードに可用性の状態が表示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートで作成した Azure App Service および関連リソースは、不要になったら、Azure DevOps プロジェクト ダッシュボードの **[削除]** 機能を使用して削除できます。

## <a name="next-steps"></a>次の手順

このチュートリアルで CI/CD プロセスを構成したときに、ビルドおよびリリース定義が VSTS プロジェクトに自動的に作成されました。 チームのニーズを満たすようにこれらのビルドおよびリリース定義を変更できます。 以下の方法について学習しました。

> [!div class="checklist"]
> * Azure DevOps プロジェクトを作成する
> * GitHub リポジトリへのアクセスを構成し、フレームワークを選択する
> * VSTS と Azure サブスクリプションを構成する 
> * GitHub への変更をコミットし、Azure に自動的にデプロイする
> * VSTS CI/CD パイプラインを調べる
> * Azure Application Insights の監視を構成する

VSTS パイプラインについて詳しくは、このチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [CD プロセスをカスタマイズする](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
