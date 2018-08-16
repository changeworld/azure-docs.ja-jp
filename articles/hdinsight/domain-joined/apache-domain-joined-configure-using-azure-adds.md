---
title: Azure AD DS を使用してドメイン参加済み HDInsight クラスターを構成する
description: Azure Active Directory Domain Services を使ってドメイン参加済み HDInsight クラスターを設定および構成する方法について説明します
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.topic: conceptual
ms.date: 07/17/2018
ms.openlocfilehash: 0d44812c92fd14bf87aac9a942241f8de55f2eec
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590587"
---
# <a name="configure-a-domain-joined-hdinsight-cluster-by-using-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services を使用してドメイン参加済み HDInsight クラスターを構成する

ドメイン参加済みクラスターは、Azure HDInsight クラスターでのマルチユーザー アクセスを提供します。 ドメイン参加済み HDInsight クラスターは、ドメイン ユーザーが各自のドメイン資格情報を使用してクラスターで認証され、ビッグ データ ジョブを実行できるように、ドメインに接続されます。 

この記事では、Azure Active Directory Domain Services (Azure AD DS) を使って、ドメイン参加済み HDInsight クラスターを構成する方法について説明します。

## <a name="enable-azure-ad-ds"></a>Azure AD DS を有効にする

Azure AD DS を有効にすることは、ドメイン参加済み HDInsight クラスターを作成するための前提条件です。 詳細については、「[Azure Portal を使用して Azure Active Directory Domain Services を有効にする](../../active-directory-domain-services/active-directory-ds-getting-started.md)」を参照してください。 

> [!NOTE]
> Azure AD DS インスタンスを作成する特権が与えられているのはテナント管理者だけです。 HDInsight の既定のストレージとして Azure Data Lake Storage Gen1 を使用する場合は、Data Lake Storage Gen1 の既定の Azure AD テナントが HDInsight クラスターのドメインと同じであることを確認します。 Hadoop は Kerberos と基本認証に依存しているため、クラスターへのアクセスを許可するユーザーに対して多要素認証を無効にする必要があります。

Azure AD DS インスタンスをプロビジョニングした後、Azure Active Directory (Azure AD) で適切なアクセス許可を持つサービス アカウントを作成します。 このサービス アカウントが既に存在する場合は、そのパスワードをリセットし、Azure AD DS に同期されるまで待ちます。 このリセットにより Kerberos パスワード ハッシュが作成され、Azure AD DS に同期するまで最大で 30 分かかる場合があります。 

サービス アカウントは次の特権が必要です。

- コンピューターをドメインに参加させ、クラスターの作成時に指定する OU 内にマシン プリンシパルを配置します。
- クラスターの作成中に指定する OU 内にサービス プリンシパルを作成します。

> [!NOTE]
> Apache Zeppelin ではドメイン名を使用して管理サービス アカウントを認証するため、サービス アカウントが正常に機能するには、Apache Zeppelin の UPN サフィックスと同じドメイン名が "*必要*" です。

OU の詳細とその管理方法については、「[Azure AD ドメイン サービスの管理対象ドメインに組織単位 (OU) を作成する](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)」をご覧ください。 

Secure LDAP は、Azure AD DS マネージド ドメインのためのものです。 詳細については、「[Azure AD ドメイン サービスの管理対象ドメインに対するセキュリティで保護された LDAP (LDAPS) の構成](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)」を参照してください。

## <a name="create-a-domain-joined-hdinsight-cluster"></a>ドメイン参加済み HDInsight クラスターを作成する

次の手順は、前のセクションで作成した Azure AD DS とサービス アカウントを使用して、HDInsight クラスターを作成することです。

Azure AD DS インスタンスと HDInsight クラスターは、どちらも同じ Azure 仮想ネットワークに配置した方が簡単です。 異なる仮想ネットワークに配置する場合は、それらの仮想ネットワークをピアリングして、HDInsight VM が VM を参加させるドメイン コントローラーを認識できるようにする必要があります。 詳細については、「[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)」をご覧ください。

ドメイン参加済み HDInsight クラスターを作成するときは、次のパラメーターを指定する必要があります。

- **ドメイン名**: Azure AD DS に関連付けるドメイン名。 たとえば、contoso.onmicrosoft.com などです。

- **ドメイン ユーザー名**: 前のセクションで作成した Azure ADDS DC マネージド ドメイン内のサービス アカウント。 例: hdiadmin@contoso.onmicrosoft.com。 このドメイン ユーザーは、この HDInsight クラスターの管理者になります。

- **ドメイン パスワード**: サービス アカウントのパスワード。

- **組織単位**: HDInsight クラスターで使用する OU の識別名。 たとえば、OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com などです。 この OU が存在しない場合、HDInsight クラスターは、サービス アカウントが持っている特権を使用して OU を作成しようとします。 たとえば、サービス アカウントが Azure AD DS Administrators グループに属する場合、OU を作成する適切なアクセス許可があります。 それ以外の場合は、最初に OU を作成し、その OU に対するフル コントロールをサービス アカウントに付与する必要があります。 詳細については、「[Azure AD ドメイン サービスの管理対象ドメインに組織単位 (OU) を作成する](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)」を参照してください。

    > [!IMPORTANT]
    > OU の後ろにコンマで区切ってすべての DC を指定します (例: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com)。

- **LDAPS URL**: たとえば、ldaps://contoso.onmicrosoft.com:636 です。

    > [!IMPORTANT]
    > "ldaps://" とポート番号 (:636) を含む完全な URL を入力します。

- **アクセス ユーザー グループ**: クラスターに同期させるユーザーが属しているセキュリティ グループ。 たとえば、HiveUsers です。 複数のユーザー グループを指定する場合は、セミコロン (;) で区切ります。 グループは、プロビジョニングする前にディレクトリに存在する必要があります。 詳細については、「[Azure Active Directory でグループを作成し、メンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。 グループが存在しない場合は、"Active Directory 内でグループ HiveUsers を見つけることができませんでした" のエラーが発生します。

次のスクリーンショットは、Azure Portal における構成を示しています。

   ![Azure HDInsight ドメイン参加済み Active Directory Domain Services の構成](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>次の手順
* Hive ポリシーの構成と Hive クエリの実行については、「[ドメイン参加済み HDInsight での Hive ポリシーの構成](apache-domain-joined-run-hive.md)」を参照してください。
* SSH を使用してドメイン参加済み HDInsight クラスターに接続する方法については、「[Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)」を参照してください。

