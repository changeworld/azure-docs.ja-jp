---
title: ドメイン参加済み Azure HDInsight のアーキテクチャ | Microsoft Docs
description: ドメイン参加済み HDInsight を計画する方法について説明します。
services: hdinsight
documentationcenter: ''
author: bhanupr
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/20/2018
ms.author: bprakash
ms.openlocfilehash: b4f79388e45e24dc906a3a03dc0c0e51df52160d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>HDInsight で Azure のドメイン参加済み Hadoop クラスターを計画する

標準的な HDInsight クラスターはシングル ユーザー クラスターです。 これは、大規模なデータ ワークロードを構築する小規模なアプリケーション チームを抱えているほとんどの企業に適しています。 Hadoop の普及に伴い、多くの企業では、IT チームが管理するクラスターを複数のアプリケーション チームが共有するモデルに移行しつつあります。 このため、マルチユーザーのクラスターに関わる機能は、Azure HDInsight で最も需要が高い機能となっています。

HDInsight は、独自のマルチユーザー認証と承認を構築する代わりに、最も人気のある ID プロバイダー、Active Directory (AD) を利用します。 AD の強力なセキュリティ機能を使用して、HDInsight でマルチユーザー認証を管理できます。 HDInsight と AD を統合することで、AD の資格情報を使ってクラスターと通信できます。 HDInsight 内の VM は、AD ドメインに参加します。HDInsight が AD ユーザーをローカルの Hadoop ユーザーにマップするため、HDInsight で実行されているすべてのサービス (Ambari、Hive サーバー、Ranger、Spark Thrift サーバーなど) が、認証済みユーザーに対してシームレスに動作します。 さらに、管理者は、Apache Ranger を使用して強力な承認ポリシーを作成し、HDInsight 内のリソースに対するロールベースのアクセス制御を行うことができます。


## <a name="integrate-hdinsight-with-active-directory"></a>HDInsight を Active Directory と統合する

HDInsight を Active Directory と統合すると、HDInsight クラスター ノードは、AD ドメインに参加することになります。 クラスター上の Hadoop コンポーネントには Kerberos セキュリティが構成されます。 Active Directory には、各 Hadoop コンポーネントに対応するサービス プリンシパルが作成されます。 また、ドメインに参加する各マシンには、対応するマシン プリンシパルが作成されます。 こうしたサービス プリンシパルとマシン プリンシパルが Active Directory で無秩序に混じり合ってしまうことがあります。 そのため Active Directory には、それらのプリンシパルの配置先となる組織単位 (OU) を設けることが必要となります。 

これらの点を総合すると、次の要素から成る環境をセットアップする必要があります。

- Active Directory ドメイン コントローラー (要 LDAPS 構成)。
- HDInsight の仮想ネットワークから Active Directory ドメイン コントローラーへの接続。
- Active Directory 上に作成された組織単位。
- 次の操作に必要なアクセス許可を持つサービス アカウント:

    - OU にサービス プリンシパルを作成する。
    - ドメインにマシンを参加させて OU にマシン プリンシパルを作成する。

次のスクリーンショットは、contoso.com に作成された OU を示したものです。スクリーンショットには、サービス プリンシパルとマシン プリンシパルもいくつか表示されています。

![ドメイン参加済み HDInsight クラスターの OU](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png)が必要です。

### <a name="the-way-of-bringing-your-own-active-directory-domain-controllers"></a>独自の Active Directory ドメイン コントローラーを導入する方法

- **Azure Active Directory Domain Services**: このサービスでは、Windows Server Active Directory と完全に互換性のある管理対象の Active Directory ドメインが提供されます。 Microsoft は、AD ドメインの管理、修正プログラムの適用、および監視を行います。 ドメイン コントローラーの管理について心配することなく、クラスターをデプロイすることができます。 ユーザー、グループおよびパスワードは、ユーザーが会社の資格情報を使ってクラスターにサインインできるように、Azure Active Directory から同期されます。 詳細については、「[Azure Active Directory Domain Services を使用してドメイン参加済み HDInsight クラスターを構成する](./apache-domain-joined-configure-using-azure-adds.md)」を参照してください。

> [!NOTE]
> Azure IaaS VM の Active Directory はサポートされなくなりました。

## <a name="next-steps"></a>次の手順
* ドメイン参加済み HDInsight クラスターを管理するには、[ドメイン参加済み HDInsight クラスターの管理](apache-domain-joined-manage.md)に関する記事をご覧ください。
* Hive ポリシーを構成して Hive クエリを実行するには、[ドメイン参加済み HDInsight クラスターでの Hive ポリシーの構成](apache-domain-joined-run-hive.md)に関する記事をご覧ください。
* SSH を使用してドメイン参加済み HDInsight クラスターで Hive クエリを実行する方法については、[HDInsight で SSH キーを使用する](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。
