---
title: Azure AD-DS を使用して、Enterprise セキュリティ パッケージで HDInsight クラスターを構成する
description: Azure Active Directory Domain Services を使って HDInsight Enterprise セキュリティ パッケージのクラスターをセットアップして構成する方法について説明する
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a5b377381fd540c2a9f1d85e0cb7edce32c2dae8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968375"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services を使用して、Enterprise セキュリティ パッケージで HDInsight クラスターを構成する

Enterprise セキュリティ パッケージ (ESP) のクラスターでは、Azure HDInsight クラスターに対するマルチ ユーザー アクセスが可能です。 ESP の HDInsight クラスターはドメインに接続されるので、ドメイン ユーザーは自分のドメイン資格情報を使用してクラスターの認証を受け、ビッグ データ ジョブを実行することができます。 

この記事では、Azure Active Directory Domain Services (Azure AD-DS) を使って、ESP の HDInsight クラスターを構成する方法について説明します。

>[!NOTE]
>ESP は、Spark、Interactive、Hadoop の HDI 3.6 以降で使用できます。 HBase クラスター タイプの ESP はプレビュー段階です。


## <a name="enable-azure-ad-ds"></a>Azure AD-DS を有効にする

Azure AD-DS を有効にすることは、ESP の HDInsight クラスターを作成するための前提条件です。 詳細については、「[Azure Portal を使用して Azure Active Directory Domain Services を有効にする](../../active-directory-domain-services/active-directory-ds-getting-started.md)」を参照してください。 

> [!NOTE]
> Azure AD-DS インスタンスを作成する特権が与えられているのはテナント管理者だけです。 HDInsight の既定のストレージとして Azure Data Lake Storage Gen1 を使用する場合は、Data Lake Storage Gen1 の既定の Azure AD テナントが HDInsight クラスターのドメインと同じであることを確認します。 Hadoop は Kerberos と基本認証に依存しているため、クラスターへのアクセスを許可するユーザーに対して多要素認証を無効にする必要があります。

Secure LDAP は、Azure AD-DS マネージド ドメインのためのものです。 LDAPS を有効にする場合は、証明書のサブジェクト名または代替サブジェクト名にドメイン名を指定します。 詳細については、「[Azure AD-DS のマネージド ドメインに対するセキュリティで保護された LDAP の構成](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)」を参照してください。

## <a name="add-managed-identity"></a>マネージド ID の追加

Azure AD-DS を有効にしたら、マネージド ID を作成し、Azure AD-DS アクセス制御の **HDInsight Domain Services 投稿者**ロールに割り当てます。

![Azure Active Directory Domain Services のアクセス制御](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

詳しくは、[Azure リソースのマネージド ID ](../../active-directory/managed-identities-azure-resources/overview.md)を参照してください。

## <a name="create-a-hdinsight-cluster-with-esp"></a>ESP の HDInsight クラスターの作成

次の手順では、Azure AD-DS を使用して有効になっている ESP の HDInsight クラスターを作成します。

Azure AD-DS インスタンスと HDInsight クラスターの両方を同じ Azure 仮想ネットワークに配置する方が簡単です。 異なる仮想ネットワークに配置する場合は、それらの仮想ネットワークをピアリングして、HDInsight VM が VM を参加させるドメイン コントローラーを認識できるようにする必要があります。 詳細については、「[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)」をご覧ください。

HDInsight クラスターを作成するときに、Enterprise セキュリティ パッケージがクラスターを Azure AD-DS に接続できるようにするオプションがあります。 

![Azure HDInsight のセキュリティとネットワーク](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

ESP を有効にすると、Azure AD-DS に関連する一般的な構成ミスが自動的に検出され、検証されます。

![Azure HDInsight Enterprise セキュリティ パッケージのドメインの検証](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

早期検出では、クラスターを作成する前にエラーを修正できるため、時間を節約できます。

![Aazure HDInsight Enterprise セキュリティ パッケージがドメインの検証に失敗しました](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

ESP で HDInsight クラスターを作成するときは、次のパラメーターを指定する必要があります。

- **クラスター管理者ユーザー**: 同期された Azure AD-DS からクラスターの管理者を選択します。

- **クラスターのアクセス グループ**: ユーザーをクラスターに同期させるセキュリティ グループは、Azure AD-DS で同期して使用する必要があります。 たとえば、HiveUsers です。 複数のユーザー グループを指定する場合は、セミコロン (;) で区切ります。 グループは、プロビジョニングする前にディレクトリに存在する必要があります。 詳細については、「[Azure Active Directory でグループを作成し、メンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。 グループが存在しない場合は、"Active Directory 内でグループ HiveUsers を見つけることができませんでした" のエラーが発生します。

- **LDAPS URL**: たとえば、ldaps://contoso.onmicrosoft.com:636 です。

    > [!IMPORTANT]
    > "ldaps://" とポート番号 (:636) を含む完全な URL を入力します。

次のスクリーンショットは、Azure Portal における構成を示しています。

   ![Azure HDInsight ESP Active Directory Domain Services の構成](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>次の手順
* Hive ポリシーの構成と Hive クエリの実行については、「[ESP の HDInsight クラスターの Hive ポリシーの構成](apache-domain-joined-run-hive.md)」を参照してください。
* SSH を使用して ESP の HDInsight クラスターに接続する方法については、「[Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)」を参照してください。

