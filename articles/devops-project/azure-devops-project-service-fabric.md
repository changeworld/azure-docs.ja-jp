---
title: Azure DevOps プロジェクトを使用して ASP.NET Core アプリを Azure Service Fabric にデプロイする | VSTS チュートリアル
description: DevOps プロジェクトを利用すると、Azure を使い始めるのが簡単になります。 Azure DevOps プロジェクトを使用すると、いくつかの簡単な手順で ASP.NET Core アプリを Azure Service Fabric に簡単にデプロイできます。
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 2fd1fc968eb6b61d7378dbc0efa48f2f7eb2aa54
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967363"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-service-fabric-with-the-azure-devops-project"></a>チュートリアル: Azure DevOps プロジェクトを使用して ASP.NET Core アプリを Azure Service Fabric にデプロイする

Azure DevOps プロジェクトにより、既存のコードと Git リポジトリが簡単に使用できるようになり、サンプル アプリケーションのいずれかを選択して Azure への継続的インテグレーション (CI) と継続的デリバリー (CD) のパイプラインを作成することができます。  この DevOps プロジェクトによって、Azure Service Fabric などの Azure リソースの作成、CI のビルド定義が含まれる VSTS 内のリリース パイプラインの作成と構成、CD のリリース定義の設定、監視用の Azure Application Insights のリソースの作成が自動的に行われます。

このチュートリアルの内容は次のとおりです。

> [!div class="checklist"]
> * ASP.NET Core アプリと Service Fabric 用の Azure DevOps プロジェクトを作成する
> * VSTS と Azure サブスクリプションを構成する 
> * VSTS CI ビルド定義を確認する
> * VSTS CD Release Management 定義を確認する
> * VSTS への変更をコミットし、Azure に自動的にデプロイする
> * リソースのクリーンアップ

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-service-fabric"></a>ASP.NET Core アプリと Service Fabric 用の Azure DevOps プロジェクトを作成する

Azure DevOps プロジェクトによって、VSTS に CI/CD パイプラインが作成されます。  **新しい VSTS** アカウントを作成することも、**既存のアカウント**を使用することもできます。  Azure DevOps プロジェクトでは、選択した **Azure サブスクリプション**に Service Fabric クラスターなどの **Azure リソース**も作成されます。

1. [Microsoft Azure portal](https://portal.azure.com) にサインインします。

1. 左側のナビゲーション バーで **[リソースの作成]** アイコンを選択し、**DevOps project** を検索します。  **[作成]** を選択します。

    ![継続的デリバリーの開始](_img/azure-devops-project-service-fabric/fullbrowser.png)

1. **[.NET]** を選び、**[次へ]** を選択します。

1. **[Choose an application Framework]\(アプリケーション フレームワークの選択\)** で **[ASP.NET Core]** を選択し、**[次へ]** を選択します。

1. **[Service Fabric クラスター]** を選択し、**[次へ]** を選択します。  

## <a name="configure-vsts-and-an-azure-subscription"></a>VSTS と Azure サブスクリプションを構成する

1. **新しい** VSTS アカウントを作成することも、**既存の**アカウントを選択することもできます。  VSTS オブジェクトの**名前**を選択します。  

1. **Azure サブスクリプション**を選択します。

1. **[変更]** リンクを選択して追加の Azure 構成設定を表示し、**Service Fabric クラスター**の**ノード仮想マシンのサイズ**と**オペレーティング システム**を識別します。  Azure サービスの種類と場所を構成するさまざまなオプションがここにあります。
 
1. Azure の構成領域を終了し、**[完了]** を選択します。

1. このプロセスは、完了するまで数分かかります。  サンプル ASP.NET Core アプリケーションが VSTS アカウント内のリポジトリに設定され、Service Fabric クラスターが作成され、ビルドとリリースが実行され、アプリケーションが Azure にデプロイされます。  

    完了すると、Azure DevOps **プロジェクト ダッシュボード**が Azure portal に読み込まれます。  **Azure DevOps プロジェクト ダッシュボード**には、**Azure portal** の **[すべてのリソース]** から直接移動できます。  

    このダッシュボードでは、VSTS **コード リポジトリ**、**VSTS CI/CD パイプライン**、**Service Fabric クラスター**が可視化されます。  さらに、VSTS で追加のオプションを構成できます。  ダッシュボードの右側にある **[参照]** を選択して、実行中のアプリケーションを表示します。

## <a name="examine-the-vsts-ci-build-definition"></a>VSTS CI ビルド定義を確認する

Azure DevOps プロジェクトによって、VSTS アカウントに完全な VSTS CI/CD パイプラインが自動的に構成されます。  パイプラインを探索し、カスタマイズできます。  VSTS ビルド定義について理解するには、次の手順に従います。

1. **Azure DevOps プロジェクト ダッシュボード**に移動します。

1. **Azure DevOps プロジェクト ダッシュボード**の**上部**から **[ビルド パイプライン]** を選択します。  このリンクは、ブラウザー タブを開き、新しいプロジェクトの VSTS ビルド定義を開きます。

1. **[状態]** フィールドの横にあるビルド定義の右にマウス カーソルを移動します。 表示される**省略記号**を選択します。  このアクションにより、**キューへの新しいビルドの挿入**、**ビルドの一時停止**、**ビルド定義の編集**などのいくつかのアクティビティを開始できるメニューが開きます。

1. **[編集]** を選択します。

1. このビューから、ビルド定義の**さまざまなタスクについて調べます**。  ビルドでは、VSTS Git リポジトリからのソースのフェッチ、依存関係の復元、デプロイに使用した出力の公開など、さまざまなタスクが実行されます。

1. ビルド定義の上部で、**ビルド定義名**を選択します。

1. ビルド定義の**名前**をわかりやすい名前に変更します。  **[保存してキューに登録]** を選択し、**[保存]** を選択します。

1. ビルド定義名で、**[履歴]** を選択します。  ビルドの最近の変更の監査証跡が表示されます。  VSTS は、ビルド定義に対する変更を追跡し、バージョンを比較できるようにします。

1. **[トリガー]** を選択します。  Azure DevOps プロジェクトでは、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。  必要に応じて、CI プロセスのブランチを含めるか除外するかを選択できます。

1. **[Retention]\(リテンション期間\)** を選択します。  シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

## <a name="examine-the-vsts-cd-release-management-definition"></a>VSTS CD Release Management 定義を確認する

Azure DevOps プロジェクトでは、VSTS アカウントから Azure サブスクリプションにデプロイするために必要な手順が自動的に作成され、構成されます。  これらの手順には、Azure サブスクリプションで VSTS を認証するための Azure サービス接続の構成が含まれます。  自動化により VSTS リリース定義も作成され、このリリースでは Azure に CD が提供されます。  VSTS リリース定義を詳しく調べるには、次の手順に従います。

1. **[ビルドとリリース]** を選択し、**[リリース]** を選択します。  Azure DevOps プロジェクトにより、Azure へのデプロイを管理する VSTS リリース定義が作成されました。

1. ブラウザーの左側で、リリース定義の横にある**省略記号**を選択し、**[編集]** を選択します。

1. リリース定義には、リリース プロセスを定義する**パイプライン**が含まれています。  **[成果物]** で、**[ドロップ]** を選択します。  前の手順で調べたビルド定義では、成果物に使用される出力が生成されます。 

1. **[ドロップ]** アイコンの右側にある **[継続的配置トリガー]** **アイコン** (稲妻として表示されます) を選択します。このリリース定義には、有効になっている CD トリガーがあります。  トリガーは、新しいビルド成果物が使用可能になるたびにデプロイを開始します。  必要に応じて、トリガーを無効にできるので、デプロイでは手動での実行が必要です。 

1. ブラウザーの右側で、**[リリースの表示]** を選択します。  このビューには、リリースの履歴が表示されます。

1. リリースの 1 つの横にある**省略記号**を選択し、**[開く]** を選択します。  **リリース概要**、**関連付けられた作業項目**、**テスト**など、このビューから表示されるいくつかのメニューがあります。

1. **[コミット]** を選択します。  このビューには、特定のデプロイに関連付けられているコードのコミットが表示されます。 リリースを比較して、デプロイ間のコミットの相違を表示できます。

1. **[ログ]** を選択します。  ログには、デプロイ プロセスに関する有用な情報が含まれます。  これらは、デプロイ中とデプロイ後の両方に表示できます。

## <a name="commit-changes-to-vsts-and-automatically-deploy-to-azure"></a>VSTS への変更をコミットし、Azure に自動的にデプロイする 

 > [!NOTE]
 > 次の手順では、Web アプリに対して単純なテキストの変更を加えて CI/CD パイプラインをテストします。

Web サイトに最新の作業を自動的にデプロイする CI/CD プロセスを使用して、アプリに対してチームで共同作業を行う準備ができました。  VSTS git リポジトリに対する各変更により、VSTS でビルドが開始し、VSTS Release Management 定義によって変更内容が Azure にデプロイされます。  以下の手順に従うか、他の手法を使用して、リポジトリに変更をコミットします。  たとえば、お気に入りのツールや IDE で Git リポジトリを**複製**し、変更をこのリポジトリにプッシュできます。

1. [VSTS] メニューから **[コード]**、**[ファイル]** の順に選択し、リポジトリに移動します。
1. **Views\Home** ディレクトリに移動し、**Index.cshtml** ファイルの横にある**省略記号**を選択し、**[編集]** を選択します。

1. **div タグ**の 1 つの内側にあるテキストなど、ファイルに変更を加えます。  右上で **[コミット]** を選択します。  もう一度 **[コミット]** を選択して変更をプッシュします。 

1. 数分後に、**VSTS でビルドが開始**し、リリースが実行されて変更がデプロイされます。  DevOps プロジェクト ダッシュボードを使用するか、VSTS アカウントを使用してブラウザーで、**ビルドの状態**を監視することができます。

1. リリースが完了したら、ブラウザーで**アプリケーションを最新の情報に更新**し、変更内容を確認します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

 > [!NOTE]
 > 次の手順では、リソースが完全に削除されます。  この機能は、画面の指示を注意深く読んだ後でのみ使用してください。

テスト中は、課金を回避するためにリソースをクリーンアップできます。  このチュートリアルで作成した Azure Service Fabric クラスターおよび関連リソースは、不要になったら、Azure DevOps プロジェクト ダッシュボードの **[削除]** 機能を使用して削除できます。  削除機能では、Azure と VSTS の両方で Azure DevOps プロジェクトにより作成されたデータが破棄され、破棄されたデータは取得できなくなるので、**注意**してください。

1. **Azure portal** から、**Azure DevOps プロジェクト**に移動します。
2. ダッシュボードの**右上**で、**[削除]** を選択します。  画面の指示を読んだ後、**[はい]** を選択するとリソースが**完全に削除**されます。

## <a name="next-steps"></a>次の手順

必要に応じて、チームのニーズを満たすようにこれらのビルドおよびリリース定義を変更できます。 この CI/CD パターンを他のプロジェクトのテンプレートとして使用することもできます。  以下の方法について学習しました。

> [!div class="checklist"]
> * ASP.NET Core アプリと Service Fabric 用の Azure DevOps プロジェクトを作成する
> * VSTS と Azure サブスクリプションを構成する 
> * VSTS CI ビルド定義を確認する
> * VSTS CD Release Management 定義を確認する
> * VSTS への変更をコミットし、Azure に自動的にデプロイする
> * リソースのクリーンアップ

Service Fabric とマイクロサービスについては、以下をご覧ください。

> [!div class="nextstepaction"]
> [マイクロサービスの手法を使用してアプリケーションを構築する](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
