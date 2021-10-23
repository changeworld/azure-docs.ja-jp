---
title: GitHub Actions を使用してカスタム ポリシーをデプロイする
titleSuffix: Azure AD B2C
description: GitHub Actions を使用して、CI/CD パイプラインに Azure AD B2C カスタム ポリシーをデプロイする方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 7ce060e9e251313b93930200a73dde9b747c2d75
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037370"
---
# <a name="deploy-custom-policies-with-github-actions"></a>GitHub Actions を使用してカスタム ポリシーをデプロイする

[GitHub Actions](https://docs.github.com/actions/quickstart) を使用すると、カスタムの継続的インテグレーション (CI) および継続的配置 (CD) ワークフローを GitHub リポジトリ内に直接作成できます。 この記事では、GitHub Actions を使用して、Azure Active Directory B2C (Azure AD B2C) [カスタム ポリシー](user-flow-overview.md)のデプロイを自動化する方法について説明します。

カスタム ポリシーのデプロイ プロセスを自動化するには、[Azure AD B2C カスタム ポリシーを展開するための GitHub アクション](https://github.com/marketplace/actions/deploy-azure-ad-b2c-custom-policy)を使用します。 この GitHub アクションは、[Azure AD B2C コミュニティ](https://github.com/azure-ad-b2c/deploy-trustframework-policy)によって開発されました。 

このアクションでは、[Microsoft Graph API](/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta&preserve-view=true) を使用して Azure AD B2C カスタム ポリシーを Azure AD B2C テナントにデプロイします。 このポリシーは、テナント内にまだ存在しない場合は作成されます。 それ以外の場合は置き換えられます。

> [!IMPORTANT]
> Azure Pipelines を使用した Azure AD B2C カスタム ポリシーの管理では、現在、Microsoft Graph API `/beta` エンドポイントで利用できる **プレビュー** 操作を使用します。 実稼働アプリケーションにおけるこれらの API の使用はサポートされていません。 詳細については、「[Microsoft Graph REST API ベータ版のエンドポイント リファレンス](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)」を参照してください。

## <a name="prerequisites"></a>前提条件

* 「[Active Directory B2C でのカスタム ポリシーの概要](tutorial-create-user-flows.md)」にある手順を完了する。
* GitHub リポジトリを作成していない場合は、[1 つ作成します](https://docs.github.com/en/get-started/quickstart/create-a-repo)。  

## <a name="select-a-custom-policies-folder"></a>カスタム ポリシーのフォルダーを選択する

GitHub リポジトリには、すべての Azure AD B2C ポリシー ファイルとその他のアセットを含めることができます。 リポジトリのルート ディレクトリで、カスタム ポリシーが含まれる既存のフォルダーを作成または選択します。 

たとえば、*policies* という名前のフォルダーを選択します。 Azure AD B2C カスタム ポリシー ファイルを *policies* フォルダーに追加します。 次に、変更を **コミット** します。

変更を **プッシュ** しないでください。 これは後で、デプロイ ワークフローの設定後に行います。

## <a name="register-a-microsoft-graph-application"></a>Microsoft Graph アプリケーションを登録する

GitHub アクションが [Microsoft Graph API](microsoft-graph-operations.md) と対話できるようにするには、事前に Azure AD B2C テナント内にアプリケーションの登録を作成します。 まだ行っていない場合は、[Microsoft Graph アプリケーションを登録します](microsoft-graph-get-started.md)。

GitHub アクションから Microsoft Graph のデータにアクセスするには、登録したアプリケーションに、関連する[アプリケーションのアクセス許可](/graph/permissions-reference)を付与します。 アプリ登録の **[API のアクセス許可]** 内で **[Microsoft Graph]**  >  **[ポリシー]**  >  **[Policy.ReadWrite.TrustFramework]** アクセス許可を付与します。

## <a name="create-a-github-encrypted-secret"></a>暗号化された GitHub シークレットを作成する

GitHub シークレットは、組織、リポジトリ、またはリポジトリ環境内に作成する、暗号化された環境変数です。 この手順では、先ほど [MS Graph アプリケーションを登録する](#register-a-microsoft-graph-application)手順で登録したアプリケーションのアプリケーション シークレットを格納します。

Azure AD B2C カスタム ポリシーをデプロイするための GitHub アクションでは、このシークレットを使用して、Microsoft Graph API との対話に使用するアクセス トークンを取得します。 詳細については、「[リポジトリの暗号化されたシークレットの作成](https://docs.github.com/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository)」を参照してください。

GitHub シークレットを作成するには、以下の手順に従います。

1. GitHub で、リポジトリのメイン ページに移動します。
1. リポジトリ名の下で、 **[設定]** を選択します。
1. 左側のサイドバーで、 **[シークレット]** を選択す。
1. **[New repository secret]\(新しいリポジトリ シークレット\)** を選択します。
1. **[名前]** に「**ClientSecret**」と入力します。
1. **[値]** には、前に成したアプリケーション シークレットを入力します。
1. **[Add secret]\(シークレットの追加\)** を選択します。

## <a name="create-a-github-workflow"></a>GitHub ワークフローを作成する

GitHub ワークフローは、ユーザーがリポジトリに追加する自動化されたプロセスです。 ワークフローは 1 つ以上のジョブで構成され、イベントによってスケジュールまたはトリガーできます。 この手順では、カスタム ポリシーをデプロイするワークフローを作成します。

ワークフローを作成するには、以下の手順に従います。

1. GitHub で、リポジトリのメイン ページに移動します。
1. リポジトリ名の下で、 **[アクション]** を選択します。

   ![[GitHub Actions] タブを示すスクリーンショット](media/deploy-custom-policies-github-action/github-actions-tab.png)

1. 前にワークフローを構成していない場合は、 **[ワークフローを自分で設定する]** を選択します。 そうでない場合は、 **[新しいワークフロー]** を選択します。

   ![新しいワークフローを作成する方法を示すスクリーンショット](media/deploy-custom-policies-github-action/set-up-a-workflow.png)

1. GitHub では、`.github/workflows` フォルダー内に `main.yml` という名前のワークフロー ファイルを作成できます。 このファイルには、Azure AD B2C 環境やデプロイするカスタム ポリシーなどの、ワークフローに関する情報を含めます。 GitHub Web エディターで、以下の YAML コードを追加します。

    ```yml
    on: push

    env:
      clientId: 00000000-0000-0000-0000-000000000000
      tenant: your-tenant.onmicrosoft.com
    
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
    
        - name: 'Upload TrustFrameworkBase Policy'
          uses: azure-ad-b2c/deploy-trustframework-policy@v3
          with:
            folder: "./Policies"
            files: "TrustFrameworkBase.xml,TrustFrameworkLocalization.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml"
            tenant: ${{ env.tenant }}
            clientId: ${{ env.clientId }}
            clientSecret: ${{ secrets.clientSecret }}
    ```

1.  YAML ファイルの以下のプロパティを更新します。

    | Section| 名前 | 値 |
    | ---- | ----- |----- |
    | `env` | `clientId` | [MS Graph アプリケーションを登録する](#register-a-microsoft-graph-application)手順で登録したアプリケーションの **アプリケーション (クライアント) ID**。 |
    |`env`| `tenant` | Azure AD B2C [テナントの名前](tenant-management.md#get-your-tenant-name) (例: contoso.onmicrosoft.com)。 |
    | `with`| `folder`| カスタム ポリシー ファイルが格納されているフォルダー。例: `./Policies`。|
    | `with`| `files` | デプロイするポリシー ファイルのコンマ区切りリスト。例: `TrustFrameworkBase.xml,TrustFrameworkLocalization.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml`。|
    
    > [!IMPORTANT]
    > エージェントを実行してポリシー ファイルをアップロードするときは、それらが次の正しい順序でアップロードされていることを確認します。
    >
    > 1. *TrustFrameworkBase.xml*
    > 1. *TrustFrameworkLocalization.xml*
    > 1. *TrustFrameworkExtensions.xml*
    > 1. *SignUpOrSignin.xml*
    > 1. *ProfileEdit.xml*
    > 1. *PasswordReset.xml*

1. **[Start commit]\(コミットの開始\)** を選択します。
1. コミットのメッセージ フィールドの下で、コミットを現在のブランチに追加するか、新しいブランチに追加するかを指定します。 **[新しいファイルをコミット]** または **[新しいファイルを提案]** を選択して、プル要求を作成します。

## <a name="test-your-workflow"></a>ワークフローのテスト

作成したワークフローをテストするには、カスタム ポリシーの変更を **プッシュ** します。 ジョブの実行が開始されると、実行の進行状況を示す視覚化グラフを表示し、GitHub での各ステップのアクティビティを参照することができます。

1. GitHub で、リポジトリのメイン ページに移動します。
1. リポジトリ名の下で、 **[アクション]** を選択します。
1. 左側のサイドバーで、作成したワークフローを選択します。
1. **[ワークフローの実行]** で、表示する実行の名前を選択します。

   ![ワークフロー アクティビティを選択する方法を示すスクリーンショット](media/deploy-custom-policies-github-action/workflow-report.png)

1. **[ジョブ]** または視覚化グラフで、表示するジョブを選択します。
1. 各ステップの結果を表示します。 次のスクリーンショットは、 **[Upload custom policy]** ステップのログを示しています。
 
   ![[Upload custom policy] ステップのログ](media/deploy-custom-policies-github-action/job-activity.png)


## <a name="optional-schedule-your-workflow"></a>省略可能: ワークフローをスケジュールする

作成したワークフローは、[プッシュ](https://docs.github.com/actions/reference/events-that-trigger-workflows#push) イベントによってトリガーされます。 必要に応じて、[プル要求](https://docs.github.com/actions/reference/events-that-trigger-workflows#pull_request)などの別のイベントを選択してワークフローをトリガーすることを選択できます。

[POSIX cron 構文](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07)を使用して特定の UTC 時刻に実行するようにワークフローをスケジュールすることもできます。 スケジュール イベントを使用すると、スケジュールした時刻にワークフローをトリガーできます。 詳細については、「[スケジュールされたイベント](https://docs.github.com/actions/reference/events-that-trigger-workflows#scheduled-events)」を参照してください。

次の例では、毎日 5:30 と 17:30 UTC にワークフローをトリガーしています。

```yml
on:
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron:  '30 5,17 * * *'
```

ワークフローを編集するには:

1. GitHub で、リポジトリのメイン ページに移動します。
1. リポジトリ名の下で、 **[アクション]** を選択します。
1. 左側のサイドバーで、作成したワークフローを選択します。
1. **[ワークフローの実行]** で、表示する実行の名前を選択します。
1. メニューで 3 つのドット **[...]** を選択してから、 **[ワークフロー ファイルの表示]** を選択します。

   ![ワークフロー ファイルを表示する方法を示すスクリーンショット](media/deploy-custom-policies-github-action/edit-workflow.png)
   
1. GitHub Web エディターで、 **[編集]** を選択します。
1. `on: push` を上記の例に変更します。
1. 変更を **コミット** します。

## <a name="next-steps"></a>次のステップ

- [ワークフローをトリガーするイベント](https://docs.github.com/actions/reference/events-that-trigger-workflows)を構成する方法を確認します


