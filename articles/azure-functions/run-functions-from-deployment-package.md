---
title: Azure Functions をパッケージから実行する
description: 関数アプリのプロジェクト ファイルが含まれたデプロイ パッケージ ファイルをマウントすることで、Azure Functions ランタイムで関数を実行します。
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: c2c0797f256cfd302f4b8a5a80300ef4469355e4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256991"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Azure Functions をパッケージ ファイルから実行する

Azure では、関数アプリのデプロイ パッケージ ファイルから、関数を直接実行できます。 他のオプションは、関数アプリの `d:\home\site\wwwroot` ディレクトリ内のファイルをデプロイすることです。

この記事では、パッケージから関数を実行するメリットについて説明します。 関数アプリでこの機能を有効にする方法も示します。

## <a name="benefits-of-running-from-a-package-file"></a>パッケージ ファイルから実行することのメリット
  
パッケージ ファイルからの実行には、さまざまなメリットがあります。

+ ファイル コピー ロック問題のリスクを軽減します。
+ 運用環境のアプリにデプロイできます (再起動が必要です)。
+ アプリで実行されるファイルを確定できます。
+ [Azure Resource Manager デプロイ](functions-infrastructure-as-code.md)のパフォーマンスが向上します。
+ 特に大規模な npm パッケージのツリーの JavaScript 関数の場合、コールド スタート時間を減らすことができます。

詳細については、こちらの[お知らせ](https://github.com/Azure/app-service-announcements/issues/84)をご覧ください。

## <a name="enabling-functions-to-run-from-a-package"></a>パッケージから関数を実行できるようにする

関数アプリをパッケージから実行できるようにするには、`WEBSITE_RUN_FROM_PACKAGE` 設定を関数アプリの設定に追加するだけです。 `WEBSITE_RUN_FROM_PACKAGE` 設定には次のいずれかの値を指定できます。

| 値  | 説明  |
|---------|---------|
| **`1`**  | Windows 上で実行される関数アプリでは、推奨されています。 関数アプリの `d:\home\data\SitePackages` フォルダー内のパッケージ ファイルから実行します。 [Zip 配置を使って配置](#integration-with-zip-deployment)しない場合、このオプションでは、フォルダー内に `packagename.txt` という名前のファイルも含む必要があります。 このファイルには、フォルダー内のパッケージ ファイルの空白なしの名前のみが含まれます。 |
|**`<URL>`**  | 実行する特定のパッケージ ファイルの場所。 また、URL を指定するとき、更新済みのパッケージを発行した後に[トリガーを同期](functions-deployment-technologies.md#trigger-syncing)する必要もあります。 <br/>Blob ストレージを使用している場合は、通常、パブリック Blob を使うべきではありません。 代わりに、[Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) があるプライベート コンテナーを使用するか、[マネージド ID を使用して](#fetch-a-package-from-azure-blob-storage-using-a-managed-identity)パッケージにアクセスするための Functions ランタイムを有効にします。 [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) を使用して、パッケージ ファイルを Blob Storage アカウントにアップロードします。 |

> [!CAUTION]
> Windows 上で関数アプリを実行する場合、外部 URL のオプションではコールド スタートのパフォーマンスが悪化します。 関数アプリを Windows にデプロイするときは、`WEBSITE_RUN_FROM_PACKAGE` を `1` に設定して、zip 配置によって発行する必要があります。

Azure Blob torage でホストされている .zip ファイルから実行するように構成された関数アプリを次に示します。

![WEBSITE_RUN_FROM_ZIP アプリの設定](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> 現時点では、.zip パッケージ ファイルのみがサポートされています。

### <a name="fetch-a-package-from-azure-blob-storage-using-a-managed-identity"></a>マネージド ID を使用して Azure Blob Storage からパッケージを取得する

[!INCLUDE [Run from package via Identity](../../includes/app-service-run-from-package-via-identity.md)]

## <a name="integration-with-zip-deployment"></a>zip デプロイとの統合

[Zip デプロイ][Zip deployment for Azure Functions]は、関数アプリ プロジェクトを `wwwroot` ディレクトリに配置することを可能にする Azure App Service の機能です。 プロジェクトは、.zip デプロイ ファイルとしてパッケージ化されます。 同じ API を使用して、パッケージを `d:\home\data\SitePackages` フォルダーに配置できます。 `WEBSITE_RUN_FROM_PACKAGE` アプリ設定が値 `1` の場合、zip デプロイ API は、ファイルを `d:\home\site\wwwroot` 抽出する代わりに、パッケージを `d:\home\data\SitePackages` フォルダーにコピーします。 それは、`packagename.txt` ファイルも作成します。 再起動後、パッケージは読み取り専用のファイルシステムとして `wwwroot` にマウントされます。 zip デプロイの詳細については、[Azure Functions の zip デプロイ](deployment-zip-push.md)に関する記事を参照してください。

> [!NOTE]
> デプロイが行われると、関数アプリの再起動がトリガーされます。 再起動する前に、既存のすべての関数の実行に完了またはタイムアウトが許可されます。詳細については、「[デプロイ動作](functions-deployment-technologies.md#deployment-behaviors)」を参照してください。

## <a name="adding-the-website_run_from_package-setting"></a>WEBSITE_RUN_FROM_PACKAGE 設定の追加

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="example-workflow-for-manually-uploading-a-package-hosted-in-azure-storage"></a>Azure Storage でホストされているパッケージの手動アップロードに関するワークフローの例

URL オプションを使用するときに ZIP 形式のパッケージをデプロイするには、圧縮された .zip パッケージを作成し、デプロイ先にアップロードする必要があります。 この例では、Blob Storage コンテナーを使用します。 

1. 適当なユーティリティを使って、プロジェクトの .zip パッケージを作成します。

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウント名を検索するか、ストレージ アカウントで参照します。
 
1. ストレージ アカウントで、 **[データ ストレージ]** の下の **[コンテナー]** を選びます。

1. **[+ コンテナー]** を選んで、アカウントに新しい Blob Storage コンテナーを作成します。

1. **[新しいコンテナー]** ページで、 **[名前]** を指定し ("deployments" など)、 **[パブリック アクセス レベル]** が **[プライベート]** になっていることを確認して、 **[作成]** を選びます。

1. 作成したコンテナーを選んで、 **[アップロード]** を選び、プロジェクトで作成した .zip ファイルの場所を参照して、 **[アップロード]** を選びます。

1. アップロードが完了したら、アップロードした BLOB ファイルを選んで、URL をコピーします。 [ID を使用](#fetch-a-package-from-azure-blob-storage-using-a-managed-identity)していない場合は、SAS URL の生成が必要な場合があります

1. 関数アプリを検索するか、 **[関数アプリ]** ページで参照します。 

1. 関数アプリの **[設定]** で、 **[構成]** を選びます。

1. **[アプリケーションの設定]** タブで、 **[新しいアプリケーション設定]** を選びます

1. **[名前]** の値 `WEBSITE_RUN_FROM_PACKAGE` を入力し、 **[値]** として Blob Storage 内のパッケージの URL を貼り付けます。

1. **[OK]** を選択します。 次に、 **[保存]**  >  **[続行]** を選んで設定を保存し、アプリを再起動します。

これで、Azure で関数を実行し、デプロイ パッケージの .zip ファイルを使用したデプロイが成功したことを検証できます。

## <a name="troubleshooting"></a>トラブルシューティング

- パッケージから実行すると `wwwroot` が読み取り専用になるので、このディレクトリにファイルを書き込むときにエラーを受け取ります。
- tar および gzip 形式はサポートされていません。
- ZIP ファイルは、最大 1 GB にすることができます。
- この機能はローカル キャッシュでは構成されません。
- コールドスタートのパフォーマンスを向上するには、ローカルの Zip オプション (`WEBSITE_RUN_FROM_PACKAGE` = 1) を使用します。
- パッケージからの実行は、デプロイ カスタマイズ オプション (`SCM_DO_BUILD_DURING_DEPLOYMENT=true`) と互換性がありません。ビルド ステップは、デプロイの間に無視されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
