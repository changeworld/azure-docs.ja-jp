---
title: Azure Functions をパッケージから実行する | Microsoft Docs
description: 関数アプリのプロジェクト ファイルが含まれたデプロイ パッケージ ファイルをマウントすることで、Azure Functions ランタイムで関数を実行します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
ms.service: functions
ms.devlang: multiple
ms.topic: conceptual
ms.workload: na
ms.date: 08/22/2018
ms.author: glenga
ms.openlocfilehash: 273b1a84b5ebe5f5751a05ef24e4721e7226bca6
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819492"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Azure Functions をパッケージ ファイルから実行する

> [!NOTE]
> この記事で説明されている機能は現在プレビュー段階です。Linux 上の Functions ではご利用いただけません。

Azure では、関数アプリのデプロイ パッケージ ファイルから、関数を直接実行できます。 他のオプションは、関数アプリの `d:\home\site\wwwroot` ディレクトリに関数プロジェクト ファイルを配置することです。

この記事では、パッケージから関数を実行するメリットについて説明します。 関数アプリでこの機能を有効にする方法も示します。

## <a name="benefits-of-running-from-a-package-file"></a>パッケージ ファイルから実行することのメリット
  
パッケージ ファイルからの実行には、さまざまなメリットがあります。

+ ファイル コピー ロック問題のリスクを軽減します。
+ 運用環境のアプリにデプロイできます (再起動が必要です)。
+ アプリで実行されるファイルを確定できます。
+ [Azure Resource Manager デプロイ](functions-infrastructure-as-code.md)のパフォーマンスが向上します。
+ JavaScript 関数のコールド スタート時間が短縮される可能性があります。

詳細については、こちらの[お知らせ](https://github.com/Azure/app-service-announcements/issues/84)をご覧ください。

## <a name="enabling-functions-to-run-from-a-package"></a>パッケージから関数を実行できるようにする

関数アプリをパッケージから実行できるようにするには、`WEBSITE_RUN_FROM_ZIP` 設定を関数アプリの設定に追加するだけです。 `WEBSITE_RUN_FROM_ZIP` 設定には次のいずれかの値を指定できます。

| 値  | 説明  |
|---------|---------|
|**`<url>`**  | 実行する特定のパッケージ ファイルの場所。 Blob Storage を使用する場合は、[Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) 付きのプライベート コンテナーを使用して、Functions ランタイムがパッケージにアクセスできるようにする必要があります。 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して、パッケージ ファイルを Blob Storage アカウントにアップロードします。         |
| **`1`**  | 関数アプリの `d:\home\data\SitePackages` フォルダー内のパッケージ ファイルから実行します。 このオプションでは、そのフォルダーに `packagename.txt` という名前のファイルも含まれている必要があります。 このファイルには、フォルダー内のパッケージ ファイルの空白なしの名前のみが含まれます。 |

Azure Blob torage でホストされている .zip ファイルから実行するように構成された関数アプリを次に示します。

![WEBSITE_RUN_FROM_ZIP アプリの設定](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> 現時点では、.zip パッケージ ファイルのみがサポートされています。

## <a name="integration-with-zip-deployment"></a>zip デプロイとの統合

[Zip デプロイ][Zip deployment for Azure Functions]は、関数アプリ プロジェクトを `wwwroot` ディレクトリに配置することを可能にする Azure App Service の機能です。 プロジェクトは、.zip デプロイ ファイルとしてパッケージ化されます。 同じ API を使用して、パッケージを `d:\home\data\SitePackages` フォルダーに配置できます。 `WEBSITE_RUN_FROM_ZIP` アプリ設定が値 `1` の場合、zip デプロイ API は、ファイルを `d:\home\site\wwwroot` 抽出する代わりに、パッケージを `d:\home\data\SitePackages` フォルダーにコピーします。 それは、`packagename.txt` ファイルも作成します。 再起動後に関数アプリがパッケージから実行され、`wwwroot` が読み取り専用になります。 zip デプロイの詳細については、[Azure Functions の zip デプロイ](deployment-zip-push.md)に関する記事を参照してください。

## <a name="adding-the-websiterunfromzip-setting"></a>WEBSITE_RUN_FROM_ZIP 設定の追加

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md