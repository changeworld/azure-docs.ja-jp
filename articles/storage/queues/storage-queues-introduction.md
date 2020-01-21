---
title: Azure キューの概要 - Azure Storage
description: Azure キューの概要
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/07/2019
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: cbrooks
ms.openlocfilehash: 0e8bac8344bec06b58a22b8c9162cd8bd22ee700
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750436"
---
# <a name="what-are-azure-queues"></a>Azure キューとは

Azure Queue storage は、多数のメッセージを格納するためのサービスです。 メッセージには、HTTP または HTTPS を使用して、認証された呼び出しを介して世界中のどこからでもアクセスできます。 キュー メッセージの許容される最大サイズは 64 KB です。 キューには、ストレージ アカウントの総容量の上限を超えない限り、数百万のメッセージを含めることができます。 キューは通常、非同期的な処理用に作業のバックログを作成するために使用されます。

## <a name="queue-service-concepts"></a>Queue サービスの概念

キュー サービスには、次のコンポーネントが含まれます。

![キューの概念](./media/storage-queues-introduction/queue1.png)

* **URL 形式**: キューは、次の URL 形式を使用してアドレス指定できます。

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    次の URL を使用すると、図のいずれかのキューをアドレス指定できます。  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **ストレージ アカウント:** Azure のストレージにアクセスする場合には必ず、ストレージ アカウントを使用します。 ストレージ アカウントの容量の詳細については、「[Standard Storage アカウントのスケーラビリティとパフォーマンスのターゲット](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)」を参照してください。

* **キュー:** キューは、メッセージのセットを格納します。 キュー名は小文字で指定する**必要があります**。 キューの名前付け規則については、「 [Naming Queues and Metadata (キューとメタデータの名前付け規則)](https://msdn.microsoft.com/library/azure/dd179349.aspx)」を参照してください。

* **メッセージ:** 形式を問わず、メッセージのサイズは最大で 64 KB です。 バージョン 2017-07-29 より前では、許可される最大有効期間は 7 日間です。 バージョン 2017-07-29 以降では、最大有効期間を任意の正の数にすることができます。また、-1 は、メッセージが期限切れにならないことを示します。 このパラメーターを省略すると、既定の有効期間は 7 日になります。

## <a name="next-steps"></a>次のステップ

* [ストレージ アカウントの作成](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [.NET を使用するキューの概要](storage-dotnet-how-to-use-queues.md)
