---
title: Azure Service Bus - メッセージング エンティティを一時停止する
description: この記事では、Azure Service Bus メッセージ エンティティ (キュー、トピック、およびサブスクリプション) を一時停止し、再アクティブ化する方法について説明します。
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: f89e17e494cc777691b7f7ca47538cd29114d2dc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575252"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>メッセージ エンティティの一時停止と再アクティブ化 (無効化)

キュー、トピック、およびサブスクリプションは、一時停止することができます。 一時停止されたエンティティは無効な状態になり、すべてのメッセージがストレージに保持されます。 ただし、メッセージを削除または追加することはできず、各プロトコル操作はエラーになります。

通常、エンティティの一時停止は、緊急の管理上の理由で実行します。 たとえば、キューからメッセージを取る受信プログラムが配置されたがそれに欠陥があり、処理に失敗したにもかかわらず正しくない状態でメッセージが完了したため、それらを削除するというシナリオです。 その動作を診断した場合、欠陥のあるコードによるそれ以上のデータ損失を防止するため、修正されたコードが配置されるまで、キューの受信は無効化されます。

一時停止または再アクティブ化はユーザーまたはシステムのいずれかが実行できます。 システムは、サブスクリプションの使用制限を越えるなど重大な管理上の理由がある場合のみ、エンティティを一時停止します。 システムによって無効化されたエンティティは、ユーザーによって再アクティブ化することはできませんが、一時停止の原因が解消されれば復元されます。

## <a name="queue-status"></a>キューの状態 
キューに設定可能な状態は次のとおりです。

-   **Active**: キューはアクティブです。
-   **Disabled**: キューは一時停止状態です。 これは、**SendDisabled** と **ReceiveDisabled** の両方を設定することと同じです。 
-   **SendDisabled**: キューは、受信は許可されていますが、部分的に一時停止状態です。
-   **ReceiveDisabled**: キューは、送信は許可されていますが、部分的に一時停止状態です。

### <a name="change-the-queue-status-in-the-azure-portal"></a>Azure portal でキューの状態を変更します。 

1. Azure portal で、使用する Service Bus 名前空間に移動します。 
1. 状態を変更するキューを選択します。 下部のペインの中央にキューが表示されます。 
1. **[Service Bus キュー]** ページに、キューの現在の状態がハイパーリンクとして表示されます。 左側のメニューで **[概要]** を選択していない場合は、それを選択してキューの状態を表示します。 キューの現在の状態を変更するには、それを選択します。 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="キューの状態を選択する":::
4. キューの新しい状態を選択して、 **[OK]** を選択します。 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="キューの状態を選択する":::
    
ポータルでは、キューの完全無効化のみ実行できます。 .NET Framework SDK の Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API を使用するか、Azure CLI または Azure PowerShell 経由の Azure Resource Manager テンプレートを使用して、送信と受信操作を個別に無効化することもできます。

### <a name="change-the-queue-status-using-azure-powershell"></a>Azure PowerShell を使用してキューの状態を変更する
キューを無効にする PowerShell コマンドの例を次に示します。 再アクティブ化コマンドは同様ですが、`Status` が **Active** に設定されます。

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>トピックの状態
Azure portal でのトピックの状態の変更は、キューの状態の変更と似ています。 トピックの現在の状態を選択すると、次のページが表示され、状態を変更できます。 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="キューの状態を選択する":::

トピックに設定可能な状態は次のとおりです。
- **[アクティブ]** : トピックはアクティブです。
- **Disabled**: トピックは中断されています。
- **SendDisabled**: **Disabled** と同じ効果があります。

## <a name="subscription-status"></a>サブスクリプションの状態
Azure portal でのサブスクリプションの状態の変更は、トピックまたはキューの状態の変更と似ています。 サブスクリプションの現在の状態を選択すると、次のページが表示され、状態を変更できます。 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="キューの状態を選択する":::

トピックに設定可能な状態は次のとおりです。
- **[アクティブ]** : トピックはアクティブです。
- **Disabled**: トピックは中断されています。
- **ReceiveDisabled**: **Disabled** と同じ効果があります。

## <a name="other-statuses"></a>その他の状態
[EntityStatu](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 列挙型は、システムによってのみ設定可能な一連の遷移状態も定義します。 


## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

