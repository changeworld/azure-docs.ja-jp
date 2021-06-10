---
title: Azure Static Web Apps の GitHub Actions ワークフロー
description: GitHub リポジトリを使用して Azure Static Web Apps への継続的デプロイを設定する方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 4f1f432da33bded4fc0f04170673e5943dec5fb0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311330"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>Azure Static Web Apps プレビューの GitHub Actions ワークフロー

新しい Azure Static Web App リソースを作成すると、アプリの継続的デプロイを制御する GitHub Actions ワークフローが生成されます。 このワークフローは、YAML ファイルによって起動されます。 この記事では、ワークフロー ファイルの構造とオプションについて詳しく説明します。

デプロイは[トリガー](#triggers)によって開始されます。このトリガーにより、個々の[手順](#steps)で定義された[ジョブ](#jobs)が実行されます。

## <a name="file-location"></a>ファイルの場所

GitHub リポジトリを Azure Static Web Apps にリンクすると、そのリポジトリにワークフロー ファイルが追加されます。

生成されたワークフロー ファイルを表示するには、次の手順に従います。

1. GitHub でアプリのリポジトリを開きます。
1. _[Code]_ タブで、`.github/workflows` フォルダーをクリックします。
1. `azure-static-web-apps-<RANDOM_NAME>.yml` のような名前のファイルをクリックします。

リポジトリ内の YAML ファイルは、次の例のようになります。

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

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
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
          action: 'upload'
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          app_location: '/' # App source code path
          api_location: 'api' # Api source code path - optional
          output_location: 'dist' # Built app content directory - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
          action: 'close'
```

## <a name="triggers"></a>トリガー

GitHub Actions の[トリガー](https://help.github.com/actions/reference/events-that-trigger-workflows)は、イベント トリガーに基づいてジョブを実行するように GitHub Actions ワークフローに通知します。 トリガーは、ワークフロー ファイルの `on` プロパティを使用して一覧表示されます。

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

`on` プロパティに関連付けられている設定を使用して、ジョブをトリガーするブランチを定義し、さまざまな pull request の状態に対してトリガーを起動するように設定できます。

この例では、_main_ ブランチが変更されるとワークフローが開始されます。 ワークフローを開始する変更としては、選択したブランチに対するコミットのプッシュと pull request のオープンがあります。

## <a name="jobs"></a>ジョブ

各イベント トリガーにはイベント ハンドラーが必要です。 [ジョブ](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs)では、イベントがトリガーされたときの動作が定義されます。

Static Web Apps ワークフロー ファイルには、2 つの使用可能なジョブがあります。

| 名前                     | 説明                                                                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | `on` プロパティに一覧表示されているブランチに対してコミットをプッシュするか pull request を開くと実行されます。          |
| `close_pull_request_job` | pull request を閉じたときにのみ実行され、これにより、pull request から作成されたステージング環境が削除されます。 |

## <a name="steps"></a>手順

手順は、ジョブのシーケンシャル タスクです。 手順では、依存関係のインストール、テストの実行、運用環境へのアプリケーションのデプロイなどのアクションが実行されます。

ワークフロー ファイルでは、次の手順が定義されます。

| ジョブ                      | 手順                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | <ol><li>Action の環境のリポジトリをチェックアウトします。<li>リポジトリをビルドして Azure Static Web Apps にデプロイします。</ol> |
| `close_pull_request_job` | <ol><li> pull request が終了したことを Azure Static Web Apps に通知します。</ol>                                                        |

## <a name="build-and-deploy"></a>ビルドとデプロイ

`Build and Deploy` という名前の手順では、Azure Static Web Apps インスタンスへのビルドとデプロイが行われます。 `with` セクションで、デプロイ用に次の値をカスタマイズできます。

```yml
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

`repo_token`、`action`、`azure_static_web_apps_api_token` の値は Azure Web Apps Static によって設定されます。手動で変更しないでください。

## <a name="custom-build-commands"></a>カスタム ビルド コマンド

デプロイ中に実行されるコマンドを詳細に制御できます。 次のコマンドは、ジョブの `with` セクションの下で定義できます。

デプロイでは常に、カスタム コマンドの前に `npm install` が呼び出されます。

| コマンド             | 説明                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `app_build_command` | 静的コンテンツ アプリケーションのデプロイ時に実行するカスタム コマンドを定義します。<br><br>たとえば、Angular アプリケーションの運用ビルドを構成するには、`build-prod` という名前の npm スクリプトを作成して `ng build --prod` を実行し、カスタム コマンドとして `npm run build-prod` を入力します。 空白のままにすると、ワークフローでは `npm run build` または `npm run build:azure` コマンドの実行が試みられます。 |
| `api_build_command` | Azure Functions API アプリケーションのデプロイ時に実行するカスタム コマンドを定義します。                                                                                                                                                                                                                                                                                                  |

## <a name="skip-app-build"></a>アプリ ビルドをスキップする

フロントエンド アプリケーションの構築方法を完全に制御する必要がある場合は、ワークフローにカスタム ビルド ステップを追加できます。 次に、Static Web Apps のアクションを構成して、自動ビルド プロセスをバイパスし、前の手順でビルドしたアプリをデプロイすることができます。

アプリのビルドをスキップするには、`skip_app_build` を `true` に設定し、`app_location` をデプロイするフォルダーの場所に設定します。

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
> フロントエンド アプリのビルドのみをスキップできます。 アプリに API がある場合でも、Static Web Apps の GitHub アクションによってビルドされます。

## <a name="route-file-location"></a>ルート ファイルの場所

ワークフローをカスタマイズして、リポジトリ内の任意のフォルダーで [staticwebapp.config.json](routes.md) を検索することができます。 次のプロパティは、ジョブの `with` セクションの下で定義できます。

| プロパティ          | 説明                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `routes_location` | _staticwebapp.config.json_ ファイルが存在するディレクトリの場所を定義します。 この場所は、リポジトリのルートを基準としています。 |

_staticwebapp.config.json_ ファイルの場所を明確にすることは、フロントエンド フレームワークのビルド手順で、既定でこのファイルが `output_location` に移動されない場合に特に重要です。

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
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
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

Monorepo は、複数のアプリケーションのコードが格納されているリポジトリです。 既定で、Static Web Apps ワークフロー ファイルでは、リポジトリ内のすべてのファイルが追跡されますが、1 つのアプリを対象とするように調整することができます。 このため、Monorepo の場合、各静的アプリには、リポジトリの _.github/workflows_ フォルダー内にサイド バイ サイドで存在するその固有の構成ファイルがあります。

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
