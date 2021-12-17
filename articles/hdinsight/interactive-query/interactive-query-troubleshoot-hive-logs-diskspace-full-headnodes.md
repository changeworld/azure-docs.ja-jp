---
title: 'トラブルシューティング: Hive ログによってディスク領域がいっぱいになる Azure HDInsight'
description: この記事では、Apache Hive ログによって Azure HDInsight 内のヘッド ノード上のディスク領域がいっぱいになったときのトラブルシューティング手順について説明します。
ms.service: hdinsight
ms.topic: troubleshooting
author: kcheeeung
ms.author: kecheung
ms.date: 05/21/2021
ms.openlocfilehash: f6e2deac6c5f5807618225f4afc208e091e7e004
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578691"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>シナリオ:Apache Hive ログによって Azure HDInsight のヘッド ノードのディスク領域がいっぱいになる

この記事では、Azure HDInsight クラスターのヘッド ノードのディスク領域不足に関連する問題のトラブルシューティングの手順と可能な解決策について説明します。

## <a name="issue"></a>問題

HDI 4.0 Apache Hive/LLAP クラスターで、不要なログによってヘッド ノードのディスク領域全体が占有されている。 この状態により、次の問題が発生する可能性があります。

- ヘッド ノードに空き領域がないため、SSH アクセスが失敗する。
- HiveServer2 Interactive が再起動に失敗する

## <a name="cause"></a>原因

Hive ログの自動削除は、高度な hive-log4j2 構成では構成されていません。 60 GB の既定のサイズ制限では、顧客の使用パターンに対して多くの領域が必要です。

## <a name="resolution"></a>解像度

1. Ambari ポータルの Hive コンポーネントの概要に移動し、 **[Configs]\(構成\)** タブを選択します。

2. **[Advanced settings]\(高度な設定\)** の [`Advanced hive-log4j2`]\(高度な hive-log4j\) セクションに移動します。

3. 次の設定になっていることを確認してください。 関連する設定が表示されない場合は、次の設定を追加します。
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfFileName
    appender.RFA.strategy.action.condition.regex = hive*.*log.*
    appender.RFA.strategy.action.condition.nested_condition.type = IfAny
    # Deletes logs based on total accumulated size, keeping the most recent
    appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize
    appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days
    #appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified
    #appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```

4. 次の 3 つの基本的なオプションを削除します。
- **総サイズ**
    - 選択したサイズ制限に `appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds` を変更します。

- **日付**
    - また、条件の使用を解除して切り替えもできます。 次に `appender.RFA.strategy.action.condition.nested_condition.lastMod.age` を選択した年齢に変更します。

    ```
    # Deletes logs based on total accumulated size, keeping the most recent 
    #appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize 
    #appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```

- **合計サイズと日付の組み合わせ**
    - 両方のオプションを組み合わせるには、次のようにコメント解除します。 その後、log4j2 は次のように動作します。いずれかの条件が満たされた場合にログの削除を開始します。
    
    ```
    # Deletes logs based on total accumulated size, keeping the most recent 
    appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize 
    appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```
5. 構成を保存し、必要なコンポーネントを再起動します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
