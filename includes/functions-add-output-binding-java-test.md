---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: eb54439f89cc2443eeed2d3b63dfbe7fedb4bf17
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673504"
---
## <a name="update-the-tests"></a>テストを更新する

アーキタイプはテストのセットも作成するため、`run` メソッド シグネチャ内の新しい `msg` パラメーターを処理するためにこれらのテストを更新する必要があります。  

_src/main/java_ の下の対象のテスト コードの場所を参照し、*Function.java* プロジェクト ファイルを開きます。`//Invoke` の下のコード行を次のコードに置き換えます。

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
