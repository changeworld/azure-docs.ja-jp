---
title: 独自の関数を Azure Static Web Apps で使用する
description: 既存の Azure Functions アプリを Azure Static Web Apps サイトで使用します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/07/2021
ms.author: cshoe
ms.openlocfilehash: 7925bd70488106943f0030e6b26534938fbc7d36
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570894"
---
# <a name="bring-your-own-functions-to-azure-static-web-apps"></a>独自の関数を Azure Static Web Apps で使用する

Azure Static Web Apps API は、マネージド関数と独自の関数の使用という 2 つの構成でサポートされています。 この 2 つの構成の詳細については、[API リファレンス](apis.md)を参照してください。

この記事では、既存の Azure Functions アプリを Azure Static Web Apps リソースにリンクする方法について説明します。

> [!NOTE]
> 独自の関数の使用は、Azure Static Web Apps の Standard プランでのみ使用できます。

## <a name="example"></a>例

次の場所を経由してエンドポイントを公開する既存の Azure Functions アプリについて検討します。

```url
https://my-functions-app.azurewebsites.net/api/getProducts
```

リンクされると、この URL の例に示すように、静的 Web アプリから `api` パスを介してその同じエンドポイントにアクセスできます。

```url
https://red-sea-123.azurestaticapps.net/api/getProducts
```

 どちらのエンドポイント URL も同じ関数を指します。

## <a name="link-an-existing-azure-functions-app"></a>既存の Azure Functions アプリをリンクする

既存の Functions アプリを関連付ける前に、まず静的 Web アプリの構成に合わせて調整する必要があります。

1. [ワークフロー構成](./build-configuration.md)ファイルで、`api_location` の値を空の文字列 (`""`) に設定します。

1. [Azure portal](https://portal.azure.com) で Static Web Apps インスタンスを開きます。

1. _[設定]_ メニューで **[Functions]** を選択します。

1. _[環境]_ ドロップダウンから **[Production]\(実稼働\)** を選択します。

1. _[関数のソース]_ ラベルの横にある **[関数アプリへのリンク]** を選択します。

1. _[サブスクリプション]_ ドロップダウンで、ご自分の Azure サブスクリプション名を選択します。

1. _[関数アプリ]_ ドロップダウンから、静的 Web アプリにリンクする既存の Functions アプリの名前を選択します。

1. **[リンク]** ボタンを選択します。

    :::image type="content" source="media/functions-bring-your-own/azure-static-web-apps-link-existing-functions-app.png" alt-text="既存の Functions アプリをリンクする":::

> [!IMPORTANT]
> 既存の Functions アプリケーションをリンクする前に、[ワークフロー構成](./build-configuration.md)ファイルで `api_location` の値を空の文字列 (`""`) に必ず設定してください。

## <a name="deployment"></a>デプロイ

Azure Functions アプリの[デプロイ ワークフロー](../azure-functions/functions-deployment-technologies.md)の設定は、ご自身の責任で行う必要があります。

## <a name="security-constraints"></a>セキュリティの制約

- **認証と認可:** 既存の Functions アプリで認証と認可のポリシーがまだ設定されていない場合、静的 Web アプリは API への排他的アクセス権限を持ちます。 Functions アプリを他のアプリケーションからアクセス可能にするには、別の ID プロバイダーを追加するか、認証されていないアクセスを許可するようセキュリティ設定を変更します。

  > [!NOTE]
  > リンクされた Functions アプリで認証と認可を有効にする場合は、Azure App Service 認証および認可プロバイダー バージョン 2 を使用する必要があります。

- **必要なパブリック アクセシビリティ:** 既存の Functions アプリでは、次のセキュリティ構成を適用しないようにする必要があります。
  - Functions アプリの IP アドレスの制限。
  - プライベート リンクまたはサービス エンドポイントを介したトラフィックの制限。

- **関数アクセス キー:** 関数に [アクセス キー](../azure-functions/security-concepts.md#function-access-keys)が必要な場合は、静的アプリから API への呼び出しをキーに指定する必要があります。

## <a name="restrictions"></a>制限

- 1 つの静的 Web アプリで使用できる Azure Functions アプリは 1 つのみです。
- `api_location`ワークフロー構成[の ](./build-configuration.md) 値は、空の文字列に設定する必要があります。
- Static Web Apps 運用環境でのみサポートされます。
- Azure Functions アプリはさまざまなトリガーに応答できますが、静的 Web アプリは HTTP エンドポイント経由でのみ関数にアクセスできます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [API を追加する](add-api.md)
