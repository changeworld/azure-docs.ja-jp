---
title: Apache Hive ビューにエラー メッセージが表示されない - Azure HDInsight
description: Azure HDInsight クラスターの Apache Hive ビューで詳細情報が表示されずに、クエリが失敗します。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2f80a966b3d326d367d376354bc83f7d1d935812
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290852"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>シナリオ: Azure HDInsight の Apache Hive ビューにクエリのエラー メッセージが表示されない

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive ビューのクエリのエラー メッセージが次のように表示され、詳細情報がありません。

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>原因

クエリ エラーのエラー メッセージが大きすぎて、Hive ビューのメイン ページに表示できないことがあります。

## <a name="resolution"></a>解決方法

Hive_view の右上にある [通知] タブで、完全なスタックトレースとエラー メッセージを確認します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]