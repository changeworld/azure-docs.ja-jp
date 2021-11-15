---
title: 'チュートリアル: Service Connector を使用して Azure Storage Blob に接続される Web アプリケーションをデプロイする'
description: Service Connector を使用して Azure Storage Blob に接続される Web アプリを作成します。
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8768d188ae56896fe1f7272ee18533e95343e69c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846560"
---
# <a name="tutorial-deploy-web-application-connected-to-azure-storage-blob-with-service-connector"></a>チュートリアル: Service Connector を使用して Azure Storage Blob に接続される Web アプリケーションをデプロイする

マネージド ID を使用して、Azure App Service で実行されている Web アプリ (サインインしているユーザーではありません) に代わって Azure Storage にアクセスする方法について説明します。 このチュートリアルでは、Azure CLI を使用して以下のタスクを実了します。

> [!div class="checklist"]
> * Azure CLI を使用して初期環境を設定する
> * ストレージ アカウントと Azure Blob Storage コンテナーを作成する。
> * Azure App Service にコードをデプロイし、Service Connector を使用してマネージド ID でストレージに接続する

## <a name="1-set-up-your-initial-environment"></a>1.初期環境を設定する

1. アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
2. <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.30.0 以降をインストールします。これを任意のシェルで使用してコマンドを実行し、Azure リソースのプロビジョニングと構成を行います。


Azure CLI のバージョンが 2.30.0 以降であることを確認します。

```Azure CLI
az --version
```

アップグレードする必要がある場合は、`az upgrade` コマンド (バージョン 2.11 以降が必要) を試すか、「<a href="/cli/azure/install-azure-cli" target="_blank"> Azure CLI のインストール</a>」をご覧ください。

次に CLI から Azure にサインインします。

```Azure CLI
az login
```

このコマンドを実行すると、お客様の資格情報を収集するためにブラウザーが開かれます。 コマンドが終了すると、ご利用のサブスクリプションに関する情報を含んだ JSON 出力が表示されます。

サインイン後は、Azure CLI を使用して Azure コマンドを実行して、サブスクリプション内のリソースを操作することができます。

## <a name="2-clone-or-download-the-sample-app"></a>2.サンプル アプリをクローンまたはダウンロードする

サンプル リポジトリをクローンします。
```Bash
git clone https://github.com/Azure-Samples/serviceconnector-webapp-storageblob-dotnet.git
```

リポジトリのルート フォルダーに移動します。
```Bash
cd WebAppStorageMISample
```

## <a name="3-create-the-app-service-app"></a>3. App Service アプリを作成する

ターミナルで、現在の場所がアプリ コードを含む *WebAppStorageMISample* リポジトリ フォルダーであることを確認します。

[`az webapp up`](/cli/azure/webapp#az_webapp_up) コマンドを使用して App Service アプリ (ホスト プロセス) を作成します。

```Azure CLI
az webapp up --name <app-name> --sku B1 --location eastus --resource-group ServiceConnector-tutorial-rg
```

- 引数 `--location` には、[Service Connector でサポートされている](concept-region-support.md)場所を使用してください。
-  *\<app-name>* を Azure 全体で一意である名前に置き換えます (サーバー エンドポイントは `https://<app-name>.azurewebsites.net`)。 *\<app-name>* に使用できる文字は `A`-`Z`、`0`-`9`、`-` です。 会社名とアプリ識別子を組み合わせて使用すると、適切なパターンになります。

## <a name="4-create-a-storage-account-and-blob-storage-container"></a>4. ストレージ アカウントと Blob Storage コンテナーを作成する

ターミナルで次のコマンドを実行して、汎用 v2 ストレージ アカウントと Blob Storage コンテナーを作成します。 *\<storage-name>* を一意の名前に **置き換え** ます。 コンテナー名は小文字である必要があり、英文字または数字で始まる必要があり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。

```Azure CLI
az storage account create --name <storage-name> --resource-group ServiceConnector-tutorial-rg --sku Standard_RAGRS --https-only
```


## <a name="5-connect-app-service-app-to-blob-storage-container-with-managed-identity"></a>5. App Service アプリを Blob Storage コンテナーにマネージド ID で接続する

ターミナルで次のコマンドを実行して、Web アプリをマネージド ID で Blob Storage に接続します。

```Azure CLI
az webapp connection create storage-blob -g ServiceConnector-tutorial-rg -n <app-name> --tg ServiceConnector-tutorial-rg --account <storage-name> --system-identity
```

- *\<app-name>* を手順 3. で使用した Web アプリの名前に **置き換え** ます。
- *\<storage-name>* を手順 4. で使用したストレージ アプリの名前に **置き換え** ます。

> [!NOTE]
> "The subscription is not registered to use Microsoft.ServiceLinker (サブスクリプションが Microsoft.ServiceLinker を使用するように登録されていません)" というエラー メッセージが表示される場合は、`az provider register -n Microsoft.ServiceLinker` を実行して Service Connector リソース プロバイダーを登録し、接続コマンドを再度実行してください。 

## <a name="6-run-sample-code"></a>6. サンプル コードを実行する

ターミナルで次のコマンドを実行して、ブラウザーでサンプル アプリケーションを開きます。 *\<app-name>* を手順 3. で使用した Web アプリの名前に置き換えます。

```Azure CLI
az webapp browse --name <app-name> 
```

サンプル コードは、Web アプリケーションです。 インデックス ページを更新するたびに、`Hello Service Connector! Current is {UTC Time Now}` というテキストの BLOB がストレージ コンテナーで作成されるか更新され、インデックス ページで表示するために読み取り直されます。

## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
