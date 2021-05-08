---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 02/18/2021
ms.author: crtreasu
ms.openlocfilehash: ada83d6263ef033208200d810c53c5f045acc9a7
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104316"
---
次の手順は、Unity 用 Azure Object Anchors パッケージのダウンロードです。

# <a name="download-with-web-browser"></a>[Web ブラウザーを使用してダウンロードする](#tab/unity-package-web-ui)

[ここ](https://aka.ms/aoa/unity-sdk/package)で Unity 用 Azure Object Anchors パッケージを見つけます。 目的のバージョンを選択し、 **[Download]\(ダウンロード\)** ボタンを使用してパッケージをダウンロードします。

# <a name="download-with-npm"></a>[NPM を使用してダウンロードする](#tab/unity-package-npm)

この手順の場合、<a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> がインストールされ、使用できる状態である必要があります。

`<version_number>` をダウンロードする Azure Object Anchors のバージョンに置き換えて、次のコマンドを実行します。

```bash
npm pack com.microsoft.azure.object-anchors.runtime@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> 入手できる Azure Object Anchors パッケージのバージョンを一覧表示するには、次のコマンドを実行します。
>
> ```bash
> npm view com.microsoft.azure.object-anchors.runtime --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

コマンドを実行したフォルダーに、Azure Object Anchors パッケージがダウンロードされます。

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Mixed Reality Feature Tool (ベータ) を使用してインストールする](#tab/unity-package-mixed-reality-feature-tool)

次の手順に進みます。 後の手順で、<a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality Feature Tool</a> を使用します。

---