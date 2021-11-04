---
title: 'チュートリアル: Microsoft Azure Maps Creator を使用してフロア ガイドを作成する'
description: Microsoft Azure Maps Creator を使用してフロア ガイドを作成する方法のチュートリアル
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: ef1643f349fea853b15e0d8bcd2f07a0fd412188
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447153"
---
# <a name="tutorial-use-creator-to-create-indoor-maps"></a>チュートリアル:Creator を使用して屋内マップを作成する

このチュートリアルでは、フロア ガイドを作成する方法を示します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * フロア ガイド Drawing パッケージをアップロードする。
> * Drawing パッケージをマップ データに変換する。
> * マップ データからデータセットを作成する。
> * データセットのデータからタイルセットを作成する。
> * Azure Maps Web Feature Service (WFS) API のクエリを実行して、マップ地物について学習する。
> * マップ地物とデータセット内のデータを使用して地物状態セットを作成する。
> * 地物状態セットを更新する。

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)。
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。
3. [Creator リソースを作成します](how-to-manage-creator.md)。
4. [サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip)をダウンロードします。

このチュートリアルでは、[Postman](https://www.postman.com/) アプリケーションを使用しますが、別の API 開発環境を使用することもできます。

>[!IMPORTANT]
> このチュートリアルでは、`us.atlas.microsoft.com` の地理的 URL を使用します。 Creator サービスが米国内で作成されていない場合は、別の地理的 URL を使用する必要があります。  詳細については、「[Creator サービスにアクセスする](how-to-manage-creator.md#access-to-creator-services)」を参照してください。 リージョンと地理的な場所のマッピングを表示するには、「[Creator サービスの地理的スコープ](creator-geographic-scope.md)」を参照してください。

## <a name="upload-a-drawing-package"></a>Drawing パッケージをアップロードする

Drawing パッケージを Azure Maps リソースにアップロードするには、[Data Upload API](/rest/api/maps/data-v2/upload-preview) を使用します。

Data Upload API は、[Creator の実行時間の長い操作 API V2](creator-long-running-operation-v2.md) で定義されたパターンを実装する長時間トランザクションです。

Drawing パッケージをアップロードするには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[要求名]** を入力します (*POST Data Upload* など)。

4. **POST** HTTP メソッドを選択します。

5. [Data Upload API](/rest/api/maps/data-v2/upload-preview) への次の URL を入力します。要求は次のような URL になります (`{Azure-Maps-Primary-Subscription-key}` は、ご自分のプライマリ サブスクリプション キーに置き換えます)。

    ```http
    https://us.atlas.microsoft.com/mapData?api-version=2.0&dataFormat=dwgzippackage&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. **[Headers]\(ヘッダー\)** タブを選択します。

7. **[キー]** フィールドで、`Content-Type` を選択します。

8. **[値]** フィールドで、`application/octet-stream` を選択します。

     :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-header.png"alt-text="データ アップロードのヘッダー タブ情報。":::

9. **[Body]** タブを選択します。

10. ドロップダウン リストで、 **[binary]\(バイナリ\)** を選択します。

11. **[Select File]\(ファイルの選択\)** を選択し、Drawing パッケージを選択します。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-body.png" alt-text="Drawing パッケージを選択する。":::

12. **[Send]** を選択します。

13. 応答ウィンドウで、 **[Headers]\(ヘッダー\)** タブを選択します。

14. **Operation-Location** キーの値である `status URL` をコピーします。 `status URL` を使用して、Drawing パッケージのアップロードの状態を確認します。

     :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-response-header.png" alt-text="Location キーの状態 URL をコピーする。":::

### <a name="check-the-drawing-package-upload-status"></a>Drawing パッケージのアップロードの状態を確認する

Drawing パッケージの状態を確認し、その一意の ID (`udid`) を取得するには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*GET Data Upload Status*」など)。

4. **GET** HTTP メソッドを選択します。

5. 「[Drawing パッケージをアップロードする](#upload-a-drawing-package)」でコピーした `status URL` を入力します。 要求は次のような URL になります (`{Azure-Maps-Primary-Subscription-key}` は、ご自分のプライマリ サブスクリプション キーに置き換えます)。

    ```http
    https://us.atlas.microsoft.com/mapData/operations/{operationId}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. **[Send]** を選択します。

7. 応答ウィンドウで、 **[Headers]\(ヘッダー\)** タブを選択します。

8. **Resource-Location** キーの値である `resource location URL` をコピーします。 `resource location URL` には、Drawing パッケージ リソースの一意の識別子 (`udid`) が含まれています。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/resource-location-url.png" alt-text="リソースの場所の URL をコピーします。":::

### <a name="optional-retrieve-drawing-package-metadata"></a>(省略可能) Drawing パッケージ メタデータを取得する

Drawing パッケージ リソースからメタデータを取得できます。 このメタデータには、リソースの場所の URL、作成日、更新日、サイズ、アップロードの状態などの情報が含まれます。

コンテンツ メタデータを取得するには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*GET Data Upload Metadata*」など)。

4. . **GET** HTTP メソッドを選択します。

5. 「[Drawing パッケージのアップロードの状態を確認する](#check-the-drawing-package-upload-status)」でコピーした `resource Location URL` を入力します。 要求は次のような URL になります (`{Azure-Maps-Primary-Subscription-key}` は、ご自分のプライマリ サブスクリプション キーに置き換えます)。

    ```http
    https://us.atlas.microsoft.com/mapData/metadata/{udid}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. **[Send]** を選択します。

7. 応答ウィンドウで、 **[Body]\(本文\)** タブを選択します。メタデータは、次の JSON フラグメントのようになります。

    ```json
    {
        "udid": "{udid}",
        "location": "https://us.atlas.microsoft.com/mapData/6ebf1ae1-2a66-760b-e28c-b9381fcff335?api-version=2.0",
        "created": "5/18/2021 8:10:32 PM +00:00",
        "updated": "5/18/2021 8:10:37 PM +00:00",
        "sizeInBytes": 946901,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Drawing パッケージを変換する

Drawing パッケージがアップロードされたので、アップロードされたパッケージに `udid` を使用して、パッケージをマップ データに変換します。 Conversion API では、[こちら](creator-long-running-operation-v2.md)で定義されたパターンを実装する実行時間の長いトランザクションが使用されます。

Drawing パッケージを変換するには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*POST Convert Drawing Package*」など)。

4. **POST** HTTP メソッドを選択します。

5. [Conversion Service](/rest/api/maps/v2/conversion/convert) への次の URL を入力します (`{Azure-Maps-Primary-Subscription-key}` はご自分のプライマリ サブスクリプション キーに、`udid` はアップロードしたパッケージの `udid` に置き換えます)。

    ```http
    https://us.atlas.microsoft.com/conversions?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0&udid={udid}&inputType=DWG&outputOntology=facility-2.0
    ```

6. **[Send]** を選択します。

7. 応答ウィンドウで、 **[Headers]\(ヘッダー\)** タブを選択します。

8. **Operation-Location** キーの値である `status URL` をコピーします。 `status URL` を使用して、変換の状態を確認します。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-convert-location-url.png" border="true" alt-text="Drawing パッケージの location キーの値をコピーする。":::

### <a name="check-the-drawing-package-conversion-status"></a>Drawing パッケージ変換の状態を確認する

変換操作が完了すると、`conversionId` が返されます。 Drawing パッケージの変換プロセスの状態を確認すると、`conversionId` にアクセスできます。 その後、`conversionId` を使用して、変換されたデータにアクセスできます。

変換プロセスの状態を確認し、`conversionId` を取得するには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*GET Conversion Status*」など)。

4. **GET** HTTP メソッドを選択します。

5. 「[Drawing パッケージを変換する](#convert-a-drawing-package)」でコピーした `status URL` を入力します。 要求は次のような URL になります (`{Azure-Maps-Primary-Subscription-key}` は、ご自分のプライマリ サブスクリプション キーに置き換えます)。

    ```http
    https://us.atlas.microsoft.com/conversions/operations/{operationId}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. **[Send]** を選択します。

7. 応答ウィンドウで、 **[Headers]\(ヘッダー\)** タブを選択します。

8. **Resource-Location** キーの値である `resource location URL` をコピーします。 `resource location URL` には一意識別子 (`conversionId`) が含まれており、他の API から使用して、変換されたマップ データにアクセスできます。

      :::image type="content" source="./media/tutorial-creator-indoor-maps/data-conversion-id.png" alt-text="変換 ID をコピーする。":::

サンプル Drawing パッケージは、エラーや警告なしに変換されます。 ただし、ご自分の Drawing パッケージからエラーまたは警告を受け取る場合は、JSON 応答に [Drawing Error Visualizer](drawing-error-visualizer.md) へのリンクが含まれます。 Drawing Error Visualizer を使用して、エラーと警告の詳細を調べることができます。 変換エラーと警告を解決するための推奨事項を受け取るには、「[Drawing Conversion のエラーと警告](drawing-conversion-error-codes.md)」を参照してください。

次の JSON フラグメントは、変換の警告のサンプルを示しています。

```json
{
    "operationId": "{operationId}",
    "created": "2021-05-19T18:24:28.7922905+00:00",
    "status": "Succeeded",
     "warning": {
        "code": "dwgConversionProblem",
        "details": [
            {
                "code": "warning",
                "details": [
                    {
                        "code": "manifestWarning",
                        "message": "Ignoring unexpected JSON property: unitProperties[0].nonWheelchairAccessible with value False"
                    }
                ]
            }
        ]
    },
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>データセットを作成する

データセットは、建物、階層、部屋などのマップ地物のコレクションです。 データセットを作成するには、[Dataset Create API](/rest/api/maps/v2/dataset/create) を使用します。 Dataset Create API は、変換された Drawing パッケージの `conversionId` を受け取り、作成されたデータセットの `datasetId` を返します。

データセットを作成するには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*POST Dataset Create*」など)。

4. **POST** HTTP メソッドを選択します。

5. [Dataset API](/rest/api/maps/v2/dataset) への次の URL を入力します。 要求は、次の URL のようになります (`{Azure-Maps-Primary-Subscription-key}` はご自分のプライマリ サブスクリプション キーに、`{conversionId` は「[Drawing パッケージ変換の状態を確認する](#check-the-drawing-package-conversion-status)」で取得した `conversionId` に置き換えます)。

    ```http
    https://us.atlas.microsoft.com/datasets?api-version=2.0&conversionId={conversionId}&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. **[Send]** を選択します。

7. 応答ウィンドウで、 **[Headers]\(ヘッダー\)** タブを選択します。 

8. **Operation-Location** キーの値である `status URL` をコピーします。 `status URL` を使用して、データセットの状態を確認します。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-dataset-location-url.png" border="true" alt-text="データセットの location キーの値をコピーする。":::

### <a name="check-the-dataset-creation-status"></a>データセットの作成状態を確認する

データセット作成プロセスの状態を確認し、`datasetId` を取得するには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*GET Dataset Status*」など)。

4. **GET** HTTP メソッドを選択します。

5. 「[データセットを作成する](#create-a-dataset)」でコピーした `status URL` を入力します。 要求は次のような URL になります (`{Azure-Maps-Primary-Subscription-key}` は、ご自分のプライマリ サブスクリプション キーに置き換えます)。

    ```http
    https://us.atlas.microsoft.com/datasets/operations/{operationId}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. **[Send]** を選択します。

7. 応答ウィンドウで、 **[Headers]\(ヘッダー\)** タブを選択します。**Resource-Location** キーの値は `resource location URL` です。 `resource location URL` には、データセットの一意の識別子 (`datasetId`) が含まれています。

8. `datasetId` をコピーします。これは、このチュートリアルの次のセクションで使用します。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/dataset-id.png" alt-text="データセット ID をコピーする。":::

## <a name="create-a-tileset"></a>タイルセットを作成する

タイルセットは、マップ上にレンダリングされる一連のベクター タイルです。 タイルセットは既存のデータセットから作成されます。 ただし、タイルセットは、そのソースとなったデータセットから独立しています。 データセットが削除された後も、タイルセットは引き続き存在します。

タイルセットを作成するには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*POST Tileset Create*」など)。

4. **POST** HTTP メソッドを選択します。

5. [Tileset API](/rest/api/maps/v2/tileset) への次の URL を入力します。 要求は、次の URL のようになります (`{Azure-Maps-Primary-Subscription-key}` はご自分のプライマリ サブスクリプション キーに、`{datasetId`} は「[データセットの作成状態を確認する](#check-the-dataset-creation-status)」で取得した `datasetId` に置き換えます)。

    ```http
    https://us.atlas.microsoft.com/tilesets?api-version=2.0&datasetID={datasetId}&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. **[Send]** を選択します。

7. 応答ウィンドウで、 **[Headers]\(ヘッダー\)** タブを選択します。

8. **Operation-Location** キーの値である `status URL` をコピーします。 `status URL` を使用して、タイルセットの状態を確認します。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-tileset-location-url.png" border="true" alt-text="タイルセットの状態 URL の値をコピーする。":::

### <a name="check-the-tileset-creation-status"></a>タイルセットの作成状態を確認する

データセット作成プロセスの状態を確認し、`tilesetId` を取得するには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*GET Tileset Status*」など)。

4. **GET** HTTP メソッドを選択します。

5. 「[タイルセットを作成する](#create-a-tileset)」でコピーした `status URL` を入力します。 要求は次のような URL になります (`{Azure-Maps-Primary-Subscription-key}` は、ご自分のプライマリ サブスクリプション キーに置き換えます)。

    ```http
    https://us.atlas.microsoft.com/tilesets/operations/{operationId}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. **[Send]** を選択します。

7. 応答ウィンドウで、 **[Headers]\(ヘッダー\)** タブを選択します。**Resource-Location** キーの値は `resource location URL` です。  `resource location URL` には、データセットの一意の識別子 (`tilesetId`) が含まれています。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/tileset-id.png" alt-text="タイルセット ID をコピーする。":::

## <a name="query-datasets-with-wfs-api"></a>WFS API を使用してデータセットのクエリを実行する

データセットには、[WFS API](/rest/api/maps/v2/wfs) を使用してクエリを実行できます。 WFS API を使用して、すべての地物コレクションまたは特定のコレクションのクエリを実行できます。 チュートリアルのこのセクションでは、両方行います。 最初にすべてのコレクションに対してクエリを実行してから、`unit` コレクションのクエリを実行します。

### <a name="query-for-feature-collections"></a>地物コレクションのクエリを実行する

データセット内のすべてのコレクションに対してクエリを実行するには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*GET Dataset Collections*」など)。

4. **GET** HTTP メソッドを選択します。

5. [WFS API](/rest/api/maps/v2/wfs) への次の URL を入力します。 要求は、次の URL のようになります (`{Azure-Maps-Primary-Subscription-key}` はご自分のプライマリ サブスクリプション キーに、`{datasetId`} は「[データセットの作成状態を確認する](#check-the-dataset-creation-status)」で取得した `datasetId` に置き換えます)。

    ```http
    https://us.atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0
    ```

6. **[Send]** を選択します。

7. 応答本文は GeoJSON 形式で返され、データセット内のすべてのコレクションが含まれます。 わかりやすくするために、この例では `unit` コレクションのみを示しています。 すべてのコレクションを含む例を参照するには、[WFS Describe Collections API](/rest/api/maps/v2/wfs/collection-description) に関するページを参照してください。 コレクションの詳細については、`link` 要素内の任意の URL を選択して確認できます。

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

### <a name="query-for-unit-feature-collection"></a>ユニット地物コレクションのクエリを実行する

このセクションでは、`unit` 地物コレクションに対して [WFS API](/rest/api/maps/v2/wfs) のクエリを実行します。

データセット内のユニット コレクションに対してクエリを実行するには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*GET Unit Collection*」など)。

4. **GET** HTTP メソッドを選択します。

5. 次の URL を入力します (`{Azure-Maps-Primary-Subscription-key}` はご自分のプライマリ サブスクリプション キーに、`{datasetId`} は「[データセットの作成状態を確認する](#check-the-dataset-creation-status)」で取得した `datasetId` に置き換えます)。

    ```http
    https://us.atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0
    ```

6. **[Send]** を選択します。

7. 応答が返された後、`unit` 地物の 1 つに地物 `id` をコピーします。 次の例では、地物 `id` は "UNIT26" です。 このチュートリアルの次のセクションでは、地物 `id` として "UNIT26" を使用します。

    ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>地物状態セットを作成する

地物状態セットで、それらをサポートする特定の地物の動的プロパティと値を定義します。 このセクションでは、**occupancy** プロパティのブール値と対応するスタイルを定義する状態セットを作成します。

状態セットを作成するには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*POST Create Stateset*」など)。

4. **POST** HTTP メソッドを選択します。

5. [Stateset API](/rest/api/maps/v2/feature-state/create-stateset) への次の URL を入力します。 要求は、次の URL のようになります (`{Azure-Maps-Primary-Subscription-key}` はご自分のプライマリ サブスクリプション キーに、`{datasetId`} は「[データセットの作成状態を確認する](#check-the-dataset-creation-status)」で取得した `datasetId` に置き換えます)。

    ```http
    https://us.atlas.microsoft.com/featurestatesets?api-version=2.0&datasetId={datasetId}&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. **[Headers]\(ヘッダー\)** タブを選択します。

7. **[キー]** フィールドで、`Content-Type` を選択します。 

8. **[値]** フィールドで、`application/json` を選択します。

     :::image type="content" source="./media/tutorial-creator-indoor-maps/stateset-header.png"alt-text="状態セット作成用のヘッダー タブ情報。":::

9. **[Body]** タブを選択します。

10. ドロップダウン リストで、 **[raw]** と **[JSON]** を選択します。

11. 次の JSON スタイルをコピーして、 **[Body]\(本文\)** ウィンドウに貼り付けます。

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          }
       ]
    }
    ```

12. **[Send]** を選択します。

13. 応答が正常に返されたら、応答本文から `statesetId` をコピーします。 次のセクションでは、`statesetId` を使用して、地物 `id` が "UNIT26" のユニットの `occupancy` プロパティの状態を変更します。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/response-stateset-id.png"alt-text="状態セット ID 応答。":::

### <a name="update-a-feature-state"></a>地物状態を更新する

地物 `id` "UNIT26" を使用してユニットの `occupied` 状態を更新するには、次の手順を行います。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*PUT Set Stateset*」など)。

4. **PUT** HTTP メソッドを選択します。

5. [Feature Statesets API](/rest/api/maps/v2/feature-state/create-stateset) への次の URL を入力します。 要求は、次の URL のようになります (`{Azure-Maps-Primary-Subscription-key}` はご自分のプライマリ サブスクリプション キーに、`{statesetId`} は「[地物状態セットを作成する](#create-a-feature-stateset)」で取得した `statesetId` に置き換えます)。

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT26?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. **[Headers]\(ヘッダー\)** タブを選択します。

7. **[キー]** フィールドで、`Content-Type` を選択します。

8. **[値]** フィールドで、`application/json` を選択します。

     :::image type="content" source="./media/tutorial-creator-indoor-maps/stateset-header.png"alt-text="状態セット作成用のヘッダー タブ情報。":::

9. **[Body]** タブを選択します。

10. ドロップダウン リストで、 **[raw]** と **[JSON]** を選択します。

11. 次の JSON スタイルをコピーして、 **[本文]** ウィンドウに貼り付けます。

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

    >[!NOTE]
    > この更新は、POST されたタイム スタンプが前の要求のタイム スタンプより後の場合にのみ保存されます。

12. **[Send]** を選択します。

13. 正常に更新されると、`200 OK` HTTP 状態コードを受け取ります。 フロア ガイドに[動的スタイル設定](indoor-map-dynamic-styling.md)を実装した場合、レンダリングされたマップに指定されたタイムスタンプで更新内容が表示されます。

[Feature Get States API](/rest/api/maps/v2/feature-state/get-states) を使用すると、その地物 `id` を使用して地物の状態を取得できます。 [Feature State Delete State API](/rest/api/maps/v2/feature-state/delete-stateset) を使用して、状態セットとそのリソースを削除することもできます。

この記事で説明したさまざまな Azure Maps Creator サービスの詳細については、[Creator フロア ガイド](creator-indoor-maps.md)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップが必要なリソースはありません。

## <a name="next-steps"></a>次のステップ

Indoor Maps モジュールの使用方法については、次を参照してください。

> [!div class="nextstepaction"]
> [Indoor Maps モジュールを使用する](how-to-use-indoor-module.md)
