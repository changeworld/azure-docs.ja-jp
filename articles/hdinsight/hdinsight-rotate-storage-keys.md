---
title: Azure HDInsight で Azure ストレージ アカウント アクセス キーを更新する
description: Azure HDInsight クラスターで Azure ストレージ アカウント アクセス キーを更新する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/29/2021
ms.openlocfilehash: 9adfb5e438aec8625cd7c4b164f5999181d9c31f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725352"
---
# <a name="update-azure-storage-account-access-keys-in-hdinsight-cluster"></a>HDInsight クラスターで Azure ストレージ アカウント アクセス キーを更新する

この記事では、Azure HDInsight でプライマリまたはセカンダリ ストレージ アカウントの Azure ストレージ アカウント アクセス キーをローテーションする方法について説明します。

>[!CAUTION]
> ストレージ側でアクセス キーを直接ローテーションすると、HDInsight クラスターにアクセスできなくなります。

## <a name="prerequisites"></a>前提条件

* プロセス全体で HDInsight クラスターに確実にアクセスできるように、ストレージ アカウントのプライマリおよびセカンダリ アクセス キーを交互にずらしてローテーションするアプローチを使用します。

    プライマリおよびセカンダリ ストレージ アクセス キーを使用し、それらに対してローテーション ポリシーを設定する方法の例を次に示します。
    1. HDInsight クラスターの作成時に、ストレージ アカウントでアクセス キー 1 を使用します。
    1. N 日ごとにアクセス キー 2 のローテーション ポリシーを設定します。 このローテーションの一環として、アクセス キー 1 を使用するように HDInsight を更新してから、ストレージ アカウントでアクセス キー 2 をローテーションします。
    1. N/2 日ごとにアクセス キー 1 のローテーション ポリシーを設定します。 このローテーションの一環として、アクセス キー 2 を使用するように HDInsight を更新してから、ストレージ アカウントでアクセス キー 1 をローテーションします。
    1. 上記のアプローチでは、アクセス キー 1 は N/2、3N/2 日などの間隔でローテーションされ、アクセス キー 2 は N、2N、3N 日などの間隔でローテーションされます。

* ストレージ アカウント キーの定期的なローテーションを設定するには、[シークレットのローテーションの自動化](../key-vault/secrets/tutorial-rotation-dual.md)に関するページを参照してください。

## <a name="update-storage-account-access-keys"></a>ストレージ アカウント アクセス キーを更新する

[スクリプト アクション](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)を使用し、次の点を考慮して変更を適用します。

|プロパティ | 値 |
|---|---|
|Bash スクリプト URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/update-storage-account-v01.sh`|
|ノードの種類|Head|
|パラメーター|`ACCOUNTNAME` `ACCOUNTKEY` `-p` (省略可)|

* `ACCOUNTNAME` は HDInsight クラスター上のストレージ アカウントの名前です。
* `ACCOUNTKEY` は `ACCOUNTNAME` のアクセス キーです。
* `-p` はオプションです。 指定した場合、キーは暗号化されず、プレーンテキストとして core-site.xml ファイルに格納されます。

## <a name="known-issues"></a>既知の問題

前のスクリプトでは、クラスター側でのみアクセス キーが直接更新され、HDInsight リソース プロバイダー側のコピーは更新されません。 そのため、ストレージ アカウントでホストされているスクリプト アクションは、アクセス キーがローテーションされた後に失敗します。

回避策: スクリプト アクションに [SAS URI](hdinsight-storage-sharedaccesssignature-permissions.md) を使用するか、スクリプトにパブリックにアクセスできるようにします。

## <a name="next-steps"></a>次のステップ

* [追加のストレージ アカウントの追加](hdinsight-hadoop-add-storage.md)