---
title: 資格情報の管理に ID ブローカー (プレビュー) を使用する - Azure HDInsight
description: ドメインに参加している Apache Hadoop クラスターの認証を簡略化するための HDInsight ID ブローカーについて説明します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75475917"
---
# <a name="use-id-broker-preview-for-credential-management"></a>資格情報の管理に ID ブローカー (プレビュー) を使用する

この記事では、Azure HDInsight で ID ブローカー機能を設定して使用する方法について説明します。 この機能を使用すると、多要素認証を使用して Apache Ambari にサインインし、Azure Active Directory Domain Services (Azure AD DS) でパスワード ハッシュを必要とせずに必要な Kerberos チケットを取得できます。

## <a name="overview"></a>概要

ID ブローカーにより、次のシナリオで複雑な認証設定が簡略化されます。

* 組織では、クラウド リソースにアクセスするユーザーを認証するためにフェデレーションに依存しています。 以前は、HDInsight Enterprise セキュリティ パッケージ (ESP) クラスターを使用するには、オンプレミス環境から Azure Active Directory へのパスワード ハッシュ同期を有効にする必要がありました。 この要件は、組織によっては難しい場合や望ましくない場合があります。

* さまざまな認証メカニズムに依存するテクノロジに依存したソリューションを構築します。 たとえば、Apache Hadoop と Apache レンジャーは Kerberos に依存しているのに対し、Azure Data Lake Storage は OAuth に依存している、などです。

ID ブローカーにより、統合された認証インフラストラクチャが提供され、Azure AD DS にパスワード ハッシュを同期する必要がなくなります。 ID ブローカーは、クラスター ゲートウェイ ノードと共に、Windows Server VM (ID ブローカー ノード) で実行されるコンポーネントで構成されます。 

次の図は、ID ブローカーが有効になった後の、フェデレーション ユーザーを含むすべてのユーザーの認証フローを示しています。

![ID ブローカーでの認証フロー](./media/identity-broker/identity-broker-architecture.png)

ID ブローカーでは、パスワードを指定せずに Multi-Factor Authentication を使用して ESP クラスターにサインインできます。 Azure portal などの他の Azure サービスに既にサインインしている場合は、シングル サインオン (SSO) エクスペリエンスによって HDInsight クラスターにサインインできます。

## <a name="enable-hdinsight-id-broker"></a>HDInsight ID ブローカーを有効にする

ID ブローカーが有効になっている ESP クラスターを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインする
1. ESP クラスターの基本的な作成手順に従います。 詳細については、「[ESP の HDInsight クラスターの作成](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)」を参照してください。
1. **[HDInsight ID ブローカーを有効にする]** を選択します。

ID ブローカー機能によって、クラスターに追加の VM が 1 つ追加されます。 この VM は ID ブローカー ノードであり、認証をサポートするサーバー コンポーネントが含まれています。 ID ブローカー ノードは、Azure AD DS ドメインに参加しているドメインです。

![ID ブローカーを有効にするオプション](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>ツールの統合

HDInsight [IntelliJ プラグイン](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib)は、OAuth をサポートするように更新されています。 このプラグインを使用して、クラスターに接続し、ジョブを送信できます。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS でのパスワード ハッシュを使用しない SSH アクセス

ID ブローカーを有効にした後も、ドメイン アカウントを使用する SSH シナリオ用に、Azure AD DS に格納されているパスワード ハッシュが必要になります。 ドメインに参加している VM に SSH 接続する場合、または `kinit` コマンドを実行する場合は、パスワードを入力する必要があります。 

SSH 認証を使用するには、Azure AD DS でハッシュを使用できるようにする必要があります。 管理シナリオ用にのみ SSH を使用する場合は、クラウド専用アカウントを 1 つ作成し、それを使用してクラスターに SSH 接続することができます。 その他のユーザーは、Azure AD DS でパスワード ハッシュを使用できるようにしなくても、Ambari または HDInsight ツール (IntelliJ プラグインなど) を使用できます。

## <a name="next-steps"></a>次のステップ

* [Azure Active Directory Domain Services を使用して、Enterprise セキュリティ パッケージで HDInsight クラスターを構成する](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory ユーザーを HDInsight クラスターに同期する](../hdinsight-sync-aad-users-to-cluster.md)
* [クラスター パフォーマンスの監視](../hdinsight-key-scenarios-to-monitor.md)
