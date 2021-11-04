---
title: 'クイック スタート: Azure Arc で Web アプリを作成する'
description: Azure Arc 上の App Service の使用を開始して最初の Web アプリをデプロイします。
ms.topic: quickstart
ms.date: 11/02/2021
ms.openlocfilehash: 356e1b5f13d2a4b7e00c3e1c11b5dcbf295d6fd3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455867"
---
# <a name="create-an-app-service-app-on-azure-arc-preview"></a>Azure Arc で App Service アプリを作成する (プレビュー)

このクイックスタートでは、[Azure Arc 対応 Kubernetes クラスターに App Service アプリを](overview-arc-integration.md)作成します (プレビュー)。 このシナリオでは Linux アプリのみをサポートし、組み込みの言語スタックまたはカスタム コンテナーを使用できます。

## <a name="prerequisites"></a>前提条件

- [App Service を実行するように Azure Arc 対応 Kubernetes を設定する](manage-create-arc-environment.md)。

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="1-create-a-resource-group"></a>1. リソース グループを作成する

次のコマンドを実行します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="2-get-the-custom-location"></a>2. カスタムの場所を取得する

[!INCLUDE [app-service-arc-get-custom-location](../../includes/app-service-arc-get-custom-location.md)]


## <a name="3-create-an-app"></a>3. アプリを作成する

次の例では Node.js アプリを作成します。 `<app-name>` をクラスター内で一意の名前で置き換えます (有効な文字は、`a-z`、`0-9`、および `-` です)。 サポートされているすべてのランタイムを確認するには、[`az webapp list-runtimes --linux`](/cli/azure/webapp) を実行します。

```azurecli-interactive
 az webapp create \
    --resource-group myResourceGroup \
    --name <app-name> \
    --custom-location $customLocationId \
    --runtime 'NODE|12-lts'
```

## <a name="4-deploy-some-code"></a>4. コードをデプロイする

> [!NOTE]
> `az webapp up` は、パブリック プレビュー期間中はサポートされていません。

Git を使用してサンプルの Node.js アプリを取得し、[ZIP デプロイ](deploy-zip.md)を使用してデプロイします。 `<app-name>` は、Web アプリの名前に置き換えます。

```azurecli-interactive
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
cd nodejs-docs-hello-world
zip -r package.zip .
az webapp deployment source config-zip --resource-group myResourceGroup --name <app-name> --src package.zip
```

## <a name="5-get-diagnostic-logs-using-log-analytics"></a>5. Log Analytics を使用して診断ログを取得する

> [!NOTE]
> Log Analytics を使用するには、[App Service 拡張機能 をインストールする](manage-create-arc-environment.md#install-the-app-service-extension)ときに有効にしておく必要があります。 Log Analytics なしで拡張機能をインストールした場合は、この手順をスキップします。

[App Service 拡張機能で構成されている Log Analytics ワークスペース](manage-create-arc-environment.md#install-the-app-service-extension)に移動し、左側のナビゲーションで [ログ] をクリックします。 次のサンプル クエリを実行して、過去 72 時間のログを表示します。 `<app-name>` は、Web アプリの名前に置き換えます。 クエリの実行中にエラーが発生した場合は、10 分から 15 分後に再試行してください (Log Analytics がアプリケーションからログを受信し始めるまでの間に待ち時間が生じることがあります)。 

```kusto
let StartTime = ago(72h);
let EndTime = now();
AppServiceConsoleLogs_CL
| where TimeGenerated between (StartTime .. EndTime)
| where AppName_s =~ "<app-name>"
```

Kubernetes クラスターでホストされているすべてのアプリのアプリケーション ログは、Log Analytics ワークスペースで `AppServiceConsoleLogs_CL` という名前のカスタム ログ テーブルに記録されます。 

**Log_s** には特定の App Service のアプリケーション ログが含まれており、**AppName_s** には App Service のアプリ名が含まれています。 Log_s 列には、アプリケーション コードを使用して書き込むログに加えて、コンテナーの起動、シャットダウン、および関数アプリのログも含まれています。

ログ クエリの詳細については、[Kusto の概要](../azure-monitor/logs/get-started-queries.md)に関する記事を参照してください。

## <a name="optional-deploy-a-custom-container"></a>(省略可能) カスタム コンテナーをデプロイする

カスタム コンテナー アプリを作成するには、`--deployment-container-image-name` を指定して [az webapp create](/cli/azure/webapp#az_webapp_create) を実行します。 プライベート リポジトリの場合、`--docker-registry-server-user` と `--docker-registry-server-password` を追加します。

たとえば、次の操作を試してください。

```azurecli-interactive
az webapp create \
    --resource-group myResourceGroup \
    --name <app-name> \
    --custom-location $customLocationId \
    --deployment-container-image-name mcr.microsoft.com/appsvc/node:12-lts
```

<!-- `TODO: currently gets an error but the app is successfully created: "Error occurred in request., RetryError: HTTPSConnectionPool(host='management.azure.com', port=443): Max retries exceeded with url: /subscriptions/62f3ac8c-ca8d-407b-abd8-04c5496b2221/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/cephalin-arctest4/config/appsettings?api-version=2020-12-01 (Caused by ResponseError('too many 500 error responses',))"` -->

アプリの作成後にイメージを更新する方法については、「[カスタム コンテナーの Docker イメージを変更する](configure-custom-container.md?pivots=container-linux#change-the-docker-image-of-a-custom-container)」を参照してください

## <a name="next-steps"></a>次の手順

- [ASP.NET Core アプリを構成する](configure-language-dotnetcore.md?pivots=platform-linux)
- [Node.js アプリを構成する](configure-language-nodejs.md?pivots=platform-linux)
- [PHP アプリを構成する](configure-language-php.md?pivots=platform-linux)
- [Linux Python アプリを構成する](configure-language-python.md)
- [Java アプリを構成する](configure-language-java.md?pivots=platform-linux)
- [Linux Ruby アプリを構成する](configure-language-ruby.md)
- [カスタム コンテナーの構成](configure-custom-container.md?pivots=container-linux)
