---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/15/2021
ms.author: glenga
ms.openlocfilehash: 33a4b0f7d25162cf258e9ef6ad4ee438d6b76c8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741372"
---
## <a name="local-settings"></a>ローカル設定

Azure の関数アプリで実行する場合、関数に必要な設定は[アプリ設定に安全に保存](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#settings)されます。 ローカル開発中は、これらの設定はその代わりに local.settings.json ファイルの `Values` オブジェクトに追加されます。 local.settings.json ファイルには、ローカルの開発ツールによって使用される設定も格納されます。 

local.settings.json には接続文字列などのシークレットが含まれている場合があるため、リモート リポジトリには絶対に格納しないようにしてください。 ローカル設定の詳細については、「[ローカル設定ファイル](../articles/azure-functions/functions-develop-local.md#local-settings-file)」を参照してください。