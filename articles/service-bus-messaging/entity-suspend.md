---
title: Azure Service Bus - メッセージング エンティティを一時停止する
description: この記事では、Azure Service Bus メッセージ エンティティ (キュー、トピック、およびサブスクリプション) を一時停止し、再アクティブ化する方法について説明します。
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 7386932f19eee064926184eb17f5e92e30add98e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760387"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>メッセージ エンティティの一時停止と再アクティブ化 (無効化)

キュー、トピック、およびサブスクリプションは、一時停止することができます。 一時停止されたエンティティは無効な状態になり、すべてのメッセージがストレージに保持されます。 ただし、メッセージを削除または追加することはできず、各プロトコル操作はエラーになります。

通常、エンティティの一時停止は、緊急の管理上の理由で実行します。 たとえば、キューからメッセージを取る受信プログラムが配置されたがそれに欠陥があり、処理に失敗したにもかかわらず正しくない状態でメッセージが完了したため、それらを削除するというシナリオです。 その動作を診断した場合、欠陥のあるコードによるそれ以上のデータ損失を防止するため、修正されたコードが配置されるまで、キューの受信は無効化されます。

一時停止または再アクティブ化はユーザーまたはシステムのいずれかが実行できます。 システムは、サブスクリプションの使用制限を越えるなど重大な管理上の理由がある場合のみ、エンティティを一時停止します。 システムによって無効化されたエンティティは、ユーザーによって再アクティブ化することはできませんが、一時停止の原因が解消されれば復元されます。

ポータルの各エンティティの **[プロパティ]** セクションで状態を変更できます。以下のスクリーンショットはキューの切り替えを示しています。

![][1]

ポータルでは、キューの完全無効化のみ実行できます。 .NET Framework SDK の Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API を使用するか、Azure CLI または Azure PowerShell 経由の Azure Resource Manager テンプレートを使用して、送信と受信操作を個別に無効化することもできます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>一時停止状態

キューに設定可能な状態は次のとおりです。

-   **[アクティブ]** : キューはアクティブです。
-   **Disabled**: キューは一時停止状態です。
-   **SendDisabled**: キューは、受信は許可されていますが、部分的に一時停止状態です。
-   **ReceiveDisabled**: キューは、送信は許可されていますが、部分的に一時停止状態です。

サブスクリプションとトピックについては、**Active** と **Disabled** のみ設定できます。

[EntityStatu](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 列挙型は、システムによってのみ設定可能な一連の遷移状態も定義します。 キューを無効にする PowerShell コマンドの例を次に示します。 再アクティブ化コマンドは同様ですが、`Status` が **Active** に設定されます。

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

