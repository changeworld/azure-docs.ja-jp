---
title: Azure Maps のマップ コントロールを使用する方法 | Microsoft Docs
description: Azure Maps のマップ コントロール クライアント側 JavaScript ライブラリを使用する方法について説明します。
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 5b8703c218790549a0cf5a319345132a0eca66ce
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025191"
---
# <a name="how-to-use-the-azure-maps-map-control"></a>Azure Maps のマップ コントロールを使用する方法
マップ コントロール クライアント側 JavaScript ライブラリを使用すると、マップと埋め込まれた Azure Maps の機能を、Web アプリケーションまたはモバイル アプリケーションにレンダリングできます。 

## <a name="create-a-new-map-in-a-web-page"></a>Web ページに新しいマップを作成する

マップ コントロール クライアント側 JavaScript ライブラリを使って、Web ページにマップを埋め込むことができます。

1. 新しいファイルを作成し、MapSearch.html という名前を付けます。

2. Azure Maps のスタイル シートとスクリプト ソースの参照を、ファイルの `<head>` 要素に追加します。

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```
    
3. ブラウザーに新しいマップをレンダリングするには、**#map** 参照を `<style>` 要素に追加します。

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. マップ コントロールを初期化するには、html 本文に新しいセクションを定義し、スクリプトを作成します。 スクリプトには固有の Azure Maps アカウント キーを使用します。 アカウントを作成するかキーを確認する必要がある場合は、「[How to manage your Azure Maps account and keys](how-to-manage-account-keys.md)」(Azure Maps のアカウントとキーを管理する方法) を参照してください。

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Web ブラウザーでファイルを開き、レンダリングされたマップを表示します。

## <a name="next-steps"></a>次の手順

この記事では Azure Maps キーを使って基本的な地図を作成する方法について説明しました。 マップに追加するコード例の詳細については、次の記事を参照してください。 

* [マップを作成する](map-create.md)
* [マップ スタイルを選択する](choose-map-style.md)
