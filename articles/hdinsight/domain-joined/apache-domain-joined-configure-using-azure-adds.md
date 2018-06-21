---
title: AAD-DS を使用した、ドメイン参加済み HDInsight クラスターの構成
description: Azure Active Directory Domain Services を使ったドメイン参加済み HDInsight クラスターの設定と構成の方法について説明します。
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8064940e0d0f035010a2521752d6f32f3f9ccd9f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849833"
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services を使用してドメイン参加済み HDInsight クラスターを構成する

ドメイン参加済みクラスターは、HDInsight クラスターでのマルチユーザー アクセスを提供します。 ドメイン参加済み HDInsight クラスターは、ドメイン ユーザーが各自のドメイン資格情報を使用してクラスターで認証され、ビッグ データ ジョブを実行できるように、ドメインに接続されます。 

この記事では、Azure Active Directory Domain Services を使用して、ドメイン参加済み HDInsight クラスターを構成する方法について説明します。

## <a name="create-azure-adds"></a>Azure ADDS の作成

Azure AD Domain Services (AAD-DS) を有効にすることは、ドメイン参加済み HDInsight クラスターを作成するための前提条件です。 AAD-DS インスタンスを有効にするには、「[Azure Portal を使用して Azure Active Directory Domain Services を有効にする](../../active-directory-domain-services/active-directory-ds-getting-started.md)」を参照してください。 

> [!NOTE]
> AAD-DS インスタンスを作成する権限が与えられているのはテナント管理者だけです。 HDInsight の既定のストレージとして Azure Data Lake Storage (ADLS) を使用する場合は、ADLS の既定の Azure AD テナントが HDInsight クラスターのドメインと同じであることを確認します。 Hadoop は Kerberos と基本認証に依存しているため、クラスターへのアクセスを許可するユーザーに対して多要素認証を無効にする必要があります。

AAD-DS インスタンスをプロビジョニングしたら、適切な権限が付与されたサービス アカウントを (AAD-DS と同期される) AAD 内に作成する必要があります。 このサービス アカウントが既に存在する場合は、パスワードをリセットし、AAD-DS と同期されるまで待つ必要があります (このリセットによって Kerberos パスワード ハッシュの作成が起こり、AAD-DS と同期するまで最大 30 分かかることがあります)。 このサービス アカウントには、次の特権を付与します。

- コンピューターをドメインに参加させ、クラスターの作成時に指定する OU 内にマシン プリンシパルを配置します。
- クラスターの作成中に指定する OU 内にサービス プリンシパルを作成します。

> [!NOTE]
> Apache Zeppelin ではドメイン名を使用して管理サービス アカウントを認証するため、サービス アカウントが正常に機能するには、Apache Zeppelin の UPN サフィックスと同じドメイン名が必要です。

OU の詳細とその管理方法については、[AAD-DS での OU の作成](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)に関する記事を参照してください。 

AAD-DS マネージド ドメイン用の Secure LDAP が必要です。 Secure LDAP を有効にするには、[AAD-DS マネージド ドメインに対してセキュリティで保護された LDAP (LDAPS) を構成する方法](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)に関する記事をご覧ください。

## <a name="create-a-domain-joined-hdinsight-cluster"></a>ドメイン参加済み HDInsight クラスターの作成

次の手順は、前のセクションで作成したサービス アカウントと AAD DS を使用して HDInsight クラスターを作成することです。

AAD-DS と HDInsight クラスターは、どちらも同じ Azure 仮想ネットワーク (VNet) に配置した方が簡単です。 異なる VNet に配置することを選択した場合は、それらの VNet をピアリングして、HDI VM が VM を参加させるドメイン コントローラーを認識できるようにする必要があります。 詳細については、「[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)」をご覧ください。

ドメイン参加済み HDInsight クラスターを作成するときは、次のパラメーターを指定する必要があります。

- **ドメイン名**: AAD-DS に関連付けるドメイン名  (例: contoso.onmicrosoft.com)。
- **ドメイン ユーザー名**: 前のセクションで作成したマネージド ドメイン内のサービス アカウント。 たとえば、「hdiadmin@contoso.onmicrosoft.com」のように入力します。 このドメイン ユーザーは、この HDInsight クラスターの管理者になります。
- **ドメイン パスワード**: サービス アカウントのパスワード。
- **組織単位**: HDInsight クラスターで使用する OU の識別名。 たとえば「OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com」と入力します。 この OU が存在しない場合、HDInsight クラスターは、サービス アカウントが持っている特権を使用して OU を作成しようとします  (たとえば、サービス アカウントが AAD-DS 管理者グループに属している場合は、OU を作成する適切な権限がありますが、それ以外の場合は、先に OU を作成し、その OU を完全に管理する権限をサービス アカウントに付与する必要があります。[AAD-DS での OU の作成](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)に関する記事を参照してください)。

    > [!IMPORTANT]
    > OU の後ろにすべての DC をコンマで区切って指定することが重要です (例: OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com)。

- **LDAPS URL**: たとえば、ldaps://contoso.onmicrosoft.com:636 です。

    > [!IMPORTANT]
    > ldaps:// とポート番号 (: 636) を含む完全な URL を入力してください。

- **アクセス ユーザー グループ**: クラスターに同期させるユーザーが属しているセキュリティ グループ。 たとえば、HiveUsers です。 複数のユーザー グループを指定する場合は、コンマ (,) で区切ってください。
 
次のスクリーンショットは、Azure Portal における構成を示しています。

![Azure HDInsight ドメイン参加済み Active Directory Domain Services の構成](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png)が必要です。


## <a name="next-steps"></a>次の手順
* Hive ポリシーの構成と Hive クエリの実行については、[ドメイン参加済み HDInsight クラスターの Hive ポリシーの構成](apache-domain-joined-run-hive.md)に関する記事をご覧ください。
* SSH を使用してドメイン参加済み HDInsight クラスターに接続する方法については、「[Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)」を参照してください。

