---
title: 開発者ポータルをセルフホストするための代替方法
titleSuffix: Azure API Management
description: Azure API Management で開発者ポータルをセルフホストするときに使用できる代替アプローチについて説明します。
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: fb8514423bf4efd42373ad2f111aa3a96c5c0969
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582899"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>開発者ポータルをセルフホストするための代替アプローチ

[開発者ポータルをセルフホスト](developer-portal-self-host.md)するときに、次のようないくつかの代替アプローチを検討することができます。

* デザイナーとパブリッシャーの運用環境用のビルドを使用します。

* Azure Function App を使用してポータルを発行します。

* ページの読み込み時間を短縮するために、ポータルのファイルを Content Delivery Network (CDN) でフロント処理します。

この記事では、これらの各アプローチについて説明します。 

まだ行っていない場合は、開発者ポータルの最新リリース用に[ローカル環境](developer-portal-self-host.md#step-1-set-up-local-environment)を設定します。

## <a name="build-for-production"></a>運用環境用のビルド

コラボレーションを目的としてポータルの開発環境をオンラインでホストする場合は、デザイナーとパブリッシャーの運用環境用のビルドを使用します。 運用環境用のビルドによってファイルがバンドルされ、ソース マップなどが除外されます。

次のコマンドを実行して、`./dist/designer` ディレクトリ内にバンドルを作成します。

```sh
npm run build-designer
```

結果はシングル ページ アプリケーションであるため、Azure Blob Storage の静的な Web サイトなどの静的な Web ホストにデプロイすることもできます。

同様に、コンパイル済みの最適化されたパブリッシャーを `./dist/publisher` フォルダーに配置します。

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>Function App を使用してポータルを発行する

発行手順をローカルで実行する代わりに、クラウドで実行します。

Azure Function App で発行を実装するには、次の前提条件を満たす必要があります。

- [Azure 関数を作成します](../azure-functions/functions-get-started.md)。 関数は JavaScript 言語関数である必要があります。
- Azure Functions Core Tools をインストールします。
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>手順 1: 出力ストレージを構成する

ローカル フォルダーではなく、ホスティング Web サイト (出力ストレージの "$web" コンテナー) にコンテンツを直接アップロードします。 `./src/config.publish.json` ファイルでこの変更を構成します。

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>手順 2: Function App をビルドしてデプロイする

`./examples` フォルダーには、HTTP トリガー関数のサンプルがあります。 これをビルドして `./dist/function` に配置するには、次のコマンドを実行します。

```sh
npm run build-function
```

次に、Azure CLI にサインインしてそれをデプロイします。

```azurecli
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

それがデプロイされると、HTTP 呼び出しを使用して呼び出すことができます。

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>ホスティングと CDN

[開発者ポータルのセルフホスト](developer-portal-self-host.md)では、Web サイトをホストするために Azure ストレージ アカウントを使用することをお勧めしました。 ただし、ホスティング プロバイダーのサービスを含め、どのようなソリューションでもファイルを発行することができます。

また、ページの読み込み時間を短縮するために、ファイルを Content Delivery Network (CDN) でフロント処理することもできます。 [Azure CDN](https://azure.microsoft.com/services/cdn/) を使用することをお勧めします。

## <a name="next-steps"></a>次の手順

開発者ポータルの詳細については、次を参照してください。

- [Azure API Management 開発者ポータルの概要](api-management-howto-developer-portal.md)
