---
title: ESP クラスター内の一部のユーザーに対して Sqoop import/export コマンドが失敗する - Azure HDInsight
description: 'Azure HDInsight ESP クラスター内の一部のユーザーに対して、Apache Sqoop import/export コマンドが失敗し、"インポートが失敗しました: java.io.IOException: ステージング ディレクトリ /user/yourusername/.staging 上の所有権が想定されたものではありません" というエラーが発生する'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 2eba44d735b1b8ea9a88164ede0ca4860efcce8a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283935"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>シナリオ: Azure HDInsight ESP クラスター内のユーザー名が 20 文字を超える場合、Sqoop import/export コマンドが失敗する

この記事では、ADLS Gen2 (ABFS) ストレージ アカウントを使用して Azure HDInsight ESP (Enterprise Security Pack) 対応のクラスターを使用する場合の既知の問題と回避策について説明します。

## <a name="issue"></a>問題

Sqoop import/export コマンドを実行すると、一部のユーザーについて以下のエラーが発生して失敗します。

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

上の例では、`/user/yourlongdomainuserna/.staging` には、ユーザー名 `yourlongdomainusername` に対して切り捨てられた 20 文字のユーザー名が表示されます。

## <a name="cause"></a>原因

ユーザー名の長さが 20 文字を超えています。 

詳細については、「[Azure Active Directory Domain Services のマネージド ドメイン内でのオブジェクトと資格情報の同期のしくみ](../active-directory-domain-services/synchronization.md)」を参照してください。

## <a name="workaround"></a>回避策

20 文字以下のユーザー名を使用してください。

## <a name="next-steps"></a>次の手順

[!INCLUDE [troubleshooting next steps](includes/hdinsight-troubleshooting-next-steps.md)]
