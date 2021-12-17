---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/12/2021
ms.author: parkerra
ms.openlocfilehash: 96a820f6cc2f0ae41f7b17fc284ba0a2a064b45d
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490502"
---
次の手順は、Unity 用 Azure Spatial Anchors パッケージのダウンロードです。 

> [!IMPORTANT]
> ASA SDK 2.7.0 は、サポートされている最小バージョンです。 Unity 2020 を使用している場合は、ASA SDK 2.9.0 がサポートされている最小バージョンです。 Mixed Reality OpenXR プラグインを使用している場合は、ASA SDK 2.10.0 がサポートされている最小バージョンです。

Unity で Azure Spatial Anchors を使用するには、コア パッケージと、サポート予定のプラットフォーム (Android、iOS、HoloLens) ごとに、プラットフォーム固有のパッケージをダウンロードする必要があります。 これは、次の 2 つ以上のパッケージをダウンロードすることを意味します。

| プラットフォーム | パッケージ名                                    |
|----------|-------------------------------------------------|
| すべてのプラットフォーム  | com.microsoft.azure.spatial-anchors-sdk.core@<version_number> |
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |

# <a name="download-with-web-browser"></a>[Web ブラウザーを使用してダウンロードする](#tab/unity-package-web-ui)

[ここで](https://dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging?_a=feed&feed=Unity-packages)、Unity の Azure Spatial Anchors コアパッケージ (com.microsoft.azure.spatial-anchors-sdk.core) を見つけます。 目的のバージョンを選択し、 **[Download]\(ダウンロード\)** ボタンを使用してパッケージをダウンロードします。 この手順を繰り返して、サポート予定のプラットフォームごとにパッケージをダウンロードします。

# <a name="download-with-npm"></a>[NPM を使用してダウンロードする](#tab/unity-package-npm)

この手順の場合、<a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> がインストールされ、使用できる状態である必要があります。

次のコマンドを実行します。`<version_number>` は、現在のフォルダーにダウンロードする Azure Spatial Anchors のバージョンに置き換えてください。

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> 入手できる Azure Spatial Anchors パッケージのバージョンを一覧表示するには、次のコマンドを実行します。
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

コマンドを実行したフォルダーに、Azure Spatial Anchors のコア パッケージがダウンロードされます。 この手順を繰り返して、サポート予定のプラットフォームごとにパッケージをダウンロードします。

# <a name="install-with-mixed-reality-feature-tool"></a>[Mixed Reality Feature Tool を使用してインストールする](#tab/unity-package-mixed-reality-feature-tool)

次の手順に進みます。 後の手順で、<a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality Feature Tool</a> を使用します。

---
