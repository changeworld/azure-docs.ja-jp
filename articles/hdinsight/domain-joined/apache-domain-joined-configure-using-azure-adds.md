---
title: Azure AD-DS を使用して、Enterprise セキュリティ パッケージで HDInsight クラスターを構成する
description: Azure Active Directory Domain Services を使って HDInsight Enterprise セキュリティ パッケージのクラスターをセットアップして構成する方法について説明する。
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: eb24aa0471604696de99f4878baef764cfef0a8b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408356"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services を使用して、Enterprise セキュリティ パッケージで HDInsight クラスターを構成する

Enterprise セキュリティ パッケージ (ESP) のクラスターでは、Azure HDInsight クラスターに対するマルチ ユーザー アクセスが可能です。 ESP の HDInsight クラスターはドメインに接続されるので、ドメイン ユーザーは自分のドメイン資格情報を使用してクラスターの認証を受け、ビッグ データ ジョブを実行することができます。 

この記事では、Azure Active Directory Domain Services (Azure AD-DS) を使って、ESP の HDInsight クラスターを構成する方法について説明します。

>[!NOTE]
>ESP は、Spark、Interactive、Hadoop の HDI 3.6 で一般公開されています。 HBase および Kafka クラスター タイプの ESP はプレビュー段階です。

## <a name="enable-azure-ad-ds"></a>Azure AD-DS を有効にする

Azure AD-DS を有効にすることは、ESP の HDInsight クラスターを作成するための前提条件です。 詳細については、「[Azure Portal を使用して Azure Active Directory Domain Services を有効にする](../../active-directory-domain-services/active-directory-ds-getting-started.md)」を参照してください。 

Azure AD-DS が有効の場合、すべてのユーザーとオブジェクトについて、Azure Active Directory (AAD) から Azure AD-DS への同期が既定で開始されます。 同期操作の長さは、AAD 内のオブジェクトの数によって異なります。 数十万のオブジェクトがある場合、同期には数日かかる場合があります。 

アクセスが必要なグループのみを HDInsight クラスターに同期するように顧客が選択できます。 特定のグループのみを同期するこのオプションは、"*範囲指定された同期*" と呼ばれます。 手順については、「[Configure scoped synchronization from Azure AD to your managed domain (Azure AD からマネージド ドメインまで範囲指定された同期を構成する)](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization)」を参照してください。

> [!NOTE]
> Azure AD-DS インスタンスを作成する特権が与えられているのはテナント管理者だけです。 クラスターにアクセスするユーザーについてのみ、多要素認証を無効にする必要があります。

セキュリティで保護された LDAP を有効にする場合は、証明書のサブジェクト名またはサブジェクトの別名にドメイン名を指定します。 たとえば、お使いのドメイン名が *contoso.com* の場合は、ご自身の証明書サブジェクト名またはサブジェクトの別名にその正確な名前が存在することを確認します。 詳細については、「[Azure AD-DS のマネージド ドメインに対するセキュリティで保護された LDAP の構成](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)」を参照してください。

## <a name="check-aad-ds-health-status"></a>AAD-DS 正常性状態の確認

**[管理]** カテゴリで **[正常性]** を選択して、Azure Active Directory Domain Services の正常性状態を表示します。 AAD-DS の状態が緑 (実行中) で、同期が完了していることを確認します。

![Azure Active Directory Domain Services の正常性](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>マネージド ID の追加

Azure AD-DS を有効にしたら、ユーザー割り当てマネージド ID を作成し、Azure AD-DS アクセス制御の **HDInsight Domain Services 共同作成者**ロールに割り当てます。

![Azure Active Directory Domain Services のアクセス制御](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

マネージド ID を **HDInsight ドメイン サービス共同作成者**ロールに割り当てると、AAD-DS ドメインで特定のドメイン サービス操作を行うための適切なアクセスが ID に付与されます。 詳しくは、[Azure リソースのマネージド ID ](../../active-directory/managed-identities-azure-resources/overview.md)を参照してください。

## <a name="create-a-hdinsight-cluster-with-esp"></a>ESP の HDInsight クラスターの作成

次の手順では、Azure AD-DS を使用して有効になっている ESP の HDInsight クラスターを作成します。

Azure AD-DS インスタンスと HDInsight クラスターの両方を同じ Azure 仮想ネットワークに配置する方が簡単です。 異なる仮想ネットワークに配置する場合は、それらの仮想ネットワークをピアリングして、HDInsight VM が VM を参加させるドメイン コントローラーを認識できるようにする必要があります。 詳細については、「[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)」をご覧ください。 ピアリングが正しく行われているかどうかをテストするには、VM を HDInsight VNet/サブネットに参加させて、ドメイン名に対して ping を実行するか **ldp.exe** を実行して、AAD-DS ドメインにアクセスします。

HDInsight クラスターを作成するとき、カスタム タブで Enterprise セキュリティ パッケージを有効にするオプションを使用できます。 

![Azure HDInsight のセキュリティとネットワーク](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

ESP を有効にすると、Azure AD-DS に関連する一般的な構成ミスが自動的に検出され、検証されます。

![Azure HDInsight Enterprise セキュリティ パッケージのドメインの検証](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

早期検出では、クラスターを作成する前にエラーを修正できるため、時間を節約できます。

![Aazure HDInsight Enterprise セキュリティ パッケージがドメインの検証に失敗しました](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

ESP で HDInsight クラスターを作成するときは、次のパラメーターを指定する必要があります。

- **クラスター管理者ユーザー**: 同期された Azure AD-DS からクラスターの管理者を選択します。 このアカウントは同期済みで、AAD-DS で使用できなければなりません。

- **クラスターのアクセス グループ**: ユーザーをクラスターに同期させるセキュリティ グループは、Azure AD-DS で使用する必要があります。 たとえば、HiveUsers グループです。 詳細については、「[Azure Active Directory でグループを作成し、メンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

- **LDAPS URL**: たとえば、ldaps://contoso.com:636 です。

次のスクリーンショットは、Azure portal における適切な構成を示しています。

![Azure HDInsight ESP Active Directory Domain Services の構成](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

作成したマネージド ID は、新しいクラスターを作成するときに、ユーザー割り当てマネージド ID ドロップダウンから選択できます。

![Azure HDInsight ESP Active Directory Domain Services の構成](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>次の手順
* Hive ポリシーの構成と Hive クエリの実行については、「[ESP の HDInsight クラスターの Hive ポリシーの構成](apache-domain-joined-run-hive.md)」を参照してください。
* SSH を使用して ESP の HDInsight クラスターに接続する方法については、「[Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)」を参照してください。