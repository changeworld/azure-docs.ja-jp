---
title: Azure Spring Cloud サービス インスタンスを開始または停止する方法
description: Azure Spring Cloud サービス インスタンスを開始または停止する方法について説明します
author: karlerickson
ms.author: wepa
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 010c117218edccd31fb397785358a35c1774754a
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027491"
---
# <a name="start-or-stop-your-azure-spring-cloud-service-instance"></a>Azure Spring Cloud サービス インスタンスを開始または停止する

この記事では、Azure Spring Cloud サービス インスタンスを開始または停止する方法について説明します。

> [!NOTE]
> 停止と開始は現在プレビュー段階にあります。

Azure Spring Cloud で実行されているアプリケーションは、継続的に実行する必要がない場合があります。たとえば、営業時間中にのみ使用されるサービス インスタンスがある場合などです。 このようなとき、Azure Spring Cloud はアイドル状態になり、システム コンポーネントのみ実行していることがあります。

実行中のインスタンスを減らし、コンピューティング リソースのコストを確実に削減することで、Azure Spring Cloud のアクティブ フットプリントを削減できます。

コストをさらに削減するために、Azure Spring Cloud サービス インスタンスを完全に停止できます。 すべてのユーザー アプリとシステム コンポーネントが停止します。 ただし、すべてのオブジェクトとネットワーク設定が保存されるのため、サービス インスタンスを再起動して、停止したところからすぐに作業を再開できます。

> [!NOTE]
> 停止した Azure Spring Cloud サービス インスタンスの状態は、プレビュー期間中は最大 90 日間保持されます。 クラスターの停止期間が 90 日間を超えた場合、クラスターの状態を回復することはできません。
> 最大停止期間は、プレビュー後に変更される可能性があります。

停止した Azure Spring Cloud サービス インスタンスは開始、表示、または削除のみ行うことができます。 アプリの作成や拡大/縮小などの更新操作を実行するには、その前にサービス インスタンスを開始する必要があります。

## <a name="prerequisites"></a>前提条件

- Azure Spring Cloud の既存のサービス　インスタンス。 新しいサービス インスタンスを作成するには、「[クイック スタート: 初めてのアプリケーションを Azure Spring Cloud にデプロイする](./quickstart.md)」を参照してください。
- (オプション) [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.11.2 以降。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

## <a name="stop-a-running-instance"></a>実行中のインスタンスを停止する

Azure portal で次の手順を使用して、実行中の Azure Spring Cloud インスタンスを停止します。

1. Azure Spring Cloud サービスの概要ページに移動します。
2. **[停止]** を選択して、実行中のインスタンスを停止します。

   :::image type="content" source="media/stop-start-service/spring-cloud-stop-service.png" alt-text="[停止] ボタンと [状態] の値が強調表示されている Azure Spring Cloud の[概要] ページを示す Azure portal のスクリーンショット":::

3. インスタンスが停止すると、状態は **[成功 (停止済み)]** と表示されます。

## <a name="start-a-stopped-instance"></a>停止したインスタンスを開始する

Azure portal で次の手順を使用して、停止した Azure Spring Cloud インスタンスを開始します。

1. Azure Spring Cloud サービスの概要ページに移動します。
2. 停止したインスタンスを開始するには、 **[開始]** を選択します。

   :::image type="content" source="media/stop-start-service/spring-cloud-start-service.png" alt-text="[開始] ボタンと [状態] の値が強調表示されている Azure Spring Cloud の[概要] ページを示す Azure portal のスクリーンショット":::

3. インスタンスが開始すると、状態は **[成功 (実行中)]** と表示されます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="stop-a-running-instance"></a>実行中のインスタンスを停止する

Azure CLI で次のコマンドを使用して、実行中の Azure Spring Cloud インスタンスを停止します。

```azurecli
az spring-cloud stop \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

## <a name="start-a-stopped-instance"></a>停止したインスタンスを開始する

Azure CLI で次のコマンドを使用して、停止した Azure Spring Cloud インスタンスを開始します。

```azurecli
az spring-cloud start \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

## <a name="check-the-power-state"></a>電源の状態を確認する

インスタンスが停止または開始したら、次のコマンドを使用して電源の状態を確認します。

```azurecli
az spring-cloud show \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

---

## <a name="next-steps"></a>次のステップ

* [Azure Activity ログと Azure Service Health で、アプリのライフサイクル イベントを監視する](./monitor-app-lifecycle-events.md)
* [Azure Monitor での使用量と推定コストの監視](../azure-monitor/usage-estimated-costs.md)
