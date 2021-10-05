---
title: Azure Static Web Apps のビルド構成
description: Azure Static Web Apps のビルド プロセスを制御する方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: cshoe
ms.custom: contperf-fy21q4
ms.openlocfilehash: 54b6231c98ce244913062010782591493ea45a5e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837856"
---
# <a name="build-configuration-for-azure-static-web-apps"></a>Azure Static Web Apps のビルド構成

Azure Static Web Apps のビルド構成は、Azure Pipelines または GitHub Actions のいずれかによって機能します。  サイトごとに、サイトのビルドとデプロイの方法を制御する YAML 構成ファイルがあります。 この記事では、ファイルの構造とオプションについて説明します。

使用可能な構成設定について、次の表で説明します。

| プロパティ | 説明 | 必須 |
|---|---|---|
| `app_location` | このフォルダーには、フロントエンド アプリケーションのソース コードが含まれています。 値は、GitHub のリポジトリのルートと Azure DevOps の現在の作業フォルダーに対して相対的です。 | はい |
| `api_location` | このフォルダーには、API アプリケーションのソース コードが含まれています。 値は、GitHub のリポジトリのルートと Azure DevOps の現在の作業フォルダーに対して相対的です。 | いいえ |
| `output_location` | Web アプリでビルド ステップを実行する場合、出力場所は、パブリック ファイルが生成されるフォルダーになります。 ほとんどのプロジェクトでは、`output_location` は `app_location` に対して相対的です。 ただし、.NET プロジェクトの場合、その場所は発行出力フォルダーに対する相対パスです。 | いいえ |
| `app_build_command` | Node.js アプリケーションの場合は、静的コンテンツ アプリケーションをビルドするためのカスタム コマンドを定義できます。<br><br>たとえば、Angular アプリケーションの運用ビルドを構成するには、`build-prod` という名前の npm スクリプトを作成して `ng build --prod` を実行し、カスタム コマンドとして `npm run build-prod` を入力します。 空白のままにすると、ワークフローでは `npm run build` または `npm run build:azure` コマンドの実行が試みられます。 | いいえ |
| `api_build_command` | Node.js アプリケーションの場合、Azure Functions API アプリケーションをビルドするためのカスタム コマンドを定義できます。 | いいえ |
| `skip_app_build` | フロントエンド アプリのビルドをスキップするには、値を `true` に設定します。 | いいえ |

これらの設定を使用すると、静的 Web アプリの継続的インテグレーションと継続的デリバリー (CI/CD) を実行するために、GitHub Actions または [Azure Pipelines](publish-devops.md) を設定できます。

> [!NOTE]
> 現在、Node.js ビルド用の `app_build_command` と `api_build_command` のみを定義できます。

## <a name="file-name-and-location"></a>ファイル名と場所

# <a name="github-actions"></a>[GitHub のアクション](#tab/github-actions)

構成ファイルは GitHub によって生成され、次の形式を使用して名前を付けた *.github/workflows* フォルダーに格納されます: `azure-static-web-apps-<RANDOM_NAME>.yml`。

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

既定では、構成ファイルは、リポジトリのルートに `azure-pipelines.yml` という名前で格納されます。

---

## <a name="build-configuration"></a>[ビルド構成]

次のサンプル構成では、リポジトリの変更を監視します。 コミットが `main` ブランチにプッシュされると、`app_location` フォルダーからアプリケーションがビルドされ、`output_location` 内のファイルがパブリック Web に提供されます。 さらに、*api* フォルダー内のアプリケーションが、サイトの `api` パスで使用可能になります。

# <a name="github-actions"></a>[GitHub のアクション](#tab/github-actions)

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
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations ######
          app_location: "src" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          action: "close"
```

この構成では次のようになります。

- `main` ブランチのコミットが監視されます。
- [ ブランチで pull request が開かれた、同期された、再度開かれた、または閉じられたときに、GitHub Actions のワークフローが](https://help.github.com/actions/reference/events-that-trigger-workflows)トリガー`main`されます。
- `on` プロパティにリストされているブランチに対してコミットをプッシュするか pull request を開くと、`build_and_deploy_job` が実行されます。
- `app_location` は、Web アプリのソース ファイルが格納されている `src` フォルダーを指します。
- `api_location` は、サイトの API エンドポイントの Azure Functions アプリケーションが格納されている `api` フォルダーを指します。
- `output_location` は、アプリのソース ファイルの最終バージョンが格納されている `public` フォルダーを指します。

`repo_token`、`action`、および `azure_static_web_apps_api_token` の値は、Azure Static Web Apps によって設定されるため、変更しないでください。

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - checkout: self
    submodules: true
  - task: AzureStaticWebApp@0
    inputs:
      app_location: 'src' # App source code path
      api_location: 'api' # Api source code path - optional
      output_location: 'public' # Built app content directory - optional
      azure_static_web_apps_api_token: $(deployment_token)
```

この構成では次のようになります。

- `main` ブランチのコミットが監視されます。
- `app_location` は、Web アプリのソース ファイルが格納されている `src` フォルダーを指します。
- `api_location` は、サイトの API エンドポイントの Azure Functions アプリケーションが格納されている `api` フォルダーを指します。
- `output_location` は、アプリのソース ファイルの最終バージョンが格納されている `public` フォルダーを指します。
- `$(deployment_token)` 変数は、[生成された Azure DevOps デプロイ トークン](./publish-devops.md) を指します。

---

## <a name="custom-build-commands"></a>カスタム ビルド コマンド

Node.js アプリケーションの場合、アプリまたは API のビルド プロセス中に実行されるコマンドをきめ細かく制御できます。 次の例は、`app_build_command` と `api_build_command` の値を使用してビルドを定義する方法を示しています。

# <a name="github-actions"></a>[GitHub のアクション](#tab/github-actions)

```yml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: '/'
  api_location: 'api'
  output_location: 'dist'
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
```
# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yaml
...

inputs:
  app_location: 'src'
  api_location: 'api'
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
  output_location: 'public'
  azure_static_web_apps_api_token: $(deployment_token)
```

---

## <a name="skip-building-front-end-app"></a>フロントエンド アプリのビルドをスキップする

フロントエンド アプリのビルドを完全に制御する必要がある場合は、前のステップでビルドしたアプリの自動ビルドとデプロイをバイパスできます。

フロントエンド アプリのビルドをスキップするには:

- `app_location` をデプロイするファイルの場所に設定します。
- `skip_app_build` を `true` に設定します。
- `output_location` を空の文字列 (`''`) に設定します。

# <a name="github-actions"></a>[GitHub のアクション](#tab/github-actions)

```yml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: 'src/dist'
  api_location: 'api'
  output_location: ''
  skip_app_build: true
```

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yml
...

inputs:
  app_location: 'src/dist'
  api_location: 'api'
  output_location: '' # Leave this empty
  skip_app_build: true
  azure_static_web_apps_api_token: $(deployment_token)
```

---

> [!NOTE]
> フロントエンド アプリのビルドのみをスキップできます。 API は、存在する場合は常にビルドされます。

## <a name="environment-variables"></a>環境変数

ビルドの環境変数を設定するには、ジョブの構成の `env` セクションを使用します。

# <a name="github-actions"></a>[GitHub のアクション](#tab/github-actions)

```yaml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: 'src'
  api_location: 'api'
  output_location: 'public'

env: # Add environment variables here
  HUGO_VERSION: 0.58.0
```

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

現在、Azure DevOps で環境変数はサポートされていません。

---

## <a name="monorepo-support"></a>Monorepo のサポート

Monorepo は、複数のアプリケーションのコードが格納されているリポジトリです。 ワークフローでは、既定でリポジトリ内のすべてのファイルを追跡しますが、1 つのアプリを対象とするように構成を調整できます。

1 つのアプリに対してワークフロー ファイルをターゲットにするには、`push` セクションと `pull_request` セクションにパスを指定します。

# <a name="github-actions"></a>[GitHub のアクション](#tab/github-actions)

monorepo を設定すると、各静的アプリ構成のスコープは、1 つのアプリのファイルのみになります。 リポジトリの _.github/workflows_ フォルダー内で異なるワークフロー ファイルが同時に存在します。

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

この例では、次のファイルに加えられた変更によってのみ、新しいビルドがトリガーされます。

- _app1_ フォルダー内のすべてのファイル
- _api1_ フォルダー内のすべてのファイル
- アプリの _azure-static-web-apps-purple-pond.yml_ ワークフロー ファイルへの変更

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

1 つのリポジトリで複数のアプリケーションをサポートするには、別のワークフロー ファイルを作成し、それを異なる複数の Azure Pipelines に関連付けます。

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [運用前環境で pull request を確認する](review-publish-pull-requests.md)
