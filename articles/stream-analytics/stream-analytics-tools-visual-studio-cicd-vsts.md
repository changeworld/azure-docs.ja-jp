---
title: VSTS を使用して CI/CD で Azure Stream Analytics ジョブを配置する方法に関するチュートリアル
description: この記事では、VSTS を使用して CI/CD で Stream Analytics ジョブを配置する方法について説明します。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: d4f1e188a1a145ba3be5fb45d2b0ea4d0bfd57a7
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "41918260"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-vsts"></a>チュートリアル: VSTS を使用して CI/CD で Azure Stream Analytics ジョブを配置する
このチュートリアルでは、Visual Studio Team Services を使用して、Azure Stream Analytics ジョブの継続的インテグレーションと継続的配置を設定する方法について説明します。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * プロジェクトにソース管理を追加する
> * Team Services でビルド定義を作成する
> * Team Services でリリース定義を作成する
> * アプリケーションを自動的にデプロイおよびアップグレードする

## <a name="prerequisites"></a>前提条件
始める前に、以下のものを用意してください。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
* [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) と、**Azure 開発**ワークロードまたは**データの保存と処理**ワークロードをインストールします。
* [Visual Studio で Stream Analytics プロジェクト](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs)を作成します。
* [Visual Studio Team Services](https://visualstudio.microsoft.com/team-services/) アカウントを作成します。

## <a name="configure-nuget-package-dependency"></a>NuGet パッケージの依存関係を構成する
任意のマシンで自動ビルドと自動配置を実行するには、NuGet パッケージ `Microsoft.Azure.StreamAnalytics.CICD` を使用する必要があります。 これは、Stream Analytics Visual Studio プロジェクトの継続的インテグレーションおよびデプロイ プロセスをサポートする MSBuild、ローカル実行、デプロイ ツールを提供します。 詳細については、[Stream Analytics の CI/CD ツール](stream-analytics-tools-for-visual-studio-cicd.md)に関するページを参照してください。

**packages.config** をプロジェクト ディレクトリに追加します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>新しい Team Services の Git リポジトリで Visual Studio ソリューションを共有する
ビルドを生成できるよう、Team Services のチーム プロジェクトで、アプリケーションのソース ファイルを共有します。  

1. Visual Studio の右下隅のステータス バーで **[ソース管理に追加]**、**[Git]** の順に選択して、プロジェクトの新しいローカル Git リポジトリを作成します。 

2. **チーム エクスプローラー**の **[同期]** ビューで、**[Visual Studio Team Services へのプッシュ]** にある **[Git リポジトリの発行]** ボタンを選択します。

   ![Git リポジトリのプッシュ](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. 電子メールを確認し、**[Team Services ドメイン]** ドロップダウンから自分のアカウントを選択します。 リポジトリ名を入力し、**[リポジトリの発行]** を選択します。

   ![Git リポジトリのプッシュ](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    リポジトリを公開すると、ローカル リポジトリと同じ名前の新しいチーム プロジェクトが自分のアカウントに作成されます。 既存のチーム プロジェクトでリポジトリを作成するには、**[リポジトリ名]** の横にある **[詳細]** をクリックして、チーム プロジェクトを選択します。 **[See it on the web]\(Web を参照\)** を選択すると、ブラウザーでコードを表示できます。
 
## <a name="configure-continuous-delivery-with-vsts"></a>VSTS で継続的デリバリーを構成する
Team Services のビルド定義では、順次実行されるビルド ステップで構成されたワークフローを記述します。 Team Services のビルド定義の詳細については、[こちら](https://www.visualstudio.com/docs/build/define/create)をご覧ください。 

Team Services のリリース定義では、クラスターにアプリケーション パッケージをデプロイするワークフローを記述します ビルド定義とリリース定義を併用すると、ソース ファイルから始まり、クラスターでのアプリケーションの実行で終わるワークフロー全体を実行できます。 Team Services のリリース定義の詳細については、 [こちら](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)をご覧ください。

### <a name="create-a-build-definition"></a>ビルド定義の作成
Web ブラウザーを開き、[Visual Studio Team Services](https://app.vsaex.visualstudio.com/) で先ほど作成したチーム プロジェクトに移動します。 

1. **[ビルドとリリース]** タブで、**[ビルド]**、**[+ 新規]** の順に選択します。  **[VSTS Git]**、**[続行]** の順に選択します。
    
    ![ソースの選択](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. **[テンプレートの選択]** で **[空のプロセス]** をクリックし、空の定義を使って開始します。
    
    ![ビルド テンプレートを選択する](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. **[トリガー]** で **[継続的インテグレーションを有効にする]** トリガー状態をオンにして、継続的インテグレーションを有効にします。  **[保存してキューに登録]** を選択して、ビルドを手動で開始します。 
    
    ![トリガー状態](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. ビルドは、プッシュ時またはチェックイン時にもトリガーされます。 ビルドの進行状況を確認するには、**[ビルド]** タブに切り替えます。ビルドが正常に実行されることを確認したら、アプリケーションをクラスターに配置するリリース定義を定義する必要があります。 ビルド定義の横にある省略記号を右クリックして、**[編集]** を選択します。

5.  **[タスク]** の **[エージェント キュー]** に「Hosted」と入力します。
    
    ![エージェント キューの選択](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. **[フェーズ 1]** で **[+]** をクリックして、**[NuGet]** タスクを追加します。
    
    ![NuGet タスクの追加](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. **[詳細設定]** を展開して、**[宛先ディレクトリ]** に「`$(Build.SourcesDirectory)\packages`」と入力します。 残りの既定の NuGet 構成値はそのままにします。

   ![NuGet タスクの構成](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. **[フェーズ 1]** で **[+]** をクリックして、**[MSBuild]** タスクを追加します。

   ![MSBuild タスクの追加](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. **[MSBuild 引数]** を次のように変更します。

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![MSBuild タスクの構成](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. **[フェーズ 1]** で **[+]** をクリックして、**[Azure リソース グループの配置]** タスクを追加します。 
    
    ![[Azure リソース グループの配置] タスクの追加](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. **[Azure の詳細]** を展開し、以下のように構成を入力します。
    
    |**設定**  |**推奨値**  |
    |---------|---------|
    |サブスクリプション  |  サブスクリプションを選択します。   |
    |アクションを表示します。  |  リソース グループを作成または更新します。   |
    |リソース グループ  |  リソース グループ名を入力します。   |
    |テンプレート  | <実際のソリューション パス>\bin\Debug\Deploy\\<実際のプロジェクト名>.JobTemplate.json   |
    |Template parameters  | <実際のソリューション パス>\bin\Debug\Deploy\\<実際のプロジェクト名>.JobTemplate.parameters.json   |
    |テンプレート パラメーターのオーバーライド  | オーバーライドするテンプレート パラメーターをテキスト ボックスに入力します。 例えば、–storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre) です。 このプロパティは省略可能です。ただし、主要なパラメーターがオーバーライドされない場合、ビルドでエラーが発生します。    |
    
    ![プロパティの設定](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. **[保存してキューに登録]** をクリックして、ビルド定義をテストします。
    
    ![パラメーターのオーバーライドの設定](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>ビルド プロセスの失敗
ビルド定義の **[Azure リソース グループの配置]** タスクでテンプレート パラメーターをオーバーライドしなかった場合、null 値の配置パラメーターに関するエラーが発生する可能性があります。 エラーを解決するには、ビルド定義に戻って null 値のパラメーターをオーバーライドします。

   ![失敗したビルド プロセス](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>リリースをトリガーする変更のコミットとプッシュ
Team Services へのコード変更をチェックインして、継続的インテグレーション パイプラインが機能していることを確認します。    

コードを記述すると、変更内容は Visual Studio によって自動的に追跡されます。 右下のステータス バーで [保留中の変更] アイコンを選択して、ローカル Git リポジトリに変更をコミットします。

1. チーム エクスプローラーの **[変更]** ビューで、更新内容を説明するメッセージを追加し、変更をコミットします。

    ![変更のコミットとプッシュ](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. 発行されていない変更のステータス バー アイコンを選択するか、またはチーム エクスプローラーで [同期] ビューを選択します。 **[プッシュ]** を選択して、Team Services と TFS へコードを更新します。

    ![変更のコミットとプッシュ](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

Team Services へ変更をプッシュすると、ビルドが自動的にトリガーされます。  ビルド定義が正常に完了すると、リリースは自動的に作成され、クラスター上のジョブの更新が開始されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ストリーミング ジョブ、および関連するすべてのリソースは、不要になったら削除します。 ジョブを削除すると、ジョブによって消費されるストリーミング ユニットに対する課金を回避することができます。 ジョブを後で使用する計画がある場合は、ジョブを停止し、必要なときに再起動することができます。 このジョブの使用を続けない場合は、以下の手順に従って、このチュートリアルで作成したすべてのリソースを削除してください。

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックし、作成したリソースの名前をクリックします。  
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

Visual Studio 用の Azure Stream Analytics ツールを使用して継続的インテグレーションと継続的配置のプロセスを設定する方法について確認するには、CI/CD パイプラインの設定に関する記事に進みます。

> [!div class="nextstepaction"]
> [Stream Analytics ツールで継続的に統合および開発する](stream-analytics-tools-for-visual-studio-cicd.md)