---
title: ドメイン参加済み HDInsight クラスターの管理 - Azure | Microsoft Docs
description: ドメイン参加済み HDInsight クラスターを管理する方法について説明します
services: hdinsight
documentationcenter: ''
author: bprakash
manager: jhubbard
editor: cgronlun
tags: ''
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2018
ms.author: bhanupr
ms.openlocfilehash: 44202541557a7513e0068f52289a637f6e48f43f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="manage-domain-joined-hdinsight-clusters"></a>ドメイン参加済み HDInsight クラスターの管理
ドメイン参加済み HDInsight のユーザーとロールについて説明し、ドメイン参加済み HDInsight クラスターの管理方法についても説明します。

## <a name="access-the-clusters-with-enterprise-security-package"></a>Enterprise セキュリティ パッケージ付きのクラスターへのアクセス

Enterprise セキュリティ パッケージ (旧称 HDInsight Premium) は、クラスターに対する複数ユーザーのアクセスを提供します。アクセスの際の認証は Active Directory によって行なわれ、承認は Apache Ranger と Storage ACL (ADLS ACL) によって行なわれます。 承認は、複数ユーザー間のセキュリティで保護された境界を提供し、承認ポリシーに基づく特権を持つユーザーのみがデータにアクセスすることを許可します。

セキュリティとユーザーの分離は、Enterprise セキュリティ パッケージがある HDInsight クラスターでは重要です。 これらの要件を満たすために、Enterprise セキュリティ パッケージがあるクラスターへの SSH アクセスはブロックされます。 次の表は、各クラスターの種類に対して推奨されるアクセス方法を示しています。

|ワークロード|シナリオ|アクセス方法|
|--------|--------|-------------|
|Hadoop|Hive – 対話型ジョブ/クエリ |<ul><li>[Beeline](#beeline)</li><li>[Hive ビュー](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|対話型ジョブ/クエリ、PySpark 対話型|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin + Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive ビュー](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|バッチ シナリオ – spark-submit、PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interactive Query (LLAP)|対話|<ul><li>[Beeline](#beeline)</li><li>[Hive ビュー](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|任意|カスタム アプリケーションのインストール|<ul><li>[スクリプト アクション](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|


標準 API の使用は、セキュリティの観点から有用です。 さらに、次の利点があります。

1.  **管理** – 標準 API を使用して、コードの管理とジョブの自動化を実行できます (Livy や HS2 など)。
2.  **監査** – SSH では、どのユーザーがクラスターにSSH 通信したかを示す監査方法はありません。 これは、ジョブがユーザーのコンテキストで実行されるため、標準エンドポイント経由で構築される場合に該当しません。 



### <a name="beeline"></a>Beeline を使用する 
Beeline をローカルにインストールし、次のパラメーターを使用してパブリック インターネット経由で接続します。 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Beeline をローカルにインストールしている場合に Azure 仮想ネットワーク経由で接続するときは、次のパラメーターを使用します。 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

ヘッドノードの完全修飾ドメイン名を検索するには、Ambari REST API を使用した HDInsight の管理に関するドキュメントの情報を使用してください。














## <a name="users-of-domain-joined-hdinsight-clusters"></a>ドメイン参加済み HDInsight クラスターのユーザー
ドメインに参加していない HDInsight クラスターには、クラスターの作成中に作成される、次の 2 つのユーザー アカウントがあります。

* **Ambari 管理者**: このアカウントは、"*Hadoop ユーザー*" または "*HTTP ユーザー*" とも呼ばれます。 このアカウントは、Ambari (https://&lt;clustername>.azurehdinsight.net) にログオンするために使用できます。 また、Ambari ビューに対するクエリの実行、外部ツール (PowerShell、Templeton、Visual Studio など) によるジョブの実行、Hive ODBC ドライバーと BI ツール (Excel、PowerBI、Tableau など) による認証にも使用できます。

ドメイン参加済み HDInsight クラスターには、Ambari 管理者以外に、3 つの新しいユーザーの種類があります。

* **Ranger 管理者**: このアカウントは、ローカルの Apache Ranger 管理者アカウントです。 これは、Active Directory ドメイン ユーザーではありません。 このアカウントは、ポリシーのセットアップや、他のユーザー管理者または代理管理者の作成に使用することができます (それらの管理者がポリシーを管理できるようにするため)。 既定では、ユーザー名は *admin* で、パスワードは Ambari 管理者パスワードと同じです。 パスワードは、Ranger の [Settings (設定)] ページで更新することができます。
* **クラスター管理者ドメイン ユーザー**: このアカウントは、Ambari と Ranger を含む Hadoop クラスター管理者として指定された Active Directory ドメイン ユーザーです。 クラスターの作成中に、このユーザーの資格情報を指定する必要があります。 このユーザーは、次の特権を持ちます。

  * コンピューターをドメインに参加させ、クラスターの作成中に指定する OU 内に配置します。
  * クラスターの作成中に指定する OU 内にサービス プリンシパルを作成します。
  * 逆引き DNS エントリを作成します。

    他の AD ユーザーもこれらの特権を持つことに注意してください。

    クラスター内には、Ranger によって管理されないエンドポイント (たとえば Templeton) がいくつかあります。そのため、それらは安全ではありません。 これらのエンドポイントは、クラスター管理者ドメイン ユーザーを除くすべてのユーザーに対してロックダウンされます。
* **レギュラー**: クラスターの作成中に、複数の Active Directory グループを指定することができます。 これらのグループのユーザーは、Ranger と Ambari に同期されます。 これらのユーザーはドメイン ユーザーであり、Ranger によって管理されているエンドポイント (たとえば Hiveserver2) のみにアクセスできます。 すべての RBAC ポリシーと監査は、これらのユーザーに適用できます。

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>ドメイン参加済み HDInsight クラスターのロール
ドメイン参加済み HDInsight には、次のロールがあります。

* クラスター管理者
* クラスター オペレーター
* サービス管理者
* サービス オペレーター
* クラスター ユーザー

**これらのロールのアクセス許可を確認するには**

1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューで **[Roles (ロール)]** をクリックします。
3. 青い疑問符をクリックして、アクセス許可を表示します。

    ![ドメイン参加済み HDInsight のロールのアクセス許可](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Ambari Management UI を開く

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. HDInsight クラスターを開きます。 「[クラスターの一覧と表示](../hdinsight-administer-use-management-portal.md#list-and-show-clusters)」を参照してください。
3. 上部のメニューで **[ダッシュボード]** をクリックして、Ambari を開きます。
4. クラスター管理者のドメイン ユーザー名とパスワードを使用して、Ambari にログオンします。
5. 右上隅のドロップダウン メニューで **[Admin (管理者)]** をクリックし、**[Manage Ambari (Ambari の管理)]** をクリックします。

    ![ドメイン参加済み HDInsight での Ambari の管理](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    次のような UI になります。

    ![ドメイン参加済み HDInsight での Ambari Management UI](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Active Directory から同期されているドメイン ユーザーの一覧表示
1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューで **[ユーザー]**をクリックします。 Active Directory から HDInsight クラスターに同期されているすべてのユーザーが表示されます。

    ![ドメイン参加済み HDInsight での Ambari Management UI によるユーザーの一覧表示](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Active Directory から同期されているドメイン グループの一覧表示
1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューで **[Groups (グループ)]** をクリックします。 Active Directory から HDInsight クラスターに同期されているすべてのグループが表示されます。

    ![ドメイン参加済み HDInsight での Ambari Management UI によるグループの一覧表示](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Hive ビューのアクセス許可の構成
1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューで **[Views (ビュー)]** をクリックします。
3. **[HIVE]** をクリックして、詳細を表示します。

    ![ドメイン参加済み HDInsight での Ambari Management UI による Hive ビュー](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. **[Hive View (Hive ビュー)]** リンクをクリックして、Hive ビューを構成します。
5. **[Permissions (アクセス許可)]** セクションまで下へスクロールします。

    ![ドメイン参加済み HDInsight での Ambari Management UI による Hive ビューのアクセス許可の構成](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. **[Add User (ユーザーの追加)]** または **[Add Group (グループの追加)]** をクリックし、Hive ビューを使用するユーザーまたはグループを指定します。

## <a name="configure-users-for-the-roles"></a>ロールのユーザーの構成
 ロールとそのアクセス許可の一覧を表示するには、「[ドメイン参加済み HDInsight クラスターのロール](#roles-of-domain---joined-hdinsight-clusters)」を参照してください。

1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューで **[Roles (ロール)]** をクリックします。
3. **[Add User (ユーザーの追加)]** または **[Add Group (グループの追加)]** をクリックし、別のロールにユーザーやグループを割り当てます。

## <a name="next-steps"></a>次の手順
* ドメイン参加済み HDInsight クラスターの構成については、[ドメイン参加済み HDInsight クラスターの構成](apache-domain-joined-configure.md)に関する記事を参照してください。
* Hive ポリシーの構成と Hive クエリの実行については、[ドメイン参加済み HDInsight クラスターの Hive ポリシーの構成](apache-domain-joined-run-hive.md)に関する記事をご覧ください。
