---
title: "Azure Media Clipper への資産の読み込み | Microsoft Docs"
description: "Azure Media Clipper へ資産を読み込む手順"
services: media-services
keywords: "クリップ;サブクリップ;エンコード;メディア"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6062e6ae22971055e186b5d1068ba78c900a7a4c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="loading-assets-into-azure-media-clipper"></a>Azure Media Clipper への資産の読み込み
資産は、次の 2 つの方法で Azure Media Clipper に読み込むことができます。
1. 資産のライブラリに静的に渡す
2. API を使用して資産のリストを動的に生成する

## <a name="loading-static-asset-library"></a>静的な資産ライブラリの読み込み
この場合は、Clipper に資産の静的セットを渡します。 各資産には、AMS 資産/フィルター ID、名前、発行されたストリーミング URL が含まれています。 該当する場合、コンテンツ保護認証トークンまたはサムネイル URL の配列を渡せます。 渡された場合は、サムネイルはインターフェイスに表示されます。 アセット ライブラリが静的で小規模なシナリオでは、ライブラリ内の資産ごとにアセット コントラクトを渡すことができます。

静的なアセット ライブラリを読み込むには、**load** メソッドを使用して各アセットの JSON 表現を渡します。 次のコード サンプルは、1 つの資産の JSON 表現を示しています。

```javascript
var assets = 
{
  /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
  "id": "my-asset-or-dynamic-manifest-asset-filter-id",

  /* Required: represents the asset name as shown in the Clipper interface */
  "name": "My Asset or Dynamic Manifest Asset Filter Name",

  /* Required: must be one of the following values: "asset" or "filter" */
  /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
  "type": "asset",

  /* Required */
  "source": {

    /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
    "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",

    /* Optional: default value "application/vnd.ms-sstr+xml" */
    "type": "application/vnd.ms-sstr+xml",

    /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
    "protectionInfo": [{
        "type": "AES",
        "authenticationToken": "Bearer aes-token-placeholder"
      },
      {
        "type": "PlayReady",
        "authenticationToken": "Bearer playready-token-placeholder"
      },
      {
        "type": "Widevine",
        "authenticationToken": "Bearer widevine-token-placeholder"
      },
      {
        "type": "FairPlay",
        "certificateUrl": "//example/path/to/myfairplay.der",
        "authenticationToken": "Bearer fairplay-token-placeholder"
      }
    ]
  },

  /* Optional: array containing thumbnail URLs for the video. */
  /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
  "thumbnails": [
    "//example/path/thumbnail_001.jpg",
    "//example/path/thumbnail_002.jpg",
    "//example/path/thumbnail_003.jpg",
    "//example/path/thumbnail_004.jpg",
    "//example/path/thumbnail_005.jpg"
  ]
};
var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.load(assets)
```

> [!NOTE]
> サムネイル URL が Clipper タイムラインで予想通りに動作するためには、動画全体にわたって均一に分配 (再生時間を基に) され、配列内で時系列順に並んでいる必要があります。 次の JSON プリセット スニペットは、'Media Encoder Standard' のプロセッサを搭載したイメージを生成するサンプルとして使用できます。

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="loading-dynamic-asset-library"></a>動的な資産ライブラリの読み込み
または、コールバックを使用して動的に資産を読み込むことができます。 資産が動的に生成されるか、大規模なライブラリのシナリオでは、コールバックによって読み込む必要があります。 資産を動的に読み込むには、オプションの onLoadAssets コールバック関数を実装する必要があります。 この関数は、初期化時に Clipper に渡されます。 解決済みの資産は、静的に読み込まれる資産と同じコントラクトに従う必要があります。 次のコード サンプルは、メソッドのシグネチャ、必要な入力、および予想される出力を示しています。

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, retuns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: perform the back-end AJAX request to get the assets using the filter parameters (search, skip, take).
            var assets = [{
                // asset (input contract)
            }, {
                // asset (input contract)
            }];

            if (/* everything turned out fine */ assets !== null) {
                resolve({
                    total: 100,
                    assets: assets
                });
            }
            else {
                reject(Error("error details"));
            }
        });

        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```