---
title: 同時実行制御 | Azure Marketplace
description: Microsoft Cloud パートナー ポータルの発行 API の同時実行制御戦略。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d0f035d77e74f157b793b9edf3ab5d3494096d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288650"
---
# <a name="concurrency-control"></a>同時実行制御

Microsoft Cloud パートナー ポータルの発行 API を呼び出すごとに、使用する同時実行制御戦略を明示的に指定する必要があります。 **If-Match** ヘッダーを指定しない場合、HTTP 400 エラー応答になります。 次の 2 つの同時実行制御の戦略が用意されています。

-   **オプティミスティック** - 更新プログラムを実行するクライアントは、前回データが読み取られてからデータに変更があったかどうかを確認します。
-   **ラスト ワン ウィン** - クライアントは、最後の読み取り時以降に別のアプリケーションによって変更されたかどうかには関係なく、データを直接更新します。

<a name="optimistic-concurrency-workflow"></a>オプティミスティック同時実行ワークフロー
-------------------------------

次のワークフローでオプティミスティック同時実行戦略を使用して、予期しない編集がリソースに行われていないことを保証します。

1.  API を使用して、エンティティを取得します。 応答には、(応答時点の) エンティティの現在格納されているバージョンを識別する ETag 値が含まれています。
2.  更新時、この同じ ETag 値を必須の **If-Match** 要求ヘッダーに含めます。
3.  API によって、要求に受信された ETag の値とエンティティの現在の ETag の値がアトミック トランザクションで比較されます。
    *   ETag 値が異なる場合、API は `412 Precondition Failed` HTTP 応答を返します。 このエラーは、エンティティが前回クライアントにより取得された後に別のプロセスにより更新されたか、要求で指定されている ETag 値が正しくないことを示します。
    *  ETag 値が同じ場合、または **If-Match** ヘッダーにアスタリスクのワイルドカード文字 (`*`) が含まれている場合は、API は要求された操作を実行します。 API 操作では、エンティティの格納されている ETag 値も更新されます。


> [!NOTE]
> **If-Match** ヘッダーでワイルドカード文字 (*) を指定すると、API はラスト ワン ウィン同時実行戦略を使用します。 この場合、ETag の比較は行われず、リソースはチェックせずに更新されます。 
