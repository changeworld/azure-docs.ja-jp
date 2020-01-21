---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 59dd5d38b1cb89ce966a74284f0e392af52e9827
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457683"
---
## <a name="what-is-queue-storage"></a>Queue storage とは

Azure キュー ストレージは、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。 キューの 1 つのメッセージの最大サイズは 64 KB で、1 つのキューには、ストレージ アカウントの合計容量の上限に達するまで、数百万のメッセージを格納できます。 Queue storage は、多くの場合、非同期的な処理用に作業のバックログを作成するために使用されます。

## <a name="queue-service-concepts"></a>Queue サービスの概念

Azure Queue サービスには、次のコンポーネントが含まれます。

![Azure Queue サービス コンポーネント](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **URL 形式**: キューは、次の URL 形式を使用してアドレス指定できます:   http://`<storage account>`.queue.core.windows.net/`<queue>`
  
    次の URL を使用すると、図のいずれかのキューをアドレス指定できます。  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **ストレージ アカウント:** Azure のストレージにアクセスする場合には必ず、ストレージ アカウントを使用します。 ストレージ アカウントの詳細については、「[ストレージ アカウントの概要][../articles/storage/common/storage-account-overview.md]」を参照してください。
* **キュー:** キューは、メッセージのセットを格納します。 すべてのメッセージはキューに 格納されている必要があります。 キュー名は小文字で入力する必要があります。 キューの名前付け規則については、「 [Naming Queues and Metadata (キューとメタデータの名前付け規則)](https://msdn.microsoft.com/library/azure/dd179349.aspx)」を参照してください。
* **メッセージ:** 形式を問わず、メッセージのサイズは最大で 64 KB です。 メッセージをキューで保持できる最長時間は 7 日間です。 バージョン 2017-07-29 以降では、最大有効期間を任意の正の数にすることができます。また、-1 は、メッセージが期限切れにならないことを示します。 このパラメーターを省略すると、既定の有効期間は 7 日になります。

