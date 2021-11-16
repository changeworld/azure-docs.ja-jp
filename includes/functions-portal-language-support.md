---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/23/2021
ms.author: glenga
ms.openlocfilehash: 8d580bbfd87f1d1df9d36be95a98c65d80736806
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224311"
---
## <a name="language-support-details"></a>言語サポートの詳細 

次の表は、Functions でサポートされているどの言語を、Linux または Windows で実行できるかを示しています。 また、お使いの言語で Azure portal での編集がサポートされているかどうかも示しています。 言語は、[Azure portal で関数アプリを作成](../articles/azure-functions/functions-create-function-app-portal.md#create-a-function-app)するときに選択した **ランタイム スタック** オプションに基づいています。 これは、Azure Functions Core Tools で `func init` コマンドを使用するときの `--worker-runtime` オプションと同じです。 

| Language | ランタイム スタック | Linux | Windows | ポータル内編集<sup>1</sup> |
|:--- |:-- |:--|:--- |:--- |
| [C# クラス ライブラリ](../articles/azure-functions/functions-dotnet-class-library.md)<sup>2</sup> |.NET|✓ |✓ | | 
| [C# スクリプト](../articles/azure-functions/functions-reference-csharp.md) | .NET | ✓ |✓ |✓ |
| [JavaScript](../articles/azure-functions/functions-reference-node.md) | Node.js |✓ |✓ | ✓ |
| [Python](../articles/azure-functions/functions-reference-python.md) | Python |✓ | | |
| [Java](../articles/azure-functions/functions-reference-java.md) | Java |✓ |✓ | |
| [PowerShell](../articles/azure-functions/functions-reference-powershell.md) |PowerShell Core | |✓ |✓ |
| [TypeScript](../articles/azure-functions/functions-reference-node.md) | Node.js |✓ |✓ |  |
| [Go/Rust/その他](../articles/azure-functions/functions-custom-handlers.md) | カスタム ハンドラー |✓ |✓ | |

<sup>1</sup> Linux 上で実行する場合、ポータル内編集は[専用 (App Service) プラン](../articles/azure-functions/dedicated-plan.md)でのみサポートされます。   
<sup>2</sup> ポータルでは、現在、.NET 5.0 で実行される関数アプリを作成できません。 詳細については、「[Azure Functions を使用した .NET 5 関数の開発と発行](../articles/azure-functions/dotnet-isolated-process-guide.md)」を参照してください。 

詳細については、[オペレーティング システム/ランタイムのサポート](../articles/azure-functions/functions-scale.md#operating-systemruntime)を参照してください。 

ポータル内編集を使用できない場合は、代わりに[関数をローカルで開発](../articles/azure-functions/functions-develop-local.md#local-development-environments)する必要があります。