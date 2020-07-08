---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 9e9f9c71701ceb1c76bc162f22e166b4565e731b
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86062684"
---
## <a name="configure-your-local-environment"></a>ローカル環境を構成する

開始する前に、次の項目を用意する必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) バージョン 2.7.1846 以降の 2.x バージョン。
::: zone-end  
::: zone pivot="programming-language-python"
+ インストール済みの Python バージョンに対応する Azure Functions Core Tools バージョン。

   | Python バージョン | Core Tools バージョン |
   | -------------- | ------------------ |
   | Python 3.8     | [バージョン 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [バージョン 2.7.1846 以降](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 ビット)](https://www.python.org/downloads/release/python-382/)、[Python 3.7 (64 ビット)](https://www.python.org/downloads/release/python-375/)、[Python 3.6 (64 ビット)](https://www.python.org/downloads/release/python-368/)。これらは Azure Functions でサポートされています。 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2 以上](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks)、バージョン 8。

+ [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。

> [!IMPORTANT]
> このクイックスタートを行うには、JAVA_HOME 環境変数を JDK のインストール場所に設定する必要があります。
::: zone-end
