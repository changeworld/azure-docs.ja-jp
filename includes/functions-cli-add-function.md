---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a33cc604d45d51a63a73ea6ed58abc67269437d7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673134"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
次のコマンドを使用して、関数をプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前 (HttpExample) で、`--template` 引数は関数のトリガー (HTTP) を指定します。 

```
func new --name HttpExample --template "HTTP trigger"
```  
::: zone-end  
::: zone pivot="programming-language-csharp"
`func new` によって、HttpExample.cs コード ファイルが作成されます。
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
`func new` によって、関数と同じ名前のサブフォルダーが作成されます。ここには、プロジェクト用に選択した言語に適したコード ファイルと、*function.json* という名前の構成ファイルが含まれます。
::: zone-end