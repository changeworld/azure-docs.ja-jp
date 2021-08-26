---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 2cd6fff6cc68baa28e85fa0c17294bcfe783ae54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121721793"
---
次の手順は、Unity 用 Azure Spatial Anchors パッケージのダウンロードです。 

> [!WARNING]
> ASA SDK 2.7.0 は、サポートされている最小バージョンです。 Unity 2020 を使用している場合は、ASA SDK 2.9.0 がサポートされている最小バージョンです。

Unity で Azure Spatial Anchors を使用するには、コア パッケージに加えて、サポート予定のプラットフォーム (Android、iOS、HoloLens) ごとに、プラットフォーム固有のパッケージをダウンロードする必要があります。

| プラットフォーム | パッケージ名                                    |
|----------|-------------------------------------------------|
| Android、iOS、HoloLens  | com.microsoft.azure.spatial-anchors-sdk.core@<version_number> |
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