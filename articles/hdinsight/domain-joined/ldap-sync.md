---
title: Azure HDInsight の Ranger と Apache Ambari での LDAP 同期
description: Ranger と Ambari での LDAP 同期について紹介し、一般的なガイドラインを提供します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77465668"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight の Ranger と Apache Ambari での LDAP 同期

HDInsight Enterprise セキュリティ パッケージ (ESP) クラスターでは、承認に Ranger を使用します。 Apache Ambari と Ranger は、どちらもユーザーとグループを別々に同期しますが、少し動作が異なります。 この記事は、Ranger と Ambari での LDAP 同期について紹介することを目的としています。

## <a name="general-guidelines"></a>一般的なガイドライン

* クラスターをデプロイする場合は常にグループを使用します。
* Ambari と Ranger でグループ フィルターを切り替える代わりに、Azure AD でこれらすべてを管理し、入れ子になったグループを使用して必要なユーザーを取り込むようにします。
* 同期されたユーザーは、グループの一部でなくなった場合でも削除されません。
* LDAP フィルターを直接変更する必要がある場合は、UI を最初に使用します。これは、UI にいくつかの検証が含まれているためです。

## <a name="users-are-synced-separately"></a>ユーザーは個別に同期される

Ambari と Ranger は、2 つの異なる目的で使用されるため、ユーザー データベースを共有しません。 ユーザーが Ambari UI を使用する必要がある場合は、そのユーザーを Ambari に同期する必要があります。 ユーザーが Ambari に同期されていない場合、Ambari UI または API はこのユーザーを拒否しますが、システムの他の部分は動作します (これらは Ambari ではなく Ranger または Resource Manager によって保護されます)。 ユーザーを Ranger ポリシーに含める場合は、ユーザーを Ranger に同期します。

セキュリティで保護されたクラスターがデプロイされると、グループ メンバーは推移的に Ambari と Ranger の両方に同期されます (すべてのサブグループとそのメンバー)。 

## <a name="ambari-user-sync-and-configuration"></a>Ambari のユーザー同期と構成

ヘッド ノードから、ユーザー同期をスケジュールするための cron ジョブ `/opt/startup_scripts/start_ambari_ldap_sync.py` が 1 時間ごとに実行されます。この cron ジョブにより、Ambari REST API シリーズが呼び出され、同期が実行されます。このスクリプトにより、同期するユーザーとグループのリストが送信されます (ユーザーは指定されたグループに属していない可能性があるため、どちらも個別に指定されています)。 Ambari によって、ユーザー名としての sAMAccountName およびすべてのグループ メンバーが推移的に同期されます。

ログは `/var/log/ambari-server/ambari-server.log` にあります。 詳細については、「[Ambari のログ レベルの構成](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)」を参照してください。

Data Lake クラスターでは、ユーザー作成後のフックを使用して、同期されたユーザーのホーム フォルダーが作成され、これらのユーザーがホーム フォルダーの所有者として設定されます。 ユーザーが Ambari に正しく同期されていない場合、ステージングおよびその他の一時フォルダーへのアクセスするときにエラーが発生する可能性があります。

### <a name="update-groups-to-be-synced-to-ambari"></a>Ambari に同期するグループを更新する

Azure AD でグループ メンバーシップを管理できない場合は、次の 2 つの選択肢があります。

* 「[LDAP ユーザーとグループを同期する](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html)」の詳しい説明に従って、1 回限りの同期を実行します。 グループ メンバーシップが変更されるたびに、この同期を再度行う必要があります。

* cron ジョブを作成し、新しいグループで [Ambari API を定期的に](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634)呼び出します。

## <a name="ranger-user-sync-and-configuration"></a>Ranger ユーザーの同期と構成

Ranger には、ユーザーを同期するために 1 時間ごとに実行される組み込みの同期エンジンが用意されています。 ユーザー データベースは Ambari と共有されません。 HDInsight により、管理者ユーザー、ウォッチドッグ ユーザー、およびクラスターの作成中に指定されたグループのメンバーを同期するように検索フィルターが構成されます。 グループ メンバーは、推移的に同期されます。

* 増分同期を無効にします。
* ユーザー グループ同期マップを有効にします。
* 推移的なグループ メンバーを含める検索フィルターを指定します。
* ユーザーの sAMAccountName とグループの名前属性を同期します。

### <a name="group-or-incremental-sync"></a>グループ同期または増分同期

Ranger ではグループ同期オプションがサポートされますが、これはユーザー フィルターとの積集合として動作します。 グループ メンバーシップとユーザー フィルターの和集合ではありません。 Ranger におけるグループ同期フィルターの一般的なユース ケースは、グループ フィルター (dn = clusteradmingroup) とユーザー フィルター (city = seattle) です。

増分同期は、既に (最初の) 同期が行われているユーザーに対してのみ動作します。 増分では、最初の同期後にグループに追加された新しいユーザーは同期されません。

### <a name="update-ranger-sync-filter"></a>Ranger 同期フィルターを更新する

LDAP フィルターは、Ambari UI の Ranger ユーザー同期構成セクションにあります。 既存のフィルターは、`(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` の形式になります。 必ず述語を末尾に追加し、`net ads` 検索コマンドまたは ldp.exe などを使用してフィルターをテストしてください。

## <a name="ranger-user-sync-logs"></a>Ranger のユーザー同期ログ

Ranger のユーザー同期は、ヘッドノードのいずれかで発生します。 ログは、`/var/log/ranger/usersync/usersync.log` にあります。 ログの詳細度を上げるには、次の手順を実行します。

1. Ambari にログインします。
1. Ranger 構成セクションにアクセスします。
1. 詳細 **usersync-log4j** セクションにアクセスします。
1. `log4j.rootLogger` を `DEBUG` レベルに変更します (変更後は `log4j.rootLogger = DEBUG,logFile,FilterLog` のようになります)。
1. 構成を保存し、Ranger を再起動します。

## <a name="next-steps"></a>次のステップ

* [Azure HDInsight での認証の問題](./domain-joined-authentication-issues.md)
* [Azure AD ユーザーを HDInsight クラスターに同期する](../hdinsight-sync-aad-users-to-cluster.md)
