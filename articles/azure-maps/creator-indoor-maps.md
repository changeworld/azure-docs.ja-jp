---
title: Azure Maps Creator で屋内マップを操作する
description: この記事では、Azure Maps Creator サービスに適用される概念について説明します
author: stevemunk
ms.author: v-munksteve
ms.date: 11/11/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 0e4f25e140c9b7d2693c94ead25b375e2298adbc
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400017"
---
# <a name="creator-for-indoor-maps"></a>屋内マップ用の Creator

この記事では、Azure Maps Creator に適用される概念とツールについて説明します。 Azure Maps Creator API と SDK を使い始める前に、この記事を読むことをお勧めします。

Creator を使用すると、フロア ガイド データに基づくマップの地物を含むアプリケーションを開発できます。 この記事では、マップ データをアップロード、変換、作成、使用するプロセスについて説明します。  通常、ワークフローは、専門分野と責任の領域が異なる 2 人の異なる人によって遂行されます。

- マップ作成者: マップ データの選択と準備を担当します。
- Creator マップ データ ユーザー: アプリケーションで顧客マップ データを利用します。

次の図はこのワークフロー全体を示したものです。

![Creator のマップ データ ワークフロー](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator"></a>Azure Maps Creator を作成する

Creator services を使用するためには、Gen 2 価格レベルの Azure Maps アカウントで Azure Maps Creator を作成する必要があります。 Azure Maps で Azure Maps Creator を作成する方法の詳細については、「[Azure Maps Creator を管理する](how-to-manage-creator.md)」を参照してください。

> [!TIP]
> 価格情報については、「[Azure Maps の価格](https://aka.ms/CreatorPricing)」の *Creator* セクションをご覧ください。

## <a name="creator-authentication"></a>Creator の認証

Creator は Azure Maps Access Control (IAM) の設定を継承します。 データにアクセスするすべての API 呼び出しは、認証および承認の規則と共に送信される必要があります。

Creator の使用状況データは、Azure Maps の使用状況グラフとアクティビティ ログに組み込まれています。 詳細については、「[Azure Maps での認証の管理](./how-to-manage-authentication.md)」をご覧ください。

>[!Important]
>次の機能を使用することをお勧めします。
>
> - Creator サービスを使用して Azure Maps アカウントで作成されたすべてのソリューションでは、Azure Active Directory (Azure AD)。 Azure AD の詳細については、「[Azure AD 認証](azure-maps-authentication.md#azure-ad-authentication)」を参照してください。
>
>- ロールベースのアクセス制御の設定。 これらの設定を使用すると、マップの作成者が Azure Maps データ共同作成者のロールとして機能し、ユーザーは Azure Maps データ リーダーのロールとして機能することができます。 詳細については、「[ロールベースのアクセス制御による認証](azure-maps-authentication.md#authorization-with-role-based-access-control)」を参照してください。

## <a name="creator-data-item-types"></a>Creator のデータ項目の種類

Creator サービスでは、以下のセクションで定義および説明するさまざまなデータの種類が作成、格納、使用されます。 Creator のデータ項目には、次の種類があります。

- 変換されたデータ
- データセット
- タイルセット
- 地物の状態セット

## <a name="upload-a-drawing-package"></a>Drawing パッケージをアップロードする

Creator では、アップロードされた Drawing パッケージを変換することによって、屋内マップ データが収集されます。 Drawing パッケージでは、構築または再モデル化されたファシリティが表されています。 Drawing パッケージの要件については、「[Drawing パッケージの要件](drawing-requirements.md)」を参照してください。

Drawing パッケージをアップロードするには、[Azure Maps Data Upload API](/rest/api/maps/data-v2/update-preview) を使用します。 Drawing パッキングがアップロードされた後、Data Upload API からはユーザー データ識別子 (`udid`) が返されます。 その後、`udid` を使用して、アップロードされたパッケージをフロア ガイド データに変換できます。

## <a name="convert-a-drawing-package"></a>Drawing パッケージを変換する

[Azure Maps Conversion サービス](/rest/api/maps/v2/conversion)では、アップロードした Drawing パッケージが屋内マップ データに変換されます。 Conversion サービスでは、パッケージの検証も行われます。 検証の問題は、2 つの種類に分類されます。

- エラー: エラーが検出されると、変換処理は失敗します。 エラーが発生すると、Conversion サービスから、[Azure Maps Drawing Error Visualizer](drawing-error-visualizer.md) スタンドアロン Web アプリケーションへのリンクが提供されます。 Drawing Error Visualizer を使用すると、変換プロセスの間に発生した [Drawing パッケージの警告とエラー](drawing-conversion-error-codes.md)を調べることができます。 エラーを修正した後、パッケージのアップロードと変換を試みることができます。
- 警告: 警告が検出された場合、変換は成功します。 ただし、すべての警告を確認して解決することをお勧めします。 警告は、変換の一部が無視されたか、自動的に修正されたことを意味します。 警告を解決しないと、後のプロセスでエラーが発生する可能性があります。
詳細については、[Drawing パッケージの警告とエラー](drawing-conversion-error-codes.md)に関するページを参照してください。

## <a name="create-indoor-map-data"></a>屋内マップ データを作成する

Azure Maps Creator には、マップの作成をサポートする次のサービスが用意されています。

- [Dataset サービス](/rest/api/maps/v2/dataset)。
- [Tileset サービス](/rest/api/maps/v2/tileset)。
データセットのベクター ベースの表現を作成するには、Tileset サービスを使用します。 アプリケーションでは、タイルセットを使用して、データセットの視覚的なタイルベースのビューを提供できます。
- [地物状態サービス](/rest/api/maps/v2/feature-state)。 動的なマップ スタイル設定をサポートするには、地物状態サービスを使用します。 アプリケーションで動的なマップ スタイルを使用して、IoT システムによって提供されるスペース上にリアルタイムのイベントを反映できます。

### <a name="datasets"></a>データセット

データセットは、屋内マップの地物のコレクションです。 フロア ガイドの地物は、変換された Drawing パッケージで定義されている設備を表します。 [Dataset サービス](/rest/api/maps/v2/dataset)を使用してデータセットを作成した後は、任意の数の[タイルセット](#tilesets)または[特徴状態セット](#feature-statesets)を作成できます。

開発者は、いつでも [Dataset サービス](/rest/api/maps/v2/dataset)を使用して、既存のデータセットに対する設備の追加や削除を行うことができます。 API を使用して既存のデータセットを更新する方法の詳細については、[Dataset サービス](/rest/api/maps/v2/dataset)に関する記事で追加オプションを参照してください。 データセットを更新する方法の例については、「[データのメンテナンス](#data-maintenance)」を参照してください。

### <a name="tilesets"></a>タイルセット

タイルセットは、均一のグリッド タイルのセットを表すベクター データのコレクションです。 開発者は、[Tileset サービス](/rest/api/maps/v2/tileset)を使用して、データセットからタイルセットを作成できます。

さまざまなコンテンツ ステージを反映するため、同じデータセットから複数のタイルセットを作成できます。 たとえば、家具や機器を含むタイルセットと、家具や機器を含まない別のタイルセットを作成することができます。 最新のデータ更新を含むタイルセットと、最新のデータ更新を含まない別のタイルセットを生成することもできます。

タイルセットでは、ベクター データだけでなく、マップ レンダリング最適化のためのメタデータも提供されます。 たとえば、タイルセットのメタデータには、タイルセットの最小と最大のズーム レベルが含まれます。 また、メタデータでは、タイルセットの地理的範囲を定義する境界ボックスも提供されます。 アプリケーションで境界ボックスを使用して、プログラムで正しい中心点を設定することができます。 タイルセットのメタデータの詳細については、[Tileset List API](/rest/api/maps/v2/tileset/list)に関するページを参照してください。

タイルセットを作成した後は、[Render V2 サービス](#render-v2-get-map-tile-api)で取得できます。

タイルセットが古くなり、役に立たなくなった場合は、タイルセットを削除できます。 タイルセットを削除する方法については、「[データのメンテナンス](#data-maintenance)」を参照してください。

>[!NOTE]
>タイルセットは、作成元のデータセットから独立しています。 データセットからタイルセットを作成した場合、後でそのデータセットを更新しても、タイルセットは更新されません。 
>
>データセットの変更を反映するには、新しいタイルセットを作成する必要があります。 同様に、タイルセットを削除しても、データセットは影響を受けません。

### <a name="feature-statesets"></a>地物状態セット

地物状態セットは、部屋や機器といったデータセットの地物に割り当てられた動的なプロパティ ("*状態*") のコレクションです。 "*状態*" の例としては、温度や占有率などがあります。 各 "*状態*" は、プロパティの名前、値、最終更新のタイムスタンプが含まれる、キーと値のペアです。

[Feature State サービス](/rest/api/maps/v2/feature-state/create-stateset)を使用すると、データセットに対する地物状態セットを作成および管理できます。 状態セットは、1 つまたは複数の "*状態*" によって定義されます。 各地物 (部屋など) には、1 つの "*状態*" を関連付けることができます。

状態セット内の各 "*状態*" の値は、IoT デバイスや他のアプリケーションで更新または取得できます。  たとえば、[Feature State Update API](/rest/api/maps/v2/feature-state/update-states) を使用すると、空間占有量を測定するデバイスで、部屋の状態の変化を体系的に送信できます。

アプリケーションでは、地物状態セットを使用することで、現在の状態とそれぞれのマップ スタイルに応じて、設備内の地物を動的にレンダリングできます。 地物状態セットを使用してレンダリング マップ内の地物のスタイルを設定する方法の詳細については、「[Indoor Maps モジュール](#indoor-maps-module)」を参照してください。

>[!NOTE]
>タイルセットと同様に、データセットを変更しても既存の地物状態セットに影響はなく、地物状態セットを削除しても、関連付けられているデータセットには影響しません。

## <a name="using-indoor-maps"></a>屋内マップの使用

### <a name="render-v2-get-map-tile-api"></a>Render V2-Get Map Tile API

Azure Maps の [Render V2-Get Map Tile API](/rest/api/maps/render-v2/get-map-tile) は、Creator のタイルセットをサポートするようにが拡張されています。

アプリケーションで Render V2-Get Map Tile API を使用して、タイルセットを要求できます。 その後、タイルセットをマップ コントロールまたは SDK に統合できます。 Render V2 サービスを使用するマップ コントロールの例については、「[Indoor Maps モジュール](#indoor-maps-module)」を参照してください。

### <a name="web-feature-service-api"></a>Web Feature Service API

[Web Feature Service (WFS) API](/rest/api/maps/v2/wfs) を使用して、データセットのクエリを実行できます。 WFS は、[Open Geospatial Consortium API の機能](http://docs.opengeospatial.org/DRAFTS/17-069r1.html)に従います。 WFS API を使用すると、データセット自体の内部にある地物のクエリを実行できます。 たとえば、WFS を使用して、特定の施設とレベルの中規模の会議室をすべて検索することができます。

### <a name="alias-api"></a>Alias API

Conversion、Dataset、Tileset、Feature State などの Creator サービスからは、API から作成された各リソースの識別子が返されます。 [Alias API](/rest/api/maps/v2/alias) を使用すると、リソース識別子を参照するための別名を割り当てることができます。

### <a name="indoor-maps-module"></a>Indoor Maps モジュール

[Azure Maps Web SDK](./index.yml) には、Indoor Maps モジュールが含まれています。 このモジュールでは、Azure Maps の "*マップ コントロール*" ライブラリに対する拡張機能が提供されます。 Indoor Maps モジュールでは、Creator で作成された屋内マップがレンダリングされます。 ユーザーが異なるフロアを視覚化できる "*フロア ピッカー*" などのウィジェットが統合されています。

Indoor Maps モジュールを使用すると、フロア ガイド データを他の [Azure Maps サービス](./index.yml)と統合する Web アプリケーションを作成できます。 最も一般的なアプリケーションの設定としては、道路、画像、気象、交通などの他のマップからフロア ガイドへの知識の追加などがあります。

また、Indoor Maps モジュールでは動的なマップ スタイル設定もサポートされています。 アプリケーションで地物状態セットの動的なスタイル設定を実装する方法の詳細な手順については、[Indoor Map モジュールの使用](how-to-use-indoor-module.md)に関する記事を参照してください。

### <a name="azure-maps-integration"></a>Azure Maps の統合

屋内マップ用のソリューションの開発を始めたら、既存の Azure Maps の機能を統合する方法がわかります。 たとえば、[Azure Maps Geofence API](/rest/api/maps/spatial/postgeofence) と Creator のフロア ガイドを使用して、資産の追跡や安全性のシナリオを実装できます。 たとえば、Geofence API を使用して、ワーカーが屋内の特定の区域に出入りしたかどうかを判断できます。 Azure Maps と IoT テレメトリを接続する方法の詳細については、[こちらの IoT 空間分析チュートリアル](tutorial-iot-hub-maps.md)を参照してください。

### <a name="data-maintenance"></a>データのメンテナンス

 Azure Maps Creator の List、Update、Delete API を使用すると、データセット、タイルセット、地物状態セットの一覧表示、更新、削除を行うことができます。

>[!NOTE]
>アイテムの一覧を確認して削除するかどうかを決定するときは、すべての依存 API またはアプリケーションに対するその削除の影響を考慮します。 たとえば、アプリケーションで [Render V2-Get Map Tile API](/rest/api/maps/render-v2/get-map-tile) により使用されているタイルセットを削除した場合、そのタイルセットのレンダリングが失敗するようになります。

### <a name="example-updating-a-dataset"></a>例:データセットの更新

次の例では、データセットの更新、新しいタイルセットの作成、古いタイルセットの削除を行う方法を示します。

1. 「[Drawing パッケージをアップロードする](#upload-a-drawing-package)」および「[Drawing パッケージを変換する](#convert-a-drawing-package)」セクションの手順に従い、新しい Drawing パッケージをアップロードして変換 ます。
2. [Dataset Create API](/rest/api/maps/v2/dataset/create) を使用して、変換されたデータを既存のデータセットに追加します。
3. [Tileset Create API](/rest/api/maps/v2/tileset/create) を使用して、更新されたデータセットから新しいタイルセットを生成します。
4. 次のステップのために、新しい **tilesetId** を保存します。
5. 更新されたキャンパス データセットの視覚化を有効にするには、アプリケーションでタイルセット識別子を更新します。 古いタイルセットを使用しなくなった場合は、削除することができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Creator 屋内マップを作成する](tutorial-creator-indoor-maps.md)
