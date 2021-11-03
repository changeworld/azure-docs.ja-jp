---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: 3c4a156f745b5c32e238a7865a9c26710baf7ed0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092015"
---
このチュートリアルの前提条件は次のとおりです。

* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    [!INCLUDE [azure-subscription-permissions](../../common-includes/azure-subscription-permissions.md)]
- [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能。
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [Python 3](https://www.python.org/downloads/) (3.6.9 以降)、[Pip 3](https://pip.pypa.io/en/stable/installing/)、必要に応じて [venv](https://docs.python.org/3/library/venv.html)。 

> [!Important]
> この Custom Vision モジュールでは、**Intel x86 および amd64** アーキテクチャだけがサポートされています。 続行する前に、エッジ デバイスのアーキテクチャを確認してください。

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](../../common-includes/azure-resources.md)]


