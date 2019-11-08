---
title: チュートリアル:Azure DevOps Projects を使用して既存のコードの CI/CD パイプラインを作成する
description: Azure DevOps Projects を利用すると、Azure を使い始めるのが簡単になります。 DevOps Projects により、独自のコードと GitHub リポジトリを使用して、いくつかの簡単な手順で任意の Azure サービス上のアプリを起動できるようになります。
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 15ac201a078864717d8e0079801507cf5fc0fe3b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481081"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>チュートリアル:Azure DevOps Projects を使用して既存のコードの CI/CD パイプラインを作成する

Azure DevOps Projects によって提供される簡略化されたエクスペリエンスを使用すると、既存のコードと Git リポジトリを使用するか、サンプル アプリケーションを選択して、Azure への継続的インテグレーション (CI) と継続的デリバリー (CD) のパイプラインを作成することができます。

このチュートリアルの内容は次のとおりです。

> [!div class="checklist"]
> * DevOps Projects を使用して CI/CD パイプラインを作成する
> * GitHub リポジトリへのアクセスを構成し、フレームワークを選択する
> * Azure DevOps と Azure サブスクリプションを構成する 
> * 変更を GitHub にコミットし、Azure に自動的にデプロイする
> * Azure Pipelines CI/CD パイプラインを調べる
> * リソースのクリーンアップ

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。
* GitHub、または .NET、Java、PHP、Node、Python、静的 Web コードのいずれかを含む外部 Git リポジトリにアクセスします。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

Azure DevOps Projects によって、Azure Pipelines に CI/CD パイプラインが作成されます。 新しい Azure DevOps 組織を作成するか、既存の組織を使用できます。 Azure DevOps Projects では、選択した Azure サブスクリプションに Azure リソースも作成されます。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. Azure portal メニューから **[リソースの作成]** を選択します。

   ![Azure portal メニュー - リソースの作成](_img/azure-devops-project-github/createaresource.png)

3. **[DevOps]、[DevOps プロジェクト]** の順に選択します。

   ![DevOps Projects ダッシュボード](_img/azure-devops-project-github/azuredashboard.png)

3. **[独自のコードを使用する]** を選択し、 **[次へ]** を選択します。

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>GitHub リポジトリへのアクセスを構成し、フレームワークを選択する

1. **GitHub** または外部の **Git** コード リポジトリを選択します。 このチュートリアルでは、 **[GitHub]** を選択します。 GitHub リポジトリへのアクセスを Azure に許可するため、初回は GitHub に対する認証が要求される場合があります。

2. **[リポジトリ]** と **[ブランチ]** を選択したら、 **[次へ]** を選択します。

3. Docker コンテナーを使用している場合は、 **[Is app Dockerized]\(アプリはDocker 化されていますか\)** を **[YES]\(はい\)** に変更しますが、このチュートリアルでは **[NO]\(いいえ\)** が選択されたまま **[Next]\(次へ\)** を選択します。 Docker コンテナーの使用の詳細については、 **[i]** アイコンにマウス ポインターを合わせてください。

   ![.NET Framework](_img/azure-devops-project-github/appframework.png)

4. ドロップダウンから**アプリケーション ランタイム**と**フレームワーク**を選択し、 **[次へ]** を選択します。 選択したアプリケーション フレームワークによって、使用可能な Azure サービスのデプロイ ターゲットの種類が決まります。

5. さらに、アプリケーションをデプロイする **Azure サービス**を選択し、 **[次へ]** を選択します。

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps と Azure サブスクリプションを構成する

1. **プロジェクト名**を入力します。

2. 新しい無料の **Azure DevOps 組織**を作成するか、ドロップダウンから既存の組織を選択します。

3. 自分の **Azure サブスクリプション**を選択し、**Web アプリ**の名前を入力するか既定のままにします。 **[場所]** を選択し、 **[完了]** を選択します。 数分後、DevOps Projects のデプロイの概要が Azure portal に表示されます。

4. **[リソースに移動]** を選択して、DevOps Projects ダッシュボードを表示します。 すばやくアクセスできるように、右上隅で、**プロジェクト**をダッシュ​​ボードにピン留めします。 Azure DevOps Projects によって、CI ビルドおよびリリース トリガーが自動的に構成されます。 コードは、GitHub リポジトリまたはその他の外部リポジトリに残ります。 サンプル アプリが **Azure DevOps 組織**のリポジトリに設定されます。 ビルドが実行され、アプリが Azure にデプロイされます。

   ![DevOps Projects ダッシュボードのビュー](_img/azure-devops-project-github/projectsdashboard.png)

5. ダッシュボードでは、コード リポジトリ、CI/CD パイプライン、および Azure のアプリが可視化されます。 右側の Azure リソースの下の **[参照]** を選択すると、実行中のアプリが表示されます。

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>変更を GitHub にコミットし、Azure に自動的にデプロイする

Web サイトに最新の作業を自動的にデプロイする CI/CD プロセスを使用して、アプリに対してチームで共同作業を行う準備ができました。 GitHub リポジトリに対する各変更により、Azure DevOps でビルドが開始され、CD パイプラインによって Azure へのデプロイが実行されます。

1. DevOps プロジェクト ダッシュボードから **[リポジトリ]** を選択します。 ご利用の GitHub リポジトリが新しいブラウザー タブに表示されます。アプリケーションに変更を加え、 **[変更のコミット]** をクリックします。

2. しばらくすると、Azure Pipelines でビルドが開始されます。 ビルドの状態は、DevOps Projects ダッシュボードで監視できるほか、プロジェクト ダッシュボードの **[ビルド パイプライン]** タブを選択することによって、自分の Azure DevOps 組織で監視することができます。

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Azure Pipelines CI/CD パイプラインを調べる

Azure DevOps Projects によって、Azure Pipelines に CI/CD パイプラインが自動的に構成されます。 パイプラインを探索し、必要に応じてカスタマイズします。 ビルドおよびリリース パイプラインについて理解するには、次の手順を行います。

1. DevOps Projects ダッシュボードから **[ビルド パイプライン]** を選択します。

2. **[Azure Pipelines]** ページが開いて、最新のビルドの履歴と各ビルドの状態が表示されます。

   ![Azure DevOps パイプラインのビルド](_img/azure-devops-project-github/pipelinesbuildpage.png)

3. **[ビルド]** ページの右上隅には、現在のビルドを**編集**するためのオプションや、新しいビルドを**キュー**に追加するためのオプション、その他のオプションを含んだメニューを表示するための省略記号 ( **&#8942;** ) が表示されます。ここでは、 **[編集]** を選択します。

4. ビルドでは、リポジトリからのソースのフェッチ、依存関係の復元、デプロイに使用した出力の発行など、さまざまなタスクが実行されます。 右側の **[名前]** で、ビルド パイプラインの名前をもっとわかりやすい名前に変更します。 **[保存してキューに登録]** を選択し、 **[保存]** を選択したら、コメントを残してもう一度 **[保存]** を選択します。

   ![Azure DevOps の [ビルド] ページ](_img/azure-devops-project-github/buildpage.png)

5. ビルドの最近の変更の監査証跡を表示するには、 **[履歴]** タブを選択します。ビルド パイプラインに対するすべての変更が Azure DevOps によって追跡され、バージョンを比較できます。

6. **[トリガー]** タブを選択します。Azure DevOps Projects によって、いくつかの既定の設定を使用して CI トリガーが自動的に作成されます。 **[継続的インテグレーションを有効にする]** などのトリガーを設定して、コードの変更がコミットされるたびにビルドを実行したり、特定の時刻にビルドが実行されるようにスケジューリングしたりすることができます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成した Azure App サービスと関連リソースが必要なくなったら、削除してかまいません。 そうするには、DevOps Projects ダッシュボードで**削除**機能を使用します。

## <a name="next-steps"></a>次の手順

このチュートリアルで CI/CD プロセスを構成したときに、ビルドとリリース パイプラインが Azure DevOps Projects に自動的に作成されました。 チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。 以下の方法について学習しました。

> [!div class="checklist"]
>  * DevOps Projects を使用して CI/CD パイプラインを作成する
> * GitHub リポジトリへのアクセスを構成し、フレームワークを選択する
> * Azure DevOps と Azure サブスクリプションを構成する
> * 変更を GitHub にコミットし、Azure に自動的にデプロイする
> * Azure Pipelines CI/CD パイプラインを調べる
> * リソースのクリーンアップ

CI/CD パイプラインの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [複数ステージの継続的デプロイ (CD) パイプラインを定義する](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

アプリケーションの監視の詳細については、次のページを参照してください。
  
 > [!div class="nextstepaction"]
 > [Azure Monitor とは](https://docs.microsoft.com/azure/azure-monitor/overview)
