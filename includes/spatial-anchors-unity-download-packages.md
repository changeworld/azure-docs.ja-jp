---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/18/2021
ms.author: parkerra
ms.openlocfilehash: d91c0aeda2b7ae2f133d2099cbc9d238fd19d287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719662"
---
必要なパッケージをダウンロードするには、<a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> がインストールされている必要があります。

次のコマンドを実行します。`<version_number>` は、現在のフォルダーにダウンロードする Azure Spatial Anchors のバージョンに置き換えてください。

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> 入手できる Azure Spatial Anchors パッケージのバージョンを一覧表示するには、次のコマンドを実行します。
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

> [!WARNING]
> ASA SDK 2.7.0 は、サポートされている最小バージョンです。 Unity 2020 を使用している場合は、ASA SDK 2.9.0 がサポートされている最小バージョンです。

コマンドを実行したフォルダーに、Azure Spatial Anchors のコア パッケージがダウンロードされます。

プロジェクトでサポートするプラットフォーム (Android、iOS、HoloLens) ごとに、この手順を繰り返してパッケージをダウンロードします。

| プラットフォーム | パッケージ名                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |