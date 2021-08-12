---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: 168d85d3c70564a5d18fda38785b0d4338105fe1
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113735487"
---
このチュートリアルの前提条件は次のとおりです。


* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、無料の[アカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    [!INCLUDE [azure-subscription-permissions](../../common-includes/azure-subscription-permissions.md)]
* [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能。
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)    
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。 

> [!Important]
> この Custom Vision モジュールでは、**Intel x86 および amd64** アーキテクチャだけがサポートされています。 続行する前に、エッジ デバイスのアーキテクチャを確認してください。

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](../../common-includes/azure-resources.md)]
