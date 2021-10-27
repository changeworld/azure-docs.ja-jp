---
title: iOS マップ コントロールの概要 | Microsoft Azure Maps
description: Azure Maps iOS SDK について理解を深めます。 SDK をインストールし、インタラクティブ マップを作成する方法を確認します。
author: stevemunk
ms.author: v-munksteve
ms.date: 10/08/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.openlocfilehash: 53d5b70f62d3ccea3d881a4e91a4b16287e1aa49
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043341"
---
# <a name="getting-started-with-azure-maps-ios-sdk-public-preview"></a>Azure Maps iOS SDK の概要 (パブリック プレビュー)

Azure Maps iOS SDK は iOS 用のベクター マップ ライブラリです。 この記事では、Azure Maps iOS SDK のインストールおよびマップの読み込みのプロセスを説明します。

## <a name="prerequisites"></a>前提条件

必ず、[iOS アプリの作成のクイックスタート](quick-ios-app.md)に関する記事の手順を完了してください。

## <a name="localizing-the-map"></a>マップのローカライズ

Azure Maps iOS SDK には、マップの言語と地域ビューを設定するための 3 つの方法が用意されています。 次のコードは、"*言語*" をフランス語 ("fr-FR") に、"*地域ビュー*" を "Auto" に設定するさまざまな方法を示しています。

1. 静的な `language` および `view` プロパティを使用し、言語と地域ビューの情報を `AzureMaps` クラスに渡します。 これでアプリに既定の言語と地域ビューのプロパティが設定されます。

    ```swift
    // Alternatively use Azure Active Directory authenticate.
    AzureMaps.configure(aadClient: "<Your aad clientId>", aadAppId: "<Your aad AppId>", aadTenant: "<Your aad Tenant>")
    
    // Set your Azure Maps Key.
    // AzureMaps.configure(subscriptionKey: "<Your Azure Maps Key>")
    
    // Set the language to be used by Azure Maps.
    AzureMaps.language = "fr-FR"
    
    // Set the regional view to be used by Azure Maps.
    AzureMaps.view = "Auto"
    ```

1. 言語と地域ビューの情報はマップ コントロール init に渡すこともできます。

    ```swift
    MapControl(options: [
        StyleOptions.language("fr-FR"),
        StyleOptions.view("Auto")
    ])
    ```

1. 言語と地域ビューのプロパティをプログラムで設定する最後の方法では、`setStyle` メソッドを使用します。 マップの言語と地域ビューを変更する必要がある場合は、いつでもこれを行うことができます。

    ```swift
    mapControl.getMapAsync { map in
        map.setStyle([
            StyleOptions.language("fr-FR"),
            StyleOptions.view("Auto")
        ])
    }
    ```

言語が "fr-FR" に設定され、地域ビューが "Auto" に設定されている Azure Maps アプリケーションの例を次に示します。

:::image type="content" source="media/ios-sdk/how-to-use-ios-map-control-library/fr-borderless.png" alt-text="ラベルをフランス語で表示するマップ イメージ。":::

サポートされている言語と地域ビューの完全な一覧については、「[Azure Maps でのローカライズのサポート](supported-languages.md)」を参照してください。

## <a name="navigating-the-map"></a>マップ内を移動する

このセクションでは、Azure Maps プログラムでのさまざまな操作方法について詳しく説明します。

### <a name="zoom-the-map"></a>マップをズームする

* 2 本の指でマップをタッチし、指でつまむようにすると縮小し、指を広げると拡大します。
* マップをダブルタップし、1 レベル分拡大します。
* 2 本の指でダブル タップし、マップを 1 レベル分縮小します。
* 2 回タップします。2 回目のタップで指をマップに置いたままにして、上にドラッグすると拡大し、下にドラッグすると縮小します。

### <a name="pan-the-map"></a>マップをパンする

* マップをタッチし、任意の方向にドラッグします。

### <a name="rotate-the-map"></a>マップを回転する

* 2 本の指でマップをタッチし、回転します。

### <a name="pitch-the-map"></a>マップのピッチを調整する

* 2 本の指でマップをタッチして、上または下にドラッグします。

## <a name="azure-government-cloud-support"></a>Azure Government クラウドのサポート

Azure Maps iOS SDK では、Azure Government クラウドを使用できます。 Azure Maps の認証詳細が指定されている次のコード行を追加することで Azure Maps Government クラウドの使用を指定します。

```
AzureMaps.domain = "atlas.azure.us"
```

マップとサービスを認証するときは、Azure Government クラウド プラットフォームからの Azure Maps 認証の詳細を必ず使用してください。