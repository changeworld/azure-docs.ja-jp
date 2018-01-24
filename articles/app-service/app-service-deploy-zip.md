---
title: "ZIP ファイルを使用した Azure App Service へのアプリのデプロイ | Microsoft Docs"
description: "ZIP ファイルを使用して Azure App Service にアプリをデプロイする方法について説明します。"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: cephalin;sisirap
ms.openlocfilehash: a0e4df0ef0a1c873f1efcac1d8dbfe3cada18218
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>ZIP ファイルを使用した Azure App Service へのアプリのデプロイ

この記事では、ZIP ファイルを使用して Web アプリを [Azure App Service](app-service-web-overview.md) にデプロイする方法について説明します。 

この ZIP ファイル デプロイでは、Kudu サービスを使用することで、継続的な統合ベース デプロイを効率化できます。 Kudu では、ZIP ファイルでのデプロイについて次の機能がサポートされています。 

- 以前のデプロイから残っているファイルの削除。
- 既定のビルド プロセスを有効にするオプション (パッケージの復元を含む)。
- [デプロイのカスタマイズ](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) (デプロイ スクリプトの実行など)。  
- デプロイ ログ。 

詳しくは、[Kudu ドキュメント](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)をご覧ください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のことが条件となります。

* [App Service アプリを作成する](/azure/app-service/)か、別のチュートリアルで作成したアプリを使用します。

## <a name="create-a-project-zip-file"></a>プロジェクトの ZIP ファイルを作成する

>[!NOTE]
> ファイルを ZIP ファイルでダウンロードした場合は、まずファイルを抽出します。 たとえば、GitHub から ZIP ファイルをダウンロードした場合、そのファイルをそのままデプロイすることはできません。 GitHub では、入れ子になったディレクトリが追加されますが、これらは App Service と連携しません。 
>

ローカル ターミナル ウィンドウで、アプリ プロジェクトのルート ディレクトリに移動します。 

このディレクトリには、Web アプリへの入力ファイルを含める必要があります (_index.html_、 _index.php_、_app.js_ など)。 パッケージ管理ファイルを含めることもできます (_project.json_、_composer.json_、_package.json_、_bower.json_、_requirements.txt_ など)。

プロジェクト内のすべての ZIP アーカイブを作成します。 次のコマンドには、お使いの端末の既定のツールを使用します。

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>Cloud Shell に ZIP ファイルをアップロードする

ローカル ターミナルから Azure CLI を実行する場合は、この手順をスキップしてください。

次の手順に従って、Cloud Shell に ZIP ファイルをアップロードします。 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

詳細については、「[Azure Cloud Shell でファイルを永続化する](../cloud-shell/persisting-shell-storage.md)」をご覧ください。

## <a name="deploy-zip-file"></a>ZIP ファイルのデプロイ

[az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) コマンドを使用して、アップロードされた ZIP ファイルを Web アプリにデプロイします。 Cloud Shell を使用しない場合は、Azure CLI のバージョンが 2.0.21 以降であることを確認してください。 使用しているバージョンを確認するには、ローカル ターミナル ウィンドウで `az --version` コマンドを実行します。 

次に示すのは、アップロードした ZIP ファイルをデプロイするコマンドの例です。 Azure CLI のローカル インストールを使用する場合は、`--src` にローカル ZIP ファイルのパスを指定します。   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

このコマンドは、ファイルとディレクトリを ZIP ファイルから既定の App Service アプリケーション フォルダー (`\home\site\wwwroot`) にデプロイし、アプリを再起動します。 任意の追加のカスタム ビルド プロセスが構成されている場合、そのプロセスも実行されます。 詳しくは、[Kudu ドキュメント](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)をご覧ください。

このアプリに対するデプロイの一覧を表示するには、REST API を使用する必要があります (次のセクションを参照)。 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>次の手順

高度なデプロイ シナリオの詳細については、[Git を使用した Azure へのデプロイ](app-service-deploy-local-git.md)に関するページをご覧ください。 Azure への Git ベース デプロイでは、バージョン管理、パッケージの復元、MSBuild などが可能です。

## <a name="more-resources"></a>その他のリソース

* [Kudu: Deploying from a zip file (Kudu: zip ファイルからのデプロイ)](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service のデプロイ資格情報](app-service-deploy-ftp.md)
