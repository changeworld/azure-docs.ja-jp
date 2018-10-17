---
title: Azure DevOps プロジェクトを使用して ASP.NET Core アプリを Azure Kubernetes Service (AKS) にデプロイする | Azure DevOps チュートリアル
description: Azure DevOps プロジェクトを利用すると、Azure を使い始めるのが簡単になります。 Azure DevOps プロジェクトを使用すると、いくつかの簡単な手順で ASP.NET Core アプリを Azure Kubernetes Service (AKS) に簡単にデプロイできます。
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 55ea101b3a03fdb7fc375c4594cab36d4cd79978
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299119"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-kubernetes-service-aks-with-the-azure-devops-project"></a>チュートリアル: Azure DevOps プロジェクトを使用して ASP.NET Core アプリを Azure Kubernetes Service (AKS) にデプロイする

Azure DevOps プロジェクトにより、既存のコードと Git リポジトリが簡単に使用できるようになり、サンプル アプリケーションのいずれかを選択して Azure への継続的インテグレーション (CI) と継続的デリバリー (CD) のパイプラインを作成することができます。  この DevOps プロジェクトによって、AKS などの Azure リソースの作成、CI/CD のビルドおよびリリース パイプラインを含む Azure DevOps Services 内でのリリース パイプラインの作成と構成、および監視用の Azure Application Insights リソースの作成が自動的に行われます。

このチュートリアルの内容は次のとおりです。

> [!div class="checklist"]
> * ASP.NET Core アプリと AKS 用の Azure DevOps プロジェクトを作成する
> * Azure DevOps Services と Azure サブスクリプションを構成する 
> * AKS クラスターを確認する
> * Azure DevOps Services CI パイプラインを確認する
> * Azure DevOps Services CD パイプラインを確認する
> * Git への変更をコミットし、Azure に自動的にデプロイする
> * リソースのクリーンアップ

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-aks"></a>ASP.NET Core アプリと AKS 用の Azure DevOps プロジェクトを作成する

Azure DevOps プロジェクトによって、Azure に CI/CD パイプラインが作成されます。  **新しい Azure DevOps Services** 組織を作成するか、**既存の組織**を使用できます。  Azure DevOps プロジェクトでは、選択した **Azure サブスクリプション**に AKS クラスターなどの **Azure リソース**も作成されます。

1. [Microsoft Azure portal](https://portal.azure.com) にサインインします。

1. 左側のナビゲーション バーで **[リソースの作成]** アイコンを選択し、**DevOps プロジェクト**を検索します。  **[作成]** を選択します。

    ![継続的デリバリーの開始](_img/azure-devops-project-aks/fullbrowser.png)

1. **[.NET]** を選び、**[次へ]** を選択します。

1. **[Choose an application Framework]\(アプリケーション フレームワークの選択\)** で **[ASP.NET Core]** を選択し、**[次へ]** を選択します。

1. **[Kubernetes サービス]** を選択し、**[次へ]** を選択します。  

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Azure DevOps Services と Azure サブスクリプションを構成する

1. **新しい** Azure DevOps 組織を作成するか、**既存**の組織を選択します。  ご自身のプロジェクトの**名前**を選択します。  

1. **Azure サブスクリプション**を選択します。

1. **[変更]** リンクを選択して追加の Azure 構成設定を確認し、**Kubernetes クラスター**の**ノード数**を識別します。  Azure サービスの種類と場所を構成するさまざまなオプションがここにあります。
 
1. Azure の構成領域を終了し、**[完了]** を選択します。

1. このプロセスは、完了するまで数分かかります。  サンプル ASP.NET Core アプリケーションがご自身の Azure DevOps Services 組織内の Azure Repos Git リポジトリに設定され、AKS クラスターが作成されます。その後、CI/CD パイプラインが実行され、ご自身のアプリケーションが Azure にデプロイされます。  

    完了すると、Azure DevOps **プロジェクト ダッシュボード**が Azure portal に読み込まれます。  **Azure DevOps プロジェクト ダッシュボード**には、**Azure portal** の **[すべてのリソース]** から直接移動できます。  

    このダッシュボードでは、Azure Repos **コード リポジトリ**、**Azure DevOps Services CI/CD パイプライン**、および **AKS クラスター**が可視化されます。  さらに、ご自身の Azure DevOps Services パイプラインで追加の CI/CD オプションを構成できます。  ダッシュボードの右側にある **[参照]** を選択して、実行中のアプリケーションを表示します。

## <a name="examine-the-aks-cluster"></a>AKS クラスターを確認する

Azure DevOps プロジェクトによって、AKS クラスターが自動的に構成されます。  クラスターを探索し、カスタマイズできます。  AKS について理解するには、次の手順に従います。

1. **Azure DevOps プロジェクト ダッシュボード**に移動します。

1. DevOps プロジェクト ダッシュボードの右側で、**[Kubernetes サービス]** を選択します。

1. AKS クラスターのブレードが開きます。  このビューから、**コンテナーの正常性の監視**、**ログの検索**、**Kubernetes ダッシュボード**を開くなどのさまざまなアクションを実行することができます。

1. 画面の右側で、**[Kubernetes ダッシュボードを表示]** を選択します。  必要に応じて、Kubernetes ダッシュボードを開く手順に従います。

## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Azure DevOps Services CI パイプラインを確認する

Azure DevOps プロジェクトによって、ご自身の Azure DevOps 組織に Azure CI/CD パイプラインが自動的に構成されます。  パイプラインを探索し、カスタマイズできます。  Azure DevOps Services CI/CD パイプラインについて理解するには、次の手順に従います。

1. **Azure DevOps プロジェクト ダッシュボード**に移動します。

1. **Azure DevOps プロジェクト ダッシュボード**の**上部**から **[ビルド パイプライン]** を選択します。  このリンクによって、ブラウザー タブが開き、ご自身の新しいプロジェクトの Azure DevOps Services ビルド パイプラインが開きます。

1. **[状態]** フィールドの横にあるビルド パイプラインの右にマウス カーソルを移動します。 表示される**省略記号**を選択します。  このアクションにより、**キューへの新しいビルドの挿入**、**ビルドの一時停止**、**ビルド パイプラインの編集**などのいくつかのアクティビティを実行できるメニューが開きます。

1. **[編集]** を選択します。

1. このビューから、**さまざまなタスクについて調べます**。  ビルドでは、Azure DevOps Services Repos Git リポジトリからのソースのフェッチ、依存関係の復元、デプロイに使用した出力の公開など、さまざまなタスクが実行されます。

1. ビルド パイプラインの上部で、**ビルド パイプラインの名前**を選択します。

1. ご自身のビルド パイプラインの**名前**をわかりやすい名前に変更します。  **[保存してキューに登録]** を選択し、**[保存]** を選択します。

1. ご自身のビルド パイプラインの名前の下で、**[履歴]** を選択します。  ビルドの最近の変更の監査証跡が表示されます。  Azure DevOps Services は、ビルド パイプラインに対する変更を追跡し、バージョンを比較できるようにします。

1. **[トリガー]** を選択します。  Azure DevOps プロジェクトでは、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。  必要に応じて、CI プロセスのブランチを含めるか除外するかを選択できます。

1. **[保持]** を選択します。  シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

## <a name="examine-the-azure-devops-services-cd-release-pipeline"></a>Azure DevOps Services CD リリース パイプラインを確認する

Azure DevOps プロジェクトでは、Azure DevOps Services 組織から Azure サブスクリプションにデプロイするために必要な手順が自動的に作成され、構成されます。  これらの手順には、ご自身の Azure サブスクリプションで Azure DevOps Services を認証するための Azure サービス接続の構成が含まれます。  自動化により Azure DevOps Services リリース パイプラインも作成され、リリース パイプラインでは Azure に CD が提供されます。  Azure DevOps Services リリース パイプラインを詳しく調べるには、次の手順に従います。

1. **[ビルドとリリース]** を選択し、**[リリース]** を選択します。  Azure DevOps プロジェクトにより、Azure へのデプロイを管理する Azure DevOps Services リリース パイプラインが作成されました。

1. ブラウザーの左側で、リリース パイプラインの横にある**省略記号**を選択し、**[編集]** を選択します。

1. リリース パイプラインには、リリース プロセスを定義する**パイプライン**が含まれています。  **[成果物]** で、**[ドロップ]** を選択します。  前の手順で調べたビルド パイプラインでは、成果物に使用される出力が生成されます。 

1. **[ドロップ]** アイコンの右側にある **[継続的配置トリガー]** **アイコン** (稲妻として表示されます) を選択します。このリリース パイプラインには、有効になっている CD トリガーがあります。  トリガーは、新しいビルド成果物が使用可能になるたびにデプロイを作成します。  必要に応じて、トリガーを無効にできるので、デプロイでは手動での実行が必要です。 

1. ブラウザーの右側で、**[リリースの表示]** を選択します。  このビューには、リリースの履歴が表示されます。

1. リリースの 1 つの横にある**省略記号**を選択し、**[開く]** を選択します。  **リリース概要**、**関連付けられた作業項目**、**テスト**など、このビューから表示されるいくつかのメニューがあります。

1. **[コミット]** を選択します。  このビューには、特定のデプロイに関連付けられているコードのコミットが表示されます。 リリースを比較して、デプロイ間のコミットの相違を表示できます。

1. **[ログ]** を選択します。  ログには、デプロイ プロセスに関する有用な情報が含まれます。  これらは、デプロイ中とデプロイ後の両方に表示できます。

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Azure DevOps Services への変更をコミットし、Azure に自動的にデプロイする 

 > [!NOTE]
 > 次の手順では、Web アプリに対して単純なテキストの変更を加えて CI/CD パイプラインをテストします。

Web サイトに最新の作業を自動的にデプロイする CI/CD プロセスを使用して、アプリに対してチームで共同作業を行う準備ができました。  Azure DevOps Services Git リポジトリに対する各変更により、Azure DevOps Services でビルドが開始され、CD パイプラインによってご自身の変更が Azure にデプロイされます。  以下の手順に従うか、他の手法を使用して、リポジトリに変更をコミットします。  たとえば、お気に入りのツールや IDE で Git リポジトリを**複製**し、変更をこのリポジトリにプッシュできます。

1. [Azure DevOps Services] メニューから **[コード]**、**[ファイル]** の順に選択し、ご自身のリポジトリに移動します。
1. **Views\Home** ディレクトリに移動し、**Index.cshtml** ファイルの横にある**省略記号**を選択し、**[編集]** を選択します。

1. **div タグ**の 1 つの内側にあるテキストなど、ファイルに変更を加えます。  右上で **[コミット]** を選択します。  もう一度 **[コミット]** を選択して変更をプッシュします。 

1. 数分後に **Azure DevOps Services でビルドが開始**され、リリースが実行されて変更がデプロイされます。  DevOps プロジェクト ダッシュボードを使用するか、ご自身の Azure DevOps Services 組織を使用してブラウザーで、**ビルドの状態**を監視することができます。

1. リリースが完了したら、ブラウザーで**アプリケーションを最新の情報に更新**し、変更内容を確認します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

 > [!NOTE]
 > 次の手順では、リソースが完全に削除されます。  この機能は、画面の指示を注意深く読んだ後でのみ使用してください。

テスト中は、課金を回避するためにリソースをクリーンアップできます。  このチュートリアルで作成した Azure Kubernetes クラスターおよび関連リソースは、不要になったら、Azure DevOps プロジェクト ダッシュボードの **[削除]** 機能を使用して削除できます。  削除機能では、Azure と Azure DevOps Services の両方で Azure DevOps プロジェクトにより作成されたデータが破棄され、破棄されたデータは取得できなくなるので、**注意してください**。

1. **Azure portal** から、**Azure DevOps プロジェクト**に移動します。
2. ダッシュボードの**右上**で、**[削除]** を選択します。  画面の指示を読んだ後、**[はい]** を選択するとリソースが**完全に削除**されます。

## <a name="next-steps"></a>次の手順

必要に応じて、チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。 この CI/CD パターンをご自身の他のパイプラインのテンプレートとして使用することもできます。  以下の方法について学習しました。

> [!div class="checklist"]
> * ASP.NET Core アプリと AKS 用の Azure DevOps プロジェクトを作成する
> * Azure DevOps Services と Azure サブスクリプションを構成する 
> * AKS クラスターを確認する
> * Azure DevOps Services CI パイプラインを確認する
> * Azure DevOps Services CD パイプラインを確認する
> * Git への変更をコミットし、Azure に自動的にデプロイする
> * リソースのクリーンアップ

Kubernetes ダッシュボードの使用について詳しくは、以下をご覧ください。

> [!div class="nextstepaction"]
> [Kubernetes ダッシュボードを使用する](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
