---
title: ドメイン参加済み Azure HDInsight のアーキテクチャ | Microsoft Docs
description: ドメイン参加済み HDInsight を計画する方法について説明します。
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8503534031dc5774e64c58edd3e158162a5a6aee
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110456"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>HDInsight で Azure のドメイン参加済み Hadoop クラスターを計画する

標準的な HDInsight クラスターはシングル ユーザー クラスターです。 これは、大規模なデータ ワークロードを構築する小規模なアプリケーション チームを抱えているほとんどの企業に適しています。 ユーザーごとに異なるオンデマンドの専用クラスターを使用し、不要になったときに破棄することができます。 ただし、多くの企業では、IT チームが管理するクラスターを複数のアプリケーション チームが共有するモデルに移行しつつあります。 したがって、これらの大規模企業向けの Azure HDInsight にはクラスターへのマルチユーザー アクセスが必要です。

HDInsight は、管理されている方法で、最も一般的な ID プロバイダーである Active Directory (AD) に依存します。 HDInsight を [Azure Active Directory Domain Services (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md) と統合することによって、ドメイン資格情報を使用してクラスターにアクセスすることができます。 HDInsight 内の VM は、指定されたドメインにドメイン参加済みであるため、HDInsight で実行されているすべてのサービス (Ambari、Hive サーバー、Ranger、Spark Thrift サーバーなど) が、認証済みユーザーに対してシームレスに動作します。 さらに、管理者は、Apache Ranger を使用して強力な承認ポリシーを作成し、クラスター内のリソースに対するロールベースのアクセス制御を行うことができます。


## <a name="integrate-hdinsight-with-active-directory"></a>HDInsight を Active Directory と統合する

オープン ソースの Hadoop は、Kerberos に依存して認証とセキュリティを提供します。 したがって、HDInsight クラスター ノードは AAD-DS で管理されるドメインにドメイン参加済みです。 クラスター上の Hadoop コンポーネントには Kerberos セキュリティが構成されます。 Hadoop コンポーネントごとに、サービス プリンシパルが自動的に作成されます。 また、ドメインに参加する各マシンには、対応するマシン プリンシパルが作成されます。 これらのサービスやマシン プリンシパルを格納するために、これらのプリンシパルが配置されているドメイン コントローラー (AAD-DS) 内の組織単位 (OU) を用意する必要があります。 

これらの点を総合すると、次の要素から成る環境をセットアップする必要があります。

- Active Directory ドメイン (AAD-DS で管理)
- AAD-DS で有効になっている Secure LDAP (LDAPS)。
- HDInsight の VNET から AAD-DS VNET への適切なネットワーク接続 (これらに別々の VNET を選択する場合)。 HDI VNET 内の VM には、VNET ピアリングを使用した AAD-DS への通信経路が必要です。 HDI と AAD-DS の両方が同じ VNET 内にデプロイされている場合、接続は自動的に提供されるので、これ以上アクションは必要ありません。
- AAD-DS 上に作成された[組織単位 (OU)](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)
- 次の操作に必要なアクセス許可を持つサービス アカウント:
    - OU にサービス プリンシパルを作成する。
    - ドメインにマシンを参加させて OU にマシン プリンシパルを作成する。

次のスクリーンショットは、contoso.com に作成された OU を示したものです。 スクリーンショットには、サービス プリンシパルとマシン プリンシパルもいくつか表示されています。

![ドメイン参加済み HDInsight クラスターの OU](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png)が必要です。

### <a name="different-domain-controllers-setup"></a>異なるドメイン コントローラーのセットアップ:
現在 HDInsight では、クラスターの Kerberos 認証を行うためにクラスターが通信するメイン ドメイン コントローラーとして AAD-DS だけがサポートされています。 ただし、HDI アクセスに対して AAD-DS が有効になる限り、他の複雑な AD セットアップも可能です。

- **[Azure Active Directory Domain Services (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md)**: このサービスでは、Windows Server Active Directory と完全に互換性のあるマネージド ドメインが提供されます。 Microsoft は、高可用性 (HA) セットアップのドメインの管理、修正プログラムの適用、および監視を行います。 ドメイン コントローラーの管理について心配することなく、クラスターをデプロイすることができます。 ユーザー、グループおよびパスワードは、ユーザーが会社の同じ資格情報を使ってクラスターにサインインできるように、Azure Active Directory (AAD) から同期されます [AAD から AAD-DS への一方向同期]。 詳しくは、[AAD-DS を使ってドメイン参加済み HDInsight クラスターを構成する方法](./apache-domain-joined-configure-using-azure-adds.md)に関する記事をご覧ください。
- **オンプレミスの AD または IaaS VM 上の AD**: ドメインに対してオンプレミスの AD またはその他のより複雑な AD セットアップがある場合は、AD Connect を使用してこれらの ID を AAD と同期してから、その AD テナント上で AAD-DS を有効にできます。 Kerberos はパスワード ハッシュに依存するため、[AAD-DS でパスワード ハッシュ同期を有効にする](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)必要があります。 AD フェデレーション サービス (ADFS) によるフェデレーションを使用している場合は、ADFS インフラストラクチャが失敗した場合のバックアップとしてパスワード ハッシュ同期をセットアップすることもできます。 詳しくは、[AAD Connect 同期によるパスワード ハッシュの同期の有効化](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)に関する記事をご覧ください。AAD と AAD-DS なしでオンプレミスの AD または IaaS VM 上の AD のみ使用することは、HDI クラスター ドメイン参加でサポートされる構成ではありません。

## <a name="next-steps"></a>次の手順
* [ドメイン参加済み HDInsight クラスターの構成](apache-domain-joined-configure-using-azure-adds.md)。
* [ドメイン参加済み HDInsight クラスターに対する Hive ポリシーの構成](apache-domain-joined-run-hive.md)。
* [ドメイン参加済み HDInsight クラスターの管理](apache-domain-joined-manage.md)。 
