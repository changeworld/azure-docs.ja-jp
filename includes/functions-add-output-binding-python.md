---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 9c3dd4ecede4e8087b2ec9ad470d5ab415ea6e35
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803374"
---
次のコードに合わせて *HttpExample\\\_\_init\_\_.py* を更新します。関数の定義に `msg` パラメーターを、`if name:` ステートメントの下に `msg.set(name)` を追加してください。

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

`msg` パラメーターは、[`azure.functions.Out class`](/python/api/azure-functions/azure.functions.out) のインスタンスです。 その `set` メソッドでは、文字列メッセージ (この場合、URL のクエリ文字列として関数に渡された名前) をキューに書き込みます。
