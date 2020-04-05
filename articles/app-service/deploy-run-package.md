---
title: ZIP パッケージからアプリを実行する
description: アトミック性を備えたアプリの ZIP パッケージをデプロイします。 ZIP デプロイ プロセス中のアプリの動作の予測可能性と信頼性を向上させます。
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920724"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>ZIP パッケージから Azure App Service のアプリを直接実行する

[Azure App Service](overview.md) では、デプロイ ZIP パッケージ ファイルから直接アプリを実行できます。 この記事では、アプリでこの機能を有効にする方法を示します。

App Service での他のすべてのデプロイ方法には共通点があります。ファイルは、アプリの *D:\home\site\wwwroot* にデプロイされます (または、Linux アプリの場合は */home/site/wwwroot*)。 同じディレクトリが実行時にアプリによって使用されるので、ファイル ロックの競合によってデプロイが失敗する可能性があります。また、一部のファイルがまだ更新されていないため、アプリが予期しない動作をする可能性があります。

これに対して、パッケージから直接実行すると、パッケージ内のファイルは *wwwroot* ディレクトリにコピーされません。 代わりに、ZIP パッケージ自体が読み取り専用の *wwwroot* ディレクトリとして直接マウントされます。 パッケージから直接実行すると、いくつかのメリットがあります。

- デプロイとランタイムの間でファイル ロックの競合がなくなります。
- 常に、完全にデプロイされたアプリのみが実行されることが保証されます。
- 運用環境のアプリにデプロイできます (再起動が必要です)。
- Azure Resource Manager デプロイのパフォーマンスが向上します。
- 特に大規模な npm パッケージのツリーの JavaScript 関数の場合、コールド スタート時間を減らすことができます。

> [!NOTE]
> 現時点では、ZIP パッケージ ファイルのみがサポートされています。

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>パッケージからの実行を有効にする

`WEBSITE_RUN_FROM_PACKAGE` アプリ設定により、パッケージから実行できるようになります。 それを設定するには、Azure CLI で次のコマンドを実行します。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` を使用すると、アプリに対してローカルなパッケージからアプリを実行できます。 [リモート パッケージ](#run-from-external-url-instead)から実行することもできます。

## <a name="run-the-package"></a>パッケージの実行

App Service でパッケージを実行する最も簡単な方法は、Azure CLI の [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) コマンドを使用することです。 次に例を示します。

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

`WEBSITE_RUN_FROM_PACKAGE` アプリ設定が設定されているため、このコマンドではパッケージの内容がアプリの *D:\home\site\wwwroot* ディレクトリに抽出されません。 代わりに、ZIP ファイルがそのまま *D:\home\data\SitePackages* にアップロードされ、同じディレクトリに *packagename* が作成されます。それには、実行時に読み込む ZIP パッケージの名前が含まれています。 ZIP パッケージを別の方法 ([FTP](deploy-ftp.md) など) でアップロードした場合は、*D:\home\data\SitePackages* ディレクトリと *packagename* ファイルを手動で作成する必要があります。

また、このコマンドではアプリが再起動されます。 `WEBSITE_RUN_FROM_PACKAGE` が設定されているため、App Service では、アップロードされたパッケージが読み取り専用の *wwwroot* ディレクトリとしてマウントされ、そのマウントされたディレクトリからアプリが直接実行されます。

## <a name="run-from-external-url-instead"></a>代わりに外部 URL から実行する

Azure Blob Storage などの外部 URL からパッケージを実行することもできます。 [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) を使用して、パッケージ ファイルを Blob Storage アカウントにアップロードします。 [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 付きのプライベート ストレージ コンテナーを使用して、App Service ランタイムがパッケージに安全にアクセスできるようにする必要があります。 

ファイルを Blob Storage にアップロードし、ファイルの SAS URL を取得したら、`WEBSITE_RUN_FROM_PACKAGE` アプリ設定を URL に設定します。 次の例では、Azure CLI を使用してそれを行っています。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

更新されたパッケージを同じ名前で Blob Storage に発行する場合は、更新されたパッケージが App Service に読み込まれるように、アプリを再起動する必要があります。

## <a name="troubleshooting"></a>トラブルシューティング

- パッケージから直接実行すると、`wwwroot` は読み取り専用になります。 アプリがこのディレクトリにファイルを書き込もうとすると、エラーが発生します。
- TAR および GZIP 形式はサポートされていません。
- この機能は、[ローカル キャッシュ](overview-local-cache.md)と互換性がありません。
- コールドスタートのパフォーマンスを向上するには、ローカルの Zip オプション (`WEBSITE_RUN_FROM_PACKAGE` = 1) を使用します。

## <a name="more-resources"></a>その他のリソース

- [Azure App Service への継続的デプロイ](deploy-continuous-deployment.md)
- [ZIP または WAR ファイルによるコードのデプロイ](deploy-zip.md)
