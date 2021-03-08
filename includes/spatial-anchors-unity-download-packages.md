---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: f6c2780ccbb914228a9870cb3b5fe4b0e3d0b214
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569661"
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

コマンドを実行したフォルダーに、Azure Spatial Anchors のコア パッケージがダウンロードされます。

プロジェクトでサポートするプラットフォーム (Android、iOS、HoloLens) ごとに、この手順を繰り返してパッケージをダウンロードします。

| プラットフォーム | パッケージ名                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |