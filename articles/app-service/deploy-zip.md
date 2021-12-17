---
title: App Service にファイルをデプロイする
description: さまざまなアプリ パッケージや個々のライブラリ、静的ファイル、スタートアップ スクリプトを Azure App Service にデプロイする方法について説明します
ms.topic: article
ms.date: 08/13/2021
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: be58de78ba82c3c5e99475b00649e2a7ebfdf5e4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216941"
---
# <a name="deploy-files-to-app-service"></a>App Service にファイルをデプロイする

この記事では、コードを ZIP、WAR、JAR、EAR のいずれかのパッケージとして [Azure App Service](overview.md) にデプロイする方法について説明します。 また、アプリケーション パッケージとは別に、個々のファイルを App Service にデプロイする方法についても説明します。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、[App Service アプリを作成する](./index.yml)か、別のチュートリアルで作成したアプリを使用します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="deploy-a-zip-package"></a>ZIP パッケージをデプロイする

ZIP パッケージをデプロイすると、その内容が、App Service によってアプリの既定のパス (Windows の場合は `D:\home\site\wwwroot`、Linux の場合は `/home/site/wwwroot`) に展開されます。

この ZIP パッケージ デプロイでは、Kudu サービスを使用することで、継続的インテグレーションベースのデプロイを効率化できます。 Kudu では、ZIP パッケージでのデプロイについて次の機能がサポートされています。 

- 以前のデプロイから残っているファイルの削除。
- 既定のビルド プロセスを有効にするオプション (パッケージの復元を含む)。
- デプロイのカスタマイズ (デプロイ スクリプトの実行など)。  
- デプロイ ログ。 
- パッケージ サイズの上限は 2,048 MB です。

詳しくは、[Kudu ドキュメント](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)をご覧ください。

> [!NOTE]
> ZIP パッケージ内のファイルがコピーされるのは、既にデプロイされているファイルとタイムスタンプが一致しない場合のみです。 出力をキャッシュするビルド プロセスを使用して zip を生成すると、デプロイが高速になる場合があります。 詳細については、「[zip ファイルまたは URL からのデプロイ](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)」を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

ZIP パッケージを Web アプリにデプロイするには、[az webapp deploy](/cli/azure/webapp#az_webapp_deploy) コマンドを使用します。 この CLI コマンドは、[Kudu publish API](#kudu-publish-api-reference) を使用してファイルをデプロイするもので、完全にカスタマイズすることができます。

次に示したのは、ZIP パッケージを自分のサイトにプッシュする例です。 `--src-path` に、ローカル ZIP パッケージのパスを指定してください。

```azurecli-interactive
az webapp deploy --resource-group <group-name> --name <app-name> --src-path <zip-package-path>
```

このコマンドを実行すると、ZIP パッケージのデプロイ後にアプリが再起動されます。 

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

次の例では、サイトでどの Azure ストレージ アカウントの URL から ZIP をプルするかを `--src-url` パラメーターで指定します。

```azurecli-interactive
az webapp deploy --resource-group <grou-name> --name <app-name> --src-url "https://storagesample.blob.core.windows.net/sample-container/myapp.zip?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

次の例では、[Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) を使用して ZIP パッケージをアップロードします。 プレースホルダー `<group-name>`、`<app-name>`、`<zip-package-path>` を置き換えます。

```powershell
Publish-AzWebApp -ResourceGroupName Default-Web-WestUS -Name MyApp -ArchivePath <zip-package-path> 
```

# <a name="kudu-api"></a>[Kudu API](#tab/api)

次の例では、cURL ツールを使用して ZIP パッケージをデプロイします。 プレースホルダー `<username>`、`<zip-package-path>`、`<app-name>` を置き換えます。 cURL によって要求されたら、[デプロイ パスワード](deploy-configure-credentials.md)を入力します。

```bash
curl -X POST -u <username> --data-binary @"<zip-package-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=zip
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

次の例では、Web アプリでどの Azure ストレージ アカウントの URL から ZIP をプルするかを `packageUri` パラメーターで指定します。

```bash
curl -X POST -u <username> https://<app-name>.scm.azurewebsites.net/api/publish -d '{"packageUri": "https://storagesample.blob.core.windows.net/sample-container/myapp.zip?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3"}'
```

# <a name="kudu-ui"></a>[Kudu UI](#tab/kudu-ui)

ブラウザーで `https://<app_name>.scm.azurewebsites.net/ZipDeployUI` にアクセスします。

「[プロジェクトの ZIP パッケージを作成する](#create-a-project-zip-package)」で作成した ZIP パッケージを、Web ページ上のエクスプローラー領域にドラッグしてアップロードします。

デプロイの進行中、右上隅のアイコンにその進行状況がパーセンテージで表示されます。 また、ページのエクスプローラー領域の下に、操作に関する詳細なメッセージも表示されます。 完了すると、最後のデプロイ メッセージに "`Deployment successful`" と表示されます。

現在、上記のエンドポイントは Linux App Services では機能しません。 代わりに、FTP または [ZIP デプロイ API](./faq-app-service-linux.yml) の使用を検討してください。

-----

## <a name="enable-build-automation-for-zip-deploy"></a>ZIP デプロイのビルド自動化を有効にする

デプロイ エンジンは、ZIP パッケージがそのまま実行できる状態であることと、ビルド自動化は実行されないことを既定では想定しています。 [Git デプロイ](deploy-local-git.md)の場合と同じビルド自動化を有効にするには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行することで、`SCM_DO_BUILD_DURING_DEPLOYMENT` アプリ設定を設定します。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

詳しくは、[Kudu ドキュメント](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)をご覧ください。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="deploy-warjarear-packages"></a>WAR、JAR、EAR パッケージをデプロイする

Azure CLI、PowerShell、または Kudu publish API を使用して、[WAR](https://wikipedia.org/wiki/WAR_(file_format))、[JAR](https://wikipedia.org/wiki/JAR_(file_format))、[EAR](https://wikipedia.org/wiki/EAR_(file_format)) のいずれかのパッケージを App Service にデプロイし、Java Web アプリを実行することができます。

パッケージは、デプロイ プロセスによって共有ファイル ドライブに正しく配置されます ([Kudu publish API のリファレンス](#kudu-publish-api-reference)を参照)。 そのため、[FTP](deploy-ftp.md) や WebDeploy を使用して WAR、JAR、EAR の各パッケージをデプロイすることは推奨されません。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

WAR パッケージを Tomcat または JBoss EAP にデプロイするには、[az webapp deploy](/cli/azure/webapp#az_webapp_deploy) コマンドを使用します。 `--src-path` に、ローカル Java パッケージのパスを指定してください。

```azurecli-interactive
az webapp deploy --resource-group <group-name> --name <app-name> --src-path ./<package-name>.war
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

次の例では、Web アプリでどの Azure ストレージ アカウントの URL から ZIP をプルするかを `--src-url` パラメーターで指定します。

```azurecli-interactive
az webapp deploy --resource-group <grou-name> --name <app-name> --src-url "https://storagesample.blob.core.windows.net/sample-container/myapp.war?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3
```

この CLI コマンドは、[Kudu publish API](#kudu-publish-api-reference) を使用してパッケージをデプロイするもので、完全にカスタマイズすることができます。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

次の例では、[Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) を使用して .war ファイルをアップロードします。 `<group-name>`、`<app-name>`、`<package-path>` の各プレースホルダーを置き換えてください (Azure PowerShell でサポートされるのは WAR ファイルと JAR ファイルのみです)。

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <package-path>
```

# <a name="kudu-api"></a>[Kudu API](#tab/api)

次の例は cURL ツールを使用して .war、.jar、.ear のいずれかのファイルを展開します。 `<username>`、`<file-path>`、`<app-name>`、`<package-type>` (適宜、`war`、`jar`、`ear` のいずれか) の各プレースホルダーを置き換えてください。 cURL によって要求されたら、[デプロイ パスワード](deploy-configure-credentials.md)を入力します。

```bash
curl -X POST -u <username> --data-binary @"<file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=<package-type>
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

次の例では、Web アプリでどの Azure ストレージ アカウントの URL から WAR をプルするかを `packageUri` パラメーターで指定します。 WAR ファイルは、JAR ファイルや EAR ファイルでもかまいません。

```bash
curl -X POST -u <username> https://<app-name>.scm.azurewebsites.net/api/publish -d '{"packageUri": "https://storagesample.blob.core.windows.net/sample-container/myapp.war?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3"}'
```

詳細については、[Kudu publish API リファレンス](#kudu-publish-api-reference)を参照してください

# <a name="kudu-ui"></a>[Kudu UI](#tab/kudu-ui)

Kudu UI では、JAR、WAR、EAR の各アプリケーションのデプロイはサポートされません。 他のいずれかの方法を使用してください。

-----

## <a name="deploy-individual-files"></a>個々のファイルをデプロイする

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

スタートアップ スクリプト、ライブラリ、静的ファイルを Web アプリにデプロイするには、`--type` パラメーターを指定して [az webapp deploy](/cli/azure/webapp#az_webapp_deploy) コマンドを使用します。

この方法でスタートアップ スクリプトをデプロイした場合、App Service が自動的にそのスクリプトを使用してアプリを起動します。

この CLI コマンドは、[Kudu publish API](#kudu-publish-api-reference) を使用してファイルをデプロイするもので、完全にカスタマイズすることができます。

### <a name="deploy-a-startup-script"></a>スタートアップ スクリプトをデプロイする

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path scripts/startup.sh --type=startup
```

### <a name="deploy-a-library-file"></a>ライブラリ ファイルをデプロイする

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path driver.jar --type=lib
```

### <a name="deploy-a-static-file"></a>静的ファイルをデプロイする

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path config.json --type=static
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

サポートされていません。 「Azure CLI」または「Kudu API」を参照してください。

# <a name="kudu-api"></a>[Kudu API](#tab/api)

### <a name="deploy-a-startup-script"></a>スタートアップ スクリプトをデプロイする

次の例では、cURL ツールを使用してアプリケーションのスタートアップ ファイルをデプロイします。`<username>`、`<startup-file-path>`、`<app-name>` の各プレースホルダーを置き換えてください。 cURL によって要求されたら、[デプロイ パスワード](deploy-configure-credentials.md)を入力します。

```bash
curl -X POST -u <username> --data-binary @"<startup-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=startup
```

### <a name="deploy-a-library-file"></a>ライブラリ ファイルをデプロイする

次の例では、cURL ツールを使用してアプリケーションのライブラリ ファイルをデプロイします。 プレースホルダー `<username>`、`<lib-file-path>`、`<app-name>` を置き換えます。 cURL によって要求されたら、[デプロイ パスワード](deploy-configure-credentials.md)を入力します。

```bash
curl -X POST -u <username> --data-binary @"<lib-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=lib&path="/home/site/deployments/tools/my-lib.jar"
```

### <a name="deploy-a-static-file"></a>静的ファイルをデプロイする

次の例では、cURL ツールを使用してアプリケーションの構成ファイルをデプロイします。 プレースホルダー `<username>`、`<config-file-path>`、`<app-name>` を置き換えます。 cURL によって要求されたら、[デプロイ パスワード](deploy-configure-credentials.md)を入力します。

```bash
curl -X POST -u <username> --data-binary @"<config-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=static&path="/home/site/deployments/tools/my-config.json"
```

# <a name="kudu-ui"></a>[Kudu UI](#tab/kudu-ui)

Kudu UI では、個々のファイルのデプロイはサポートされません。 Azure CLI または Kudu REST API を使用してください。

-----

## <a name="kudu-publish-api-reference"></a>Kudu publish API リファレンス

`publish` Kudu API では、CLI コマンドと同じパラメーターを URL のクエリ パラメーターとして指定できます。 Kudu API に対して認証を行うには、アプリの[デプロイ資格情報](deploy-configure-credentials.md#userscope)による基本認証を使用できます。

以下の表に、使用可能なクエリ パラメーターとパラメーターに指定できる値、および説明を示します。

| キー | 使用できる値 | 説明 | 必須 | 型  |
|-|-|-|-|-|
| `type` | `war`\|`jar`\|`ear`\|`lib`\|`startup`\|`static`\|`zip` | デプロイする成果物の種類。これによって既定のターゲット パスが設定され、デプロイの処理方法が Web アプリに伝えられます。 <br/> - `type=zip`: ZIP パッケージの内容を `/home/site/wwwroot` に解凍することでパッケージをデプロイします。 `path` パラメーターは省略可能です。 <br/> - `type=war`: WAR パッケージをデプロイします。 既定では、WAR パッケージが `/home/site/wwwroot/app.war` にデプロイされます。 ターゲット パスは、`path` で指定できます。 <br/> - `type=jar`: JAR パッケージを `/home/site/wwwroot/app.jar` にデプロイします。 `path` パラメーターは無視されます <br/> - `type=ear`: EAR パッケージを `/home/site/wwwroot/app.ear` にデプロイします。 `path` パラメーターは無視されます <br/> - `type=lib`: JAR ライブラリ ファイルをデプロイします。 既定では、ファイルが `/home/site/libs` にデプロイされます。 ターゲット パスは、`path` で指定できます。 <br/> - `type=static`: 静的ファイル (スクリプトなど) をデプロイします。 既定では、ファイルが `/home/site/scripts` にデプロイされます。 ターゲット パスは、`path` で指定できます。 <br/> - `type=startup`: App Service によってアプリのスタートアップ スクリプトとして自動的に使用されるスクリプトをデプロイします。 既定では、`D:\home\site\scripts\<name-of-source>` (Windows の場合) および `home/site/wwwroot/startup.sh` (Linux の場合) にスクリプトがデプロイされます。 ターゲット パスは、`path` で指定できます。 | はい | String |
| `restart` | `true`\|`false` | 既定では、デプロイ操作の後に API によってアプリが再起動されます (`restart=true`)。 複数の成果物をデプロイする場合は、最後のデプロイ以外はいずれも `restart=false` を設定して再起動を禁止します。 | いいえ | Boolean |
| `clean` | `true`\|`false` | 成果物をデプロイする前に、ターゲット デプロイをクリーン (削除) するかどうかを指定します。 | いいえ | Boolean |
| `ignorestack` | `true`\|`false` | publish API は `WEBSITE_STACK` 環境変数を使用し、実際のサイトの言語スタックに応じて安全な既定値を選択します。 このパラメーターを `false` に設定すると、言語固有の既定値が無効になります。 | いいえ | Boolean |
| `path` | `"<absolute-path>"` | 成果物のデプロイ先の絶対パス。 たとえば、`"/home/site/deployments/tools/driver.jar"`、`"/home/site/scripts/helper.sh"` のようになります。 | いいえ | String |

## <a name="next-steps"></a>次のステップ

高度なデプロイ シナリオの詳細については、[Git を使用した Azure へのデプロイ](deploy-local-git.md)に関するページをご覧ください。 Azure への Git ベース デプロイでは、バージョン管理、パッケージの復元、MSBuild などが可能です。

## <a name="more-resources"></a>その他のリソース

* [Kudu:Deploying from a zip file (Kudu: zip ファイルからのデプロイ)](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service のデプロイ資格情報](deploy-ftp.md)
* [環境変数とアプリ設定のリファレンス](reference-app-settings.md)
