---
title: Azure Maps のマップ コントロールを使用する方法 | Microsoft Docs
description: Azure Maps のマップ コントロール クライアント側 JavaScript ライブラリを使用する方法について説明します。
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 850f9b28c112c11fd98a8abc81a1811cd26d81cc
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166032"
---
# <a name="use-the-azure-maps-map-control"></a>Azure Maps のマップ コントロールを使用する

マップ コントロール クライアント側 JavaScript ライブラリを使用すると、マップと埋め込まれた Azure Maps の機能を、Web アプリケーションまたはモバイル アプリケーションにレンダリングできます。

## <a name="create-a-new-map-in-a-web-page"></a>Web ページに新しいマップを作成する

マップ コントロール クライアント側 JavaScript ライブラリを使って、Web ページにマップを埋め込むことができます。

1. 新しいファイルを作成し、**MapSearch.html** という名前を付けます。

2. Azure Maps のスタイル シートとスクリプト ソースの参照を、ファイルの `<head>` 要素に追加します。

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```

3. ブラウザーで新しいマップをレンダリングするには、**#map** 参照を `<style>` 要素に追加します。

    ```html
    <style>
        #map {
            width: 100%;
            height: 100%;
        }
    </style>
    ```

4. マップ コントロールを初期化するには、html 本文に新しいセクションを定義し、スクリプトを作成します。 スクリプトには固有の Azure Maps アカウント キーを使用します。 アカウントを作成するか、キーを確認する必要がある場合は、「[Azure Maps のアカウントとキーを管理する方法](how-to-manage-account-keys.md)」を参照してください。 **setLanguage** メソッドには、マップ ラベルとコントロールに使用される言語を指定します。 サポートされている言語の詳細については、[サポートされている言語](https://docs.microsoft.com/azure/azure-maps/supported-languages)に関するページを参照してください。

    ```html
    <div id="map">
        <script>
            atlas.setSubscriptionKey("<_your account key_>");
            atlas.setLanguage("en");
            var map = new atlas.Map("map", {
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```

5. Web ブラウザーでファイルを開き、レンダリングされたマップを表示します。

## <a name="next-steps"></a>次の手順

例全体を使用してマップを作成する方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [マップを作成する](map-create.md)

マップにスタイルを設定する方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [マップ スタイルを選択する](choose-map-style.md)
