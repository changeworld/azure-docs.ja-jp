---
title: Unity 向けの Azure Spatial Anchors をインストールする
description: Azure Spatial Anchors を使用するように Unity プロジェクトを構成する
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 08/17/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 57ead9636b7218ecfc7d72bb605b469d6a7d1ac6
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536359"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Unity プロジェクトでの Azure Spatial Anchors の構成

このガイドでは、Unity プロジェクトで Azure Spatial Anchors SDK の使用を開始する方法について説明します。

## <a name="requirements"></a>必要条件

Azure Spatial Anchors では現在、次の構成を持つ Unity 2019.4 (LTS) がサポートされています。

* AR Foundation 3.1 を備えた Unity 2019.4 が Azure Spatial Anchors 2.4.0 以降でサポートされています。

## <a name="configuring-a-project"></a>プロジェクトの構成

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[Unity Package Manager パッケージをプロジェクトに追加する](#tab/UPMPackage)

Unity 用の Azure Spatial Anchors は、現在、Unity Package Manager (UPM) パッケージを使用して配布されています。 これらのパッケージは [NPM レジストリ](https://bintray.com/microsoft/AzureMixedReality-NPM)にあります。 Unity プロジェクトでスコープ パッケージ レジストリを使用する方法の詳細については、[こちら](https://docs.unity3d.com/Manual/upm-scoped.html)の公式の Unity ドキュメントを参照してください。

#### <a name="add-the-registry-to-your-unity-project"></a>Unity プロジェクトにレジストリを追加する

1. ファイル エクスプローラーで、ご利用の Unity プロジェクトの `Packages` フォルダーに移動します。 テキスト エディターでプロジェクト マニフェスト ファイル `manifest.json` を開きます。
2. ファイルの上部の `dependencies` セクションと同じレベルで、次のエントリを追加して、Azure Spatial Anchors レジストリをプロジェクトに含めます。 `scopedRegistries` エントリにより、Azure Spatial Anchors SDK パッケージを検索する場所が Unity に指示されます。

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>SDK パッケージを Unity プロジェクトに追加する

| プラットフォーム | パッケージ名                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows |

1. プロジェクトでサポートするプラットフォーム (Android/iOS/HoloLens) ごとに、プロジェクト マニフェストの `dependencies` セクションにパッケージ名とパッケージ バージョンのエントリを追加します。 下記の例をご覧ください。

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. `manifest.json` ファイルを保存して閉じます。 Unity に戻ると、Unity によってプロジェクト マニフェストの変更が自動的に検出され、指定したパッケージが取得されます。 プロジェクト ビュー内で `Packages` フォルダーを展開すれば、適切なパッケージがインポートされていることを確認できます。

#### <a name="android-only-configure-the-maintemplategradle-file"></a>Android のみ:mainTemplate.gradle ファイルを構成する

1. **[Edit]\(編集\)**  >  **[Project Settings]\(プロジェクト設定\)**  >  **[Player]\(プレーヤー\)** の順に移動します。
2. **[プレーヤー設定]** の **[インスペクター] パネル**で、**Android** アイコンを選択します。
3. **[ビルド]** セクションで、 **[カスタム メイン Gradle テンプレート]** チェックボックスをオンにして、`Assets\Plugins\Android\mainTemplate.gradle` でカスタム Gradle テンプレートを生成します。
4. `mainTemplate.gradle` ファイルをテキスト エディターで開きます。 
5. `dependencies` セクションに、次の依存関係を貼り付けます。

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

すべて完了すると、`dependencies` セクションは次のようになります。

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

### <a name="import-the-asset-package"></a>[アセット パッケージをインポートする](#tab/UnityAssetPackage)

> [!WARNING]
> Azure Spatial Anchors SDK の Unity アセット パッケージの配布は、SDK バージョン 2.5.0 後に非推奨とされます。

1. [GitHub リリース](https://github.com/Azure/azure-spatial-anchors-samples/releases)から対象とするバージョンの `AzureSpatialAnchors.unitypackage` ファイルをダウンロードします。 
2. [こちら](https://docs.unity3d.com/Manual/AssetPackagesImport.html)の指示に従って、Unity アセット パッケージをプロジェクトにインポートします。    

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: Unity でアンカーを作成して配置する](./create-locate-anchors-unity.md)
