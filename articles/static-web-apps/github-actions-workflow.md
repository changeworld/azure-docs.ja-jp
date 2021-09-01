---
title: Azure Static Web Apps の GitHub Actions ワークフロー
description: GitHub リポジトリを使用して Azure Static Web Apps への継続的デプロイを設定する方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: cshoe
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9dc0c8a83279e3a70bceebb316536485e337c873
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729728"
---
# <a name="github-actions-workflows-for-azure-static-web-apps"></a>Azure Static Web Apps の GitHub Actions ワークフロー

GitHub デプロイを使用すると、YAML ファイルによってサイトのビルド ワークフローが制御されます。 この記事では、ファイルの構造とオプションについて説明します。

デプロイは、個々の[手順](#steps)で構成される[ジョブ](#jobs)を実行する[トリガー](#triggers)から開始されます。

> [!NOTE]
> Azure Static Web Apps では、Azure DevOps ワークフローもサポートされています。 パイプラインの設定の詳細については、[Azure DevOps を使用した発行](publish-devops.md)に関する記事を参照してください。

## <a name="file-name-and-location"></a>ファイル名と場所

リポジトリをリンクすると、Azure Static Web Apps によって、ワークフローを制御するファイルが生成されます。

ワークフロー ファイルを表示するには、次の手順に従います。

1. GitHub でアプリのリポジトリを開きます。
1. **[コード]** タブを選択します。
1. **.github/workflows** フォルダーを選択します。
1. **azure-static-web-apps-<RANDOM_NAME>.yml** と似た名前のファイルを選択します。

## <a name="triggers"></a>トリガー

GitHub Actions の[トリガー](https://help.github.com/actions/reference/events-that-trigger-workflows)は、特定のイベントに基づいてジョブを実行するように GitHub Actions ワークフローに通知します。 トリガーは、ワークフロー ファイルの `on` プロパティを使用して一覧表示されます。

```yml
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
```

この例では、*メイン* 分岐のプル要求が次のようになると、ワークフローが開始されます。

- は次のファイルを開きました: 
- synchronized
- reopened
- closed

ワークフローのこの部分をカスタマイズして、異なる分岐または異なるイベントを対象にすることができます。

## <a name="jobs"></a>ジョブ

各トリガーは、イベントに応答して実行する一連の[ジョブ](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs)を定義します。

| 名前 | 説明 |
| --- | --- |
| `build_and_deploy_job` | `on` プロパティに一覧表示されているブランチに対してコミットをプッシュするか pull request を開くと実行されます。          |
| `close_pull_request_job` | pull request を閉じたときにのみ実行され、これにより、pull request から作成されたステージング環境が削除されます。 |

## <a name="steps"></a>手順

手順は、ジョブのシーケンシャル タスクです。 手順では、依存関係のインストール、テストの実行、運用環境へのアプリケーションのデプロイなどのアクションが実行されます。

| ジョブ | 手順 |
| --- | --- |
| `build_and_deploy_job` | <li>GitHub Action の環境のリポジトリをチェックアウトします。<li>リポジトリをビルドして Azure Static Web Apps にデプロイします。 |
| `close_pull_request_job` | <li> pull request が終了したことを Azure Static Web Apps に通知します。 |

## <a name="build-and-deploy"></a>ビルドとデプロイ

`build_and_deploy_job` という名前の手順では、Azure Static Web Apps へのサイトのビルドとデプロイが行われます。 `with` セクションで、デプロイ用に次の値をカスタマイズできます。

次の例は、これらの値がワークフロー ファイルにどのように表示されるかを示しています。

```yml
...
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  ###### End of Repository/Build Configurations ######
```

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

`repo_token`、`action`、および `azure_static_web_apps_api_token` の値は、Azure Static Web Apps によって設定されるため、変更しないでください。

## <a name="custom-build-commands"></a>カスタム ビルド コマンド

アプリまたは API のビルド プロセス中に実行されるコマンドをきめ細かく制御できます。 次のコマンドは、ジョブの `with` セクションの下に表示されます。

| command | 説明 |
| --- |--- |
| `app_build_command` | 静的コンテンツ アプリケーションのビルド時に実行するカスタム コマンドを定義します。<br><br>たとえば、Angular アプリケーションの運用ビルドを構成するには、`build-prod` という名前の npm スクリプトを作成して `ng build --prod` を実行し、カスタム コマンドとして `npm run build-prod` を入力します。 空白のままにすると、ワークフローでは `npm run build` または `npm run build:azure` コマンドの実行が試みられます。 |
| `api_build_command` | Azure Functions API アプリケーションのビルド時に実行するカスタム コマンドを定義します。 |

次の例は、ジョブの `with` セクション内でカスタム ビルド コマンドを定義する方法を示しています。

```yml
...
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
  ###### End of Repository/Build Configurations ######
```

> [!NOTE]
> 現時点では、Node.js ビルドのカスタム ビルド コマンドのみを定義できます。 ビルド プロセスでは、カスタム コマンドの前に常に `npm install` が呼び出されます。

## <a name="skip-building-front-end-app"></a>フロントエンド アプリのビルドをスキップする

フロントエンド アプリのビルドを完全に制御する必要がある場合は、カスタム ステップをワークフローに追加できます。 たとえば、自動ビルドをバイパスして、前の手順でビルドしたアプリをデプロイすることもできます。

フロントエンド アプリのビルドをスキップするには、`skip_app_build` を `true` に設定し、`app_location` をデプロイするフォルダーの場所に設定します。

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| プロパティ         | 説明                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | フロントエンド アプリのビルドをスキップするには、値を `true` に設定します。 |

> [!NOTE]
> フロントエンド アプリのビルドのみをスキップできます。 API は、存在する場合は常にビルドされます。

## <a name="environment-variables"></a>環境変数

ビルドの環境変数を設定するには、ジョブの構成の `env` セクションを使用します。

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Monorepo のサポート

Monorepo は、複数のアプリケーションのコードが格納されているリポジトリです。 ワークフローでは、既定でリポジトリ内のすべてのファイルを追跡しますが、1 つのアプリを対象とするように構成を調整できます。

Monorepo を設定すると、各静的アプリには、1 つのアプリ内のファイルのみをスコープとする独自の構成ファイルがあります。 リポジトリの _.github/workflows_ フォルダー内で異なるワークフロー ファイルが同時に存在します。

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

1 つのアプリに対してワークフロー ファイルをターゲットにするには、`push` セクションと `pull_request` セクションにパスを指定します。

次の例では、_azure-static-web-apps-purple-pond.yml_ という名前のファイルの `push` セクションと `pull_request` セクションに `paths` ノードを追加する方法を示しています。

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

このインスタンスでは、次のファイルに加えられた変更によってのみ、新しいビルドがトリガーされます。

- _app1_ フォルダー内のすべてのファイル
- _api1_ フォルダー内のすべてのファイル
- アプリの _azure-static-web-apps-purple-pond.yml_ ワークフロー ファイルへの変更

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [運用前環境で pull request を確認する](review-publish-pull-requests.md)
