---
title: Azure Maps Creator 屋内マップの動的スタイル設定を実装する
description: Creator の屋内マップに動的スタイル設定を実装する方法について説明します
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 9089ff5adda1eedd441fc221ae1d5a0d3401ae12
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462066"
---
# <a name="implement-dynamic-styling-for-creator-indoor-maps"></a>Creator の屋内マップに動的スタイル設定を実装する

Azure Maps Creator [Feature State サービス](/rest/api/maps/v2/feature-state)を使用すると、屋内マップ データ地物の動的プロパティに基づくスタイルを適用できます。  たとえば、施設の会議室を特定の色でレンダリングして、占有状態を反映できます。 この記事では、[Feature State サービス](/rest/api/maps/v2/feature-state)と [Indoor Web モジュール](how-to-use-indoor-module.md)を使用して、屋内マップ地物を動的にレンダリングする方法について説明します。

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。
3. [Creator リソースを作成します](how-to-manage-creator.md)
4. [サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)をダウンロードします。
5. [屋内マップを作成](tutorial-creator-indoor-maps.md)して、`tilesetId` と `statesetId` を取得します。
6. [Indoor Maps モジュールの使用方法](how-to-use-indoor-module.md)の手順に従って、Web アプリケーションを構築します。

このチュートリアルでは [Postman](https://www.postman.com/) アプリケーションを使用していますが、別の API 開発環境を選択することもできます。

## <a name="implement-dynamic-styling"></a>動的スタイル設定を実装する

前提条件を完了すると、サブスクリプション キー `tilesetId` および `statesetId` を使用して構成された単純な Web アプリケーションが作成されます。

### <a name="select-features"></a>機能を選択する

動的スタイル設定を実装するには、会議室やカンファレンス ルームなどの地物をその地物 `id` から参照する必要があります。 地物 `id` を使用して、その地物の動的プロパティまたは "*状態*" を更新します。 データセットで定義されている地物を表示するには、次のいずれかの方法を使用できます。

* WFS API (Web Feature サービス)。 [WFS API](/rest/api/maps/v2/wfs) を使用して、データセットのクエリを実行できます。 WFS は、[Open Geospatial Consortium API の機能](http://docs.opengeospatial.org/DRAFTS/17-069r1.html)に従います。 WFS API は、データセット内の地物のクエリに役立ちます。 たとえば、WFS を使用して、特定の施設とレベルの中規模の会議室をすべて検索することができます。

* ユーザーが Web アプリケーションを使用してマップ上の地物を選択できるようにするカスタマイズされたコードを実装します。 この記事ではこのオプションを使用します。  

次のスクリプトでは、マウス クリック イベントを実装します。 このコードでは、クリックされたポイントに基づいて地物 `id` を取得します。 アプリケーションでは、Indoor Manager コード ブロックの後にコードを挿入できます。 アプリケーションを実行した後、コンソールを確認して、クリックしたポイントの地物 `id` を取得します。

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    features.forEach(function (feature) {
        if (feature.layer.id == 'indoor_unit_office') {
            console.log(feature);
        }
    });
});
```

[屋内マップの作成](tutorial-creator-indoor-maps.md)チュートリアルでは、`occupancy` の状態の更新を受け入れるように地物状態セットを構成しました。

次のセクションでは、オフィス `UNIT26` の占有 "*状態*" を `true` に設定し、オフィス `UNIT27` を `false` に設定します。

### <a name="set-occupancy-status"></a>占有状態を設定する

 ここでは、2 つのオフィス `UNIT26` と `UNIT27` の状態を更新します。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[要求名]** を入力します (*POST Data Upload* など)。

4. [Feature Update States API](/rest/api/maps/v2/feature-state/update-states) に対する次の URL を入力します (`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換え、`statesetId` を `statesetId` に置き換えます)。

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT26?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

5. **[Headers]\(ヘッダー\)** タブを選択します。

6. **[キー]** フィールドで、`Content-Type` を選択します。 **[値]** フィールドで、`application/json` を選択します。

     :::image type="content" source="./media/indoor-map-dynamic-styling/stateset-header.png"alt-text="状態セット作成用のヘッダー タブ情報。":::

7. **[Body]** タブを選択します。

8. ドロップダウン リストで、 **[raw]** と **[JSON]** を選択します。

9. 次の JSON スタイルをコピーして、 **[本文]** ウィンドウに貼り付けます。

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2020-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!IMPORTANT]
    >POST されたタイム スタンプが、同じ地物 `ID` に対する以前の地物状態の更新要求で使用されたタイム スタンプより後の場合にのみ、更新は保存されます。

10. ステップ 7 で使用した URL を変更し、`UNIT26` を `UNIT27` に置き換えます。

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT27?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

11. 次の JSON スタイルをコピーして、 **[本文]** ウィンドウに貼り付けます。

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2020-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>マップ上で動的スタイルを視覚化する

以前にブラウザーで開いた Web アプリケーションには、マップ地物の更新された状態が反映されているはずです。

* オフィス `UNIT27`(142) が緑で表示されます。
* オフィス `UNIT26`(143) が赤で表示されます。

![緑の空き部屋と赤の空いていない部屋](./media/indoor-map-dynamic-styling/room-state.png)

[ライブ デモを見る](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [屋内マップ用の Creator](creator-indoor-maps.md)

この記事で説明されている API のリファレンスを参照してください。

> [!div class="nextstepaction"]
> [Data Upload](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [データ変換](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [データセット](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [タイルセット](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [地物状態セット](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS サービス](creator-indoor-maps.md#web-feature-service-api)
