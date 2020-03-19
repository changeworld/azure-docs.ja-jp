---
title: Web マップ コントロールの概要 | Microsoft Azure Maps
description: Microsoft Azure マップのマップコントロールクライアント側 JavaScript ライブラリを使用して、Web アプリケーションまたはモバイルアプリケーションにマップと埋め込み Azure Maps 機能をレンダリングする方法について説明します。
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 535707191557142054e493d0216b713312f53953
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368653"
---
# <a name="use-the-azure-maps-map-control"></a>Azure Maps のマップ コントロールを使用する

マップ コントロール クライアント側 JavaScript ライブラリを使用すると、マップと埋め込まれた Azure Maps の機能を、Web アプリケーションまたはモバイル アプリケーションにレンダリングできます。

## <a name="create-a-new-map-in-a-web-page"></a>Web ページに新しいマップを作成する

マップ コントロール クライアント側 JavaScript ライブラリを使って、Web ページにマップを埋め込むことができます。

1. 新しい HTML ファイルを作成します。

2. Azure Maps Web SDK に読み込みます。 次の 2 つのオプションのいずれかを選ぶことができます。

    * HTML ファイルの `<head>` 要素内の JavaScript とスタイルシートに参照を追加して、グローバルにホストされる CDN バージョンの Azure Maps Web SDK を使用します。

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) NPM パッケージを使用して、Azure Maps Web SDK のソース コードをローカルに読み込み、アプリを使用してホストします。 このパッケージには TypeScript 定義も含まれています。

        > **npm install azure-maps-control**

       次に、Azure Maps のスタイル シートとスクリプト ソースの参照を、ファイルの `<head>` 要素に追加します。

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > Typescript の定義は、次のコードを追加することによってアプリケーションにインポートできます。
    >
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. ページの本文全体を埋めるようにマップをレンダリングするには、次の `<style>` 要素を `<head>` 要素に追加します。

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. ページの本文で、`<div>` 要素を追加して **myMap** の `id` を指定します。

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. マップ コントロールを初期化するには、html 本文で新しいスクリプト タグを定義します。 `Map` クラスのインスタンスを作成するときに、最初のパラメーターとしてマップ `<div>` の `id` または `HTMLElement` (`document.getElementById('myMap')` など) を渡します。 [認証オプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)を使用してマップを認証するには、固有の Azure Maps アカウント キーまたは Azure Active Directory (AAD) 資格情報を使用します。 

   アカウントを作成するか、キーを見つける必要がある場合、[アカウントの作成](quick-demo-map-app.md#create-an-account-with-azure-maps)手順と[主キーの取得](quick-demo-map-app.md#get-the-primary-key-for-your-account)手順に従ってください。 

   **language** オプションには、マップ ラベルとコントロールに使用される言語を指定します。 サポートされている言語に関する情報については、[サポートされている言語](supported-languages.md)に関するページを参照してください。認証にサブスクリプション キーを使用している場合、次を使用します。

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

   認証に Azure Active Directory (AAD) を使用している場合、次を使用します。

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
   ```

   Azure Active Directory (AAD) と Azure Maps を統合する方法を示すサンプルの一覧は、[ここ](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)にあります。 
    
   詳細については、[Azure Maps での認証](azure-maps-authentication.md)と [Azure Maps での Azure AD 認証のサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)に関するドキュメントを参照してください。

6. 必要に応じて、ページの見出しに次のメタ タグ要素を追加すると役立つ場合があります。

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. すべてをまとめると、HTML ファイルは次のようなコードになります。

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Web ブラウザーでファイルを開き、レンダリングされたマップを表示します。 出力は下の図のようになります。

   ![レンダリングされた結果を示すマップ イメージ](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>マップのローカライズ

Azure Maps には、レンダリングされたマップの言語と地域ビューを設定するための 2 つの異なる方法が用意されています。 最初のオプションでは、この情報をグローバルな `atlas` 名前空間に追加します。これにより、アプリ内のすべてのマップ コントロール インスタンスにより、これらの設定が既定で使用されます。 以下では、言語をフランス語 ("fr-FR") に、地域ビューを "auto" に設定します。

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

2 つ目のオプションでは、次のようにマップを読み込むときに、この情報をマップ オプションに渡します。

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> Web SDK を使用すると、言語と地域の設定が異なる複数のマップ インスタンスを同じページに読み込むことができます。 また、これらの設定は、マップの `setStyle` 関数を使用してマップを読み込んだ後に更新できます。 

言語が "fr-FR" に設定され、地域ビューが "auto" に設定されている Azure Maps の例を次に示します。

![ラベルをフランス語で表示するマップ イメージ](./media/how-to-use-map-control/websdk-localization.png)

サポートされている言語と地域ビューの完全な一覧については、[こちら](supported-languages.md)をご覧ください。

## <a name="azure-government-cloud-support"></a>Azure Government クラウドのサポート

Azure Maps Web SDK では、Azure Government クラウドがサポートされています。 Azure Maps Web SDK にアクセスする目的で使用される JavaScript と CSS URL はすべて変わりません。 Azure Maps プラットフォームの Azure Government cloud バージョンに接続するには、次のタスクを実行する必要があります。

対話型マップ コントロールを使用する場合は、`Map` クラスのインスタンスを作成する前に、次のコード行を追加します。 

```javascript
atlas.setDomain('atlas.azure.us');
```

マップとサービスを認証するときは、Azure Government クラウド プラットフォームからの Azure Maps 認証の詳細を必ず使用してください。

サービス モジュールを使用する場合は、API URL エンドポイントのインスタンスを作成する際にサービス用のドメインを設定する必要があります。 たとえば、次のコードでは `SearchURL` クラスのインスタンスが作成され、ドメインが Azure Government クラウドを指すようにされます。

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Azure Maps REST サービスに直接アクセスする場合は、URL ドメインを `atlas.azure.us` に変更します。 たとえば、検索 API サービスを使用する場合は、URL ドメインを `https://atlas.microsoft.com/search/` から `https://atlas.azure.us/search/` に変更します。

## <a name="next-steps"></a>次のステップ

マップを作成して操作する方法について説明します。

> [!div class="nextstepaction"]
> [マップを作成する](map-create.md)

マップにスタイルを設定する方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [マップ スタイルを選択する](choose-map-style.md)

マップにさらにデータを追加するには:

> [!div class="nextstepaction"]
> [マップを作成する](map-create.md)

> [!div class="nextstepaction"]
> [コード サンプル](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Azure Active Directory (AAD) と Azure Maps を統合する方法を示すサンプルの一覧については、次を参照してください。

> [!div class="nextstepaction"]
> [Azure AD 認証のサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
