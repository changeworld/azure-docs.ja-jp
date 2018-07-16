---
title: Azure DevOps プロジェクトで Ruby on Rails 用の CI/CD パイプラインを作成する | クイック スタート
description: DevOps プロジェクトを利用すると、Azure を使い始めるのが簡単になります。 いくつかの簡単な手順により、Azure サービス上で Ruby on Rails の Web アプリを稼働させることができます。
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 773e77e0965dfb059460faf564d8cc3d80cef3ea
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967014"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-with-the-azure-devops-project"></a>Azure DevOps プロジェクトで Ruby on Rails 用の CI/CD パイプラインを作成する

**Azure DevOps プロジェクト**を使用して、Ruby on Rails アプリケーション用に継続的インテグレーション (CI) と継続的デリバリー (CD) を構成します。  Azure DevOps プロジェクトによって、VSTS ビルドおよびリリース パイプラインの初期構成が簡略化されます。

Azure サブスクリプションをお持ちでない場合は、[Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

Azure DevOps プロジェクトによって、VSTS に CI/CD パイプラインが作成されます。  **新しい VSTS** アカウントを作成することも、**既存のアカウント**を使用することもできます。  Azure DevOps プロジェクトでは、選択した **Azure サブスクリプション**に **Azure リソース**も作成されます。

1. [Microsoft Azure portal](https://portal.azure.com) にサインインします。

1. 左側のナビゲーション バーで **[リソースの作成]** アイコンを選択し、**DevOps project** を検索します。  **[作成]** を選択します。

    ![継続的デリバリーの開始](_img/azure-devops-project-ruby/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>サンプル アプリケーションと Azure サービスを選択する

1. **Ruby** サンプル アプリケーションを選択します。

1. **Ruby on Rails** アプリケーション フレームワークを選択します。  完了したら、**[次へ]** を選びます。

1. **Web App on Linux** が既定の展開先です。  必要に応じて、Web App for Containers を選択できます。  前の手順で選択したアプリケーション フレームワークによって、ここで使用可能な Azure サービスのデプロイ ターゲットの種類が決まります。  任意の**ターゲット サービス**を選択します。  完了したら、**[次へ]** を選びます。

## <a name="configure-vsts-and-an-azure-subscription"></a>VSTS と Azure サブスクリプションを構成する 

1. **新しい**無料の VSTS アカウントを作成することも、**既存の**アカウントを選択することもできます。  VSTS オブジェクトの**名前**を選択します。  アプリケーションの **Azure サブスクリプション**、**場所**、**名前**を選択します。  完了したら、**[完了]** を選びます。

    ![VSTS 情報の入力](_img/azure-devops-project-ruby/vstsazureinfo.png)

1. 数分で、**プロジェクト ダッシュボード**が Azure portal に読み込まれます。  サンプル アプリケーションが VSTS アカウント内のリポジトリに設定され、ビルドが実行され、アプリケーションが Azure にデプロイされます。  このダッシュボードでは、**コード リポジトリ**、**VSTS CI/CD パイプライン**、**Azure のアプリケーション**が可視化されます。  ダッシュボードの右側にある **[参照]** を選択して、実行中のアプリケーションを表示します。

    ![ダッシュボード ビュー](_img/azure-devops-project-ruby/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>コードの変更をコミットし、CI/CD を実行する

Azure DevOps プロジェクトにより、VSTS または GitHub アカウントに Git リポジトリが作成されました。  リポジトリを表示し、アプリケーションにコード変更を加えるには、次の手順に従います。

1. DevOps プロジェクト ダッシュボードの左側にある**マスター**分岐のリンクを選択します。  このリンクは、新しく作成された Git リポジトリのビューを開きます。

1. リポジトリのクローン URL を表示するには、ブラウザーの右上から **[複製]** を選択します。 お気に入りの IDE で Git リポジトリを複製できます。  次のいくつかの手順では、Web ブラウザーを使用してマスター ブランチに直接コード変更を行い、コミットできます。

1. ブラウザーの左側で、**app/views/pages/home.html.erb** ファイルに移動します。

1. **[編集]** を選び、テキストをいくつか変更します。  たとえば、いずれかの div タグ内のテキストを変更します。

1. **[コミット]** を選択し、変更を保存します。

1. ブラウザーで、**Azure DevOps プロジェクト ダッシュボード**に移動します。  ビルドが進行中であることが表示されます。  行った変更は、VSTS CI/CD パイプラインを介して自動的にビルドおよびデプロイされます。

## <a name="examine-the-vsts-cicd-pipeline"></a>VSTS CI/CD パイプラインを調べる

Azure DevOps プロジェクトによって、VSTS アカウントに完全な VSTS CI/CD パイプラインが自動的に構成されました。  パイプラインを探索し、必要に応じてカスタマイズします。  VSTS のビルドおよびリリース定義について理解するには、次の手順に従います。

1. Azure DevOps プロジェクト ダッシュボードの**上部**から **[ビルド パイプライン]** を選択します。  このリンクは、ブラウザー タブを開き、新しいプロジェクトの VSTS ビルド定義を開きます。

1. **省略記号**を選択します。  このアクションにより、キューへの新しいビルドの挿入、ビルドの一時停止、ビルド定義の編集などのいくつかのアクティビティを開始できるメニューが開きます。

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

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートで作成した Azure App Service および関連リソースは、不要になったら、Azure DevOps プロジェクト ダッシュボードの **[削除]** 機能を使用して削除できます。

## <a name="next-steps"></a>次の手順

チームのニーズを満たすためのビルドおよびリリース定義の変更について詳しくは、このチュートリアルをご覧ください:

> [!div class="nextstepaction"]
> [CD プロセスをカスタマイズする](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
