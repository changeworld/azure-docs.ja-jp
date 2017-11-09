---
title: "ファイルおよびフォルダー レベルでのユーザー アクセス許可の管理 - Azure HDInsight | Microsoft Docs"
description: "ドメイン参加済み HDInsight クラスターのファイルとフォルダーのアクセス許可を管理する方法"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 9ca91721e691eca239478c4ac8b85e2652babdfd
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2017
---
# <a name="manage-user-permissions-at-the-file-and-folder-levels"></a>ファイルおよびフォルダー レベルでのユーザー アクセス許可の管理

[ドメイン参加済み HDInsight クラスター](./domain-joined/apache-domain-joined-introduction.md)では、Azure Active Directory (Azure AD) ユーザーでの強力な認証が使用されます。また、YARN や Hive といった各種のサービスには、"*ロールベースのアクセス制御*" (RBAC) ポリシーが使用されます。 ご使用のクラスターの既定のデータ ストアが Azure Storage または Windows Azure Storage Blobs (WASB) である場合は、ファイル レベルやフォルダー レベルのアクセス許可を適用することもできます。 同期されている Azure AD のユーザーとグループに関しては、クラスターのファイルに対するアクセスを Apache Ranger を使用して制御できます。
<!-- [synchronized Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md). -->

HDInsight ドメイン参加済みクラスターの Apache Ranger インスタンスには、あらかじめ Ranger-WASB サービスが構成されています。 Ranger-WASB サービスは、Ranger-HDFS に似たポリシー管理エンジンですが、Ranger のアクセス ポリシーの適用に違いがあります。 Ranger-WASB サービスでは、受信リソース要求に一致する Ranger ポリシーがない場合、既定の応答が DENY になります。 Ranger サービスから WASB にアクセス許可のチェックが転嫁されることはありません。

## <a name="permission-and-policy-model"></a>アクセス許可とポリシー モデル

リソース アクセス要求は、次のフローに従って評価されます。

![Apache Ranger ポリシー評価フロー](./media/hdinsight-add-acls-at-file-folder-levels/ranger-policy-evaluation-flow.png)

先に DENY ルールが評価され、その後 ALLOW ルールが評価されます。 突き合わせの最後に、いずれのポリシーにも一致しない場合は、DENY が返されます。

### <a name="user-variable"></a>USER 変数

`{USER}` 変数は、たとえば `/{username}` サブディレクトリにアクセスするユーザーのポリシーを割り当てるときに使用できます。

```
resource: path=/app-logs/{USER}, user: {USER}, recursive=true, permissions: all, delegateAdmin=true
```

上記のポリシーでは、ユーザーに対し、`/app-logs/` ディレクトリ下にある自分のサブフォルダーへのアクセスを許可しています。 Ranger のユーザー インターフェイスからは、このポリシーが次のように見えます。

![USER 変数の使用例](./media/hdinsight-add-acls-at-file-folder-levels/user-variable.png)

### <a name="policy-model-examples"></a>ポリシー モデルの例

次の表は、ポリシー モデルが実際にどのように作用するかの例を一覧にしたものです。

| Ranger ポリシー | 既存のファイル システム | ユーザーと要求 | 結果 |
| -- | -- | -- | -- |
| /data/finance/、bob、WRITE | /data | bob、/data/finance/mydatafile.txt ファイルの作成 | ALLOW - 先祖チェックにより中間フォルダー "finance" が作成されます。 |
| /data/finance/、bob、WRITE | /data | alice、/data/finance/mydatafile.txt ファイルの作成 | DENY - 一致するポリシーがありません。 |
| /data/finance*、bob、WRITE | /data | bob、/data/finance/mydatafile.txt ファイルの作成 | ALLOW - この場合、オプションの再帰ポリシー (`*`) が存在します。「[ワイルドカード](#wildcards)」を参照してください。 |
| /data/finance/mydatafile.txt、bob、WRITE | /data | bob、/data/finance/mydatafile.txt ファイルの作成 | DENY - ポリシーが存在しないため、"/data" に対する先祖チェックに失敗します。 |
| /data/finance/mydatafile.txt、bob、WRITE | /data/finance | bob、/data/finance/mydatafile.txt ファイルの作成 | DENY - "/data/finance" に対する先祖チェックのポリシーがありません。 |

操作の種類に応じたフォルダー レベルまたはファイル レベルのアクセス許可が必要となります。 たとえば "read/open" の呼び出しではファイル レベルの読み取りアクセスが、"create" の呼び出しでは先祖フォルダー レベルのアクセス許可が必要となります。

### <a name="wildcards-"></a>ワイルドカード (*)

ポリシーのパスにワイルドカード (`*`) が存在する場合、該当するパスとそのサブツリー全体にワイルドカードが適用されます。 この再帰は `recurse-flag` の使用と同じです。 Ranger-WASB のワイルドカードは、名前の部分一致と再帰の両方を意味します。

## <a name="manage-file-and-folder-level-permissions-with-apache-ranger"></a>ファイル レベルとフォルダー レベルのアクセス許可を Apache Ranger で管理する

新しいドメイン参加済みクラスターをまだプロビジョニングしていない場合は、[こちらの手順](./domain-joined/apache-domain-joined-configure.md)に従ってプロビジョニングしてください。

ブラウザーで `https://<YOUR CLUSTER NAME>.azurehdinsight.net/ranger/` にアクセスして Ranger-WASB を開きます。 クラスターの作成時に定義したクラスタ アドミニストレーターのユーザー名とパスワードを入力します。

サインイン後、Ranger のダッシュボードが表示されます。

![Ranger のダッシュボード](./media/hdinsight-add-acls-at-file-folder-levels/ranger-dashboard.png)

クラスターに関連付けられている Azure ストレージ アカウントに関して、現在のファイルとフォルダーに対するアクセス許可を表示するには、[WASB] コントロール ボックスの ***<クラスター名>*_wasb** リンクをクリックします。

![Ranger のダッシュボード](./media/hdinsight-add-acls-at-file-folder-levels/wasb-dashboard-link.png)

現在のポリシー一覧が表示されます。 いくつかの標準的なポリシーが最初から含まれています。各ポリシーの細部をよく見てその用例を確認してください。

ポリシーが有効であるかどうか、監査ログが構成されているかどうか、また、どのグループとユーザーが割り当てられているかをポリシーごとに確認できます。 各ポリシーには、[編集] と [削除] の 2 つのアクション ボタンがあります。

![ポリシー一覧](./media/hdinsight-add-acls-at-file-folder-levels/policy-list.png)

### <a name="adding-a-new-policy"></a>新しいポリシーの追加

1. WASB ポリシー ページの右上にある **[新しいポリシーの追加]** を選択します。

    ![新しいポリシーの追加](./media/hdinsight-add-acls-at-file-folder-levels/add-new.png)

2. わかりやすい**ポリシー名**を入力します。 対象クラスターの Azure **ストレージ アカウント** (*<アカウント名>*.blob.core.windows.net) と、クラスターの作成時に指定した**ストレージ アカウント コンテナー**を指定します。 アクセスするフォルダーまたはファイルの (クラスターを基準とする) **相対パス**を入力します。

    ![ポリシーの新規作成フォーム](./media/hdinsight-add-acls-at-file-folder-levels/new-policy.png)

3. このフォームの下で、この新しいリソースに対する**許可の条件**を指定します。 該当するグループとユーザーを選択し、そのアクセス許可を設定します。 次の例では、`sales` グループのすべてのユーザーに読み取り/書き込みアクセス許可を付与しています。

    ![sales を許可](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales.png)

4. **[ 保存]** を選択します。

### <a name="example-policy-conditions"></a>ポリシーの条件の例

Apache Ranger の[ポリシー評価フロー](#permission-and-policy-model)では、実際の要件に応じて、許可と拒否の条件を自由に組み合わせて指定することができます。 次に例をいくつか示します。

1. interns を除くすべての sales ユーザーを許可するには、次のようにします。

    ![sales を許可、interns を拒否](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns.png)

2. すべての sales ユーザーを許可し、すべての interns を拒否したうえで、"hiveuser3" という名前のインターンについてのみ、例外的に読み取りアクセスを許可するには、次のようにします。

    ![sales を許可、hiveuser3 を除く interns を拒否](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns-except-hiveuser3.png)

## <a name="next-steps"></a>次のステップ

* [ドメイン参加済み HDInsight での Hive ポリシーの構成](./domain-joined/apache-domain-joined-run-hive.md)
* [ドメイン参加済み HDInsight クラスターの管理](./domain-joined/apache-domain-joined-manage.md)
* [Ambari の管理 - Ambari に対するユーザーの承認](hdinsight-authorize-users-to-ambari.md)

<!-- * [Synchronize Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md) -->

