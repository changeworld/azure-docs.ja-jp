---
title: "Azure Location Based Services のマップ コントロールを使用する方法 | Microsoft Docs"
description: "Azure Location Based Services のクライアント側 JavaScript ライブラリであるマップ コントロールを使う方法を説明します。"
services: location-based-services
keywords: "SEO チャンプを確認せずに、キーワードを追加または編集しないでください。"
author: philmea
ms.author: philmea
ms.date: 11/22/2017
ms.topic: how-to
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: 9ddd11806accddb41c02c0c6681aac07bba25356
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>Azure Location Based Services のマップ コントロールを使用する方法
マップ コントロール クライアント側 JavaScript ライブラリを使うと、マップと埋め込まれた Azure Location Based Services の機能を、Web アプリケーションまたはモバイル アプリケーションにレンダリングできます。 

## <a name="prerequisites"></a>前提条件
Azure Location Based Services アカウントおよびサブスクリプション キー。 アカウントを作成して、サブスクリプション キーを取得する方法については、「[How to manage your Azure Location Based Services account and keys](how-to-manage-account-keys.md)」(Azure Location Based Services のアカウントおよびキーを管理する方法) をご覧ください。 

## <a name="create-a-new-map-in-a-web-page-using-the-map-control-api"></a>マップ コントロール API を使用して Web ページに新しいマップを作成する
マップ コントロール クライアント側 JavaScript ライブラリを使って、Web ページにマップを埋め込むことができます。

1. 新しいファイルを作成し、MapSearch.html という名前を付けます。

2. Azure Location Based Services のスタイル シートとスクリプト ソースの参照を、ファイルの `<head>` 要素に追加します。

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. ブラウザーに新しいマップをレンダリングするには、**#map** 参照を `<style>` 要素に追加します。

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. マップ コントロールを初期化するには、html 本文に新しいセクションを定義し、スクリプトを作成します。 Azure Location Based Services アカウントのご自分のサブスクリプション キーを使います。 

    ```html
    <div id="map">
        <script>
            var subscriptionKey = "<_subscriptionKey_>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        <script>
    <div>
    ```
    
5. Web ブラウザーでファイルを開き、レンダリングされたマップを表示します。