---
title: Azure Data Explorer の Azure DevOps タスク
description: このトピックでは、リリース パイプラインを作成とデプロイについて説明します
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 6394d7149bd4e80f0a17a59a6259eedf4c806fd4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188176"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure Data Explorer の Azure DevOps タスク

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) は、高性能パイプライン、無料のプライベート Git リポジトリ、構成可能なかんばんボード、広範囲で自動化された継続的テスト機能など、開発向け共同作業ツールを提供します。 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) は Azure DevOps の機能であり、あらゆる言語、プラットフォーム、クラウドと連動する高性能パイプラインでコードをデプロイするよう、CI/CD を管理できます。
[Azure Data Explorer - Admin Commands](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) は the Azure Pipelines のタスクであり、リリース パイプラインを作成し、データベースの変更を Azure Data Explorer データベースにデプロイできます。 [Visual Studio Marketplace](https://marketplace.visualstudio.com/) から無料で入手できます。

このドキュメントでは、**Azure Data Explorer – Admin Commands** タスクを使用し、スキーマの変更をデータベースにデプロイする簡単な例について説明します。 完全な CI/CD パイプラインについては、[Azure DevOps ドキュメント](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* Azure Data Explorer クラスター セットアップ:
    * [Azure Data Explorer クラスターとデータベース](/azure/data-explorer/create-cluster-database-portal)
    * [Azure AD アプリケーションをプロビジョニングする](/azure/kusto/management/access-control/how-to-provision-aad-app)ことによって、Azure Active Directory (Azure AD) アプリを作成します。
    * [Azure Data Explorer データベースのアクセス許可を管理すること](/azure/data-explorer/manage-database-permissions)で、Azure Data Explorer データベースの Azure AD アプリへのアクセスを付与します。
* Azure DevOps セットアップ:
    * [無料組織の新規登録](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [組織の作成](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Azure DevOps でのプロジェクトの作成](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Git を使用したコード](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>フォルダーを作成します。

Git リポジトリでサンプル フォルダー (*Functions*、*Policies*、*Tables*) を作成します。 下の画像のように[ここ](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline)から個々のフォルダーにファイルをコピーし、変更内容をコミットします。 次のワークフローを実行するためのサンプル ファイルが提供されます。

![フォルダーを作成します。](media/devops/create-folders.png)

> [!TIP]
> 独自のワークフローを作成するとき、コードをべき等にすることをお勧めします。 たとえば、[.create テーブル](/azure/kusto/management/create-table-command)の代わりに [.create-merge テーブル](/azure/kusto/management/create-table-command#create-merge-table)を使用し、[.create](/azure/kusto/management/functions#create-function) 関数の代わりに [.create-or-alter](/azure/kusto/management/functions#create-or-alter-function) 関数を使用します。

## <a name="create-a-release-pipeline"></a>リリース パイプラインを作成する

1. [Azure DevOps 組織](https://dev.azure.com/)にサインインします。
1. 左側のメニューから **[パイプライン]**  > 、 **[リリース]** の順に選択し、 **[新しいパイプライン]** を選択します。

    ![新しいパイプライン](media/devops/new-pipeline.png)

1. **[新しいリリース パイプライン]** ウィンドウが開きます。 **[パイプライン]** タブの **[テンプレートの選択]** ウィンドウで **[空のジョブ]** を選択します。

     ![テンプレートの選択](media/devops/select-template.png)

1. **[ステージ]** ボタンを選択します。 **[ステージ]** ウィンドウで **[ステージ名]** を追加します。 **[保存]** を選択し、パイプラインを保存します。

    ![ステージに名前を付ける](media/devops/stage-name.png)

1. **[成果物の追加]** ボタンを選択します。 **[成果物の追加]** ウィンドウで、コードが存在するリポジトリを選択し、関連情報を入力し、 **[追加]** をクリックします。 **[保存]** を選択し、パイプラインを保存します。

    ![アーティファクトの追加](media/devops/add-artifact.png)

1. **[変数]** タブで **[+ 追加]** を選択し、タスクで使用される **[エンドポイント URL]** の変数を作成します。 エンドポイントの **[名前]** と **[値]** を書きます。 **[保存]** を選択し、パイプラインを保存します。 

    ![変数の作成](media/devops/create-variable.png)

    Azure portal の **Azure Data Explorer クラスター**の概要ページに Azure Data Explorer クラスター URI が含まれています。自分のエンドポイント URL はそこで見つかります。 URI は形式 `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>` で構築します。  https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB などです。

    ![Azure Data Explorer クラスター URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>デプロイするタスクの作成

1. **[パイプライン]** タブで、 **[1 ジョブ、0 タスク]** をクリックし、タスクを追加します。 

    ![タスクの追加](media/devops/add-task.png)

1. **Tables**、**Functions**、**Policies** をデプロイする 3 つのタスクをこの順番で作成します。 

1. **[タスク]** タブで **[エージェント ジョブ]** の横にある **+** を選択します。 **Azure Data Explorer** を検索します。 **マーケットプレース**で、**Azure Data Explorer – Admin Commands** 拡張をインストールします。 次に、 **[Run Azure Data Explorer Command]\(Azure Data Explorer コマンドの実行\)** で **[追加]** を選択します。

     ![管理者コマンドの追加](media/devops/add-admin-commands.png)

1. 左の **[Kusto コマンド]** をクリックし、次の情報でタスクを更新します。
    * **表示名**:タスクの名前
    * **[ファイル パス]** : **[Tables]** タスクで */Tables/* .csl を指定します。テーブル作成ファイルが *Table* フォルダーにあるためです。
    * **[エンドポイント URL]** : 前の手順で作成した `EndPoint URL` 変数を入力します。
    * **[Use Service Endpoint]\(サービス エンドポイントの使用\)** を選択し、 **[+ 新規]** を選択します。

    ![Kusto コマンドの更新タスク](media/devops/kusto-command-task.png)

1. **[Add Azure Data Explorer service connection]\(Azure Data Explorer サービス接続の追加\)** ウィンドウで次の情報を入力します。

    |設定  |推奨値  |
    |---------|---------|
    |**[接続名]**     |    このサービス エンドポイントを識別する名前を入力します     |
    |**クラスター URL**    |    値は、Azure portal の Azure Data Explorer クラスターの概要セクションにあります | 
    |**サービス プリンシパル ID**    |    AAD アプリ ID (前提条件として作成されています) を入力します     |
    |**サービス プリンシパル アプリのキー**     |    AAD アプリ キー (前提条件として作成されています) を入力します    |
    |**AAD テナント ID**    |      AAD テナント (microsoft.com や contoso.com など) を入力します    |

    **[この接続の使用をすべてのパイプラインに許可します]** チェックボックスを選択します。 **[OK]** を選択します。

    ![サービス接続の追加](media/devops/add-service-connection.png)

1. 手順 1-5 をさらに 2 回繰り返し、*Functions* フォルダーと *Policies* フォルダーからファイルをデプロイします。 **[保存]** を選択します。 **[タスク]** タブで、**Deploy Tables**、**Deploy Functions**、**Deploy Policies** という 3 つのタスクが作成されていることを確認します。

    ![すべてのフォルダーのデプロイ](media/devops/deploy-all-folders.png)

1. **[+ リリース]**  > 、 **[リリースの作成]** の順に選択し、リリースを作成します。

    ![リリースを作成する](media/devops/create-release.png)

1. **[ログ]** タブで、デプロイ状態が成功であることを確認します。

    ![デプロイ成功](media/devops/deployment-successful.png)

これで、実稼働前に 3 つのタスクをデプロイするためのリリース パイプラインを作成できました。