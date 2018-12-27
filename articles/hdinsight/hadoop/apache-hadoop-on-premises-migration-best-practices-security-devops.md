---
title: オンプレミスの Apache Hadoop クラスターの Azure HDInsight への移行 - セキュリティおよび DevOps のベスト プラクティス
description: オンプレミスの Apache Hadoop クラスターを Azure HDInsight に移行することについてのセキュリティおよび DevOps のベスト プラクティスについて説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 9489d6e8780a30c5c54ee307d6c45c4bc2eb0e5d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419284"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>オンプレミスの Apache Hadoop クラスターの Azure HDInsight への移行 - セキュリティおよび DevOps のベスト プラクティス

この記事では、Azure HDInsight システムのセキュリティと DevOps のための推奨事項について説明します。 これはオンプレミスの Apache Hadoop システムを Azure HDInsight に移行することを支援するためのベスト プラクティスを提供するシリーズの一部です。

## <a name="use-the-enterprise-security-package-to-secure-and-govern-the-cluster"></a>Enterprise セキュリティ パッケージを使用してクラスターをセキュリティ保護して管理する

Enterprise セキュリティ パッケージ (ESP) は、Active Directory ベースの認証、マルチ ユーザー サポート、およびロールベースのアクセス制御をサポートします。 ESP オプションを選択することで、HDInsight クラスターが Active Directory ドメインに参加し、エンタープライズ管理者は Apache Ranger を使用して、Hive のセキュリティのためのロールベースのアクセス制御 (RBAC) を構成できます。 管理者はさらに従業員によるデータ アクセスとアクセス制御ポリシーに加えられた変更を監査できます。

ESP 機能は現在プレビュー段階で、Apache Hadoop、Apache Spark、Apache HBase、Apache Kafka、および Apache Interactive Query のクラスター タイプでのみ使用できます。

ドメイン参加済み HDInsight クラスターをデプロイするには、次の手順を使用します。

- ドメイン名を渡すことによって Azure Active Directory (AAD) をデプロイする
- Azure Active Directory Domain Services (AAD DS) をデプロイする
- 必要な仮想ネットワークとサブネットを作成する
- AAD DS を管理するために仮想ネットワーク内に VM をデプロイする
- VM をドメインに参加させる
- AD および DNS ツールをインストールする
- AAD DS 管理者に組織単位 (OU) を作成させる
- AAD DS のために LDAPS を有効にする
- OU に対して委任された読み取りおよび書き込み管理者アクセス許可を使用して、読み取りおよび書き込みができるように、Azure Active Directory 内にサービス アカウントを作成する。 このサービス アカウントは、コンピューターをドメインに参加させ、OU 内にマシン プリンシパルを配置することができます。 また、クラスターの作成中に指定する OU 内にサービス プリンシパルを作成することもできます。

    > [!Note]
    > サービス アカウントは AD ドメイン管理者アカウントである必要はありません

- 次のパラメーターを設定して、HDInsight ESP クラスターを展開する。
    - **ドメイン名**: Azure AD DS に関連付けるドメイン名。
    - **ドメイン ユーザー名**: 前のセクションで作成した Azure AD DS DC マネージド ドメイン内のサービス アカウント。たとえば `hdiadmin@contoso.onmicrosoft.com` のようになります。 このドメイン ユーザーは、この HDInsight クラスターの管理者になります。
    - **ドメイン パスワード**: サービス アカウントのパスワード。
    - **組織単位**: HDInsight クラスターで使用する OU の識別名。たとえば `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com` のようになります。 この OU が存在しない場合、HDInsight クラスターは、サービス アカウントが持っている特権を使用して OU を作成しようとします。
    - **LDAPS の URL**: たとえば `ldaps://contoso.onmicrosoft.com:636` のようになります。
    - **アクセス ユーザー グループ**: クラスターに同期させるユーザーが属しているセキュリティ グループ。たとえば `HiveUsers` のようになります。 複数のユーザー グループを指定する場合は、セミコロン (;) で区切ります。 グループは、ESP クラスターを作成する前にディレクトリに存在する必要があります。

詳細については、次の記事を参照してください。

- [ドメイン参加済み HDInsight クラスターでの Hadoop セキュリティの概要](../domain-joined/apache-domain-joined-introduction.md)
- [HDInsight で Azure のドメイン参加済み Hadoop クラスターを計画する](../domain-joined/apache-domain-joined-architecture.md)
- [Azure Active Directory Domain Services を使用してドメイン参加済み HDInsight クラスターを構成する](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Azure Active Directory ユーザーを HDInsight クラスターに同期する](../hdinsight-sync-aad-users-to-cluster.md)
- [ドメイン参加済み HDInsight での Hive ポリシーの構成](../domain-joined/apache-domain-joined-run-hive.md)
- [ドメイン参加済み HDInsight Hadoop クラスターで Apache Oozie を実行する](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security-management"></a>エンドツーエンドのエンタープライズ セキュリティ管理を実装する

エンド ツー エンドのエンタープライズ セキュリティは、次のコントロールを使用して実現できます。

- **private および protected のデータ パイプライン (境界レベルのセキュリティ)**
    - 境界レベルのセキュリティは、Azure Virtual Networks、Network Security Groups、および Gateway サービスを通じて実現できます

- **データ アクセスの認証と承認**
    - Azure Active Directory Domain Services を使用してドメイン参加済み HDInsight クラスターを作成します。 (Enterprise セキュリティ パッケージ)
    - Ambari を使用して、AD ユーザー用のクラスター リソースへのロールベースのアクセスを提供します
    - Apache Ranger を構成して、Hive のアクセス制御ポリシーを表/列/行のレベルで設定できます。
    - クラスターへの SSH アクセスも管理者だけに制限されます。

- **監査**
    - HDInsight クラスター リソースとデータへのすべてのアクセスを表示し、レポートを作成できます。
    - アクセス制御ポリシーのすべての変更を表示し、レポートを作成できます

- **暗号化**
    - Microsoft が管理するキーまたは顧客管理キーを使用した、透過的なサーバー側の暗号化。
    - クライアント側の暗号化、https および TLS を使用した、転送中の暗号化

詳細については、次の記事を参照してください。

- [Azure Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)
- [Azure ネットワーク セキュリティ グループの概要](../../virtual-network/security-overview.md)
- [Azure Virtual Network ピアリング](../../virtual-network/virtual-network-peering-overview.md)
- [Azure Storage セキュリティ ガイド](../../storage/common/storage-security-guide.md)
- [保存時の Azure Storage Service Encryption](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>監視とアラートを使用する

詳しくは、次の記事を参照してください。

[Azure Monitor の概要](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>クラスターをアップグレードする

最新の機能を活用するために最新の HDInsight バージョンに定期的にアップグレードします。 クラスターを最新バージョンにアップグレードするには、次の手順を使用できます。

1. 使用可能な最新の HDInsight バージョンを使用して、新しい TEST HDInsight クラスターを作成します。
1. 新しいクラスターでテストし、ジョブとワークロードが期待どおりに動作するかどうかを確認します。
1. 必要に応じて、ジョブ、アプリケーション、またはワークロードを変更します。
1. クラスター ノードでローカルに格納されている一時的なデータをバックアップします。
1. 既存のクラスターを削除します。
1. 同じ既定のデータおよびメタストアを以前のクラスターとして使用して、同じ VNET サブネット内に最新の HDInsight バージョンのクラスターを作成します。
1. バックアップしたすべての一時的なデータをインポートします。
1. 新しいクラスターを使用して、ジョブを開始または処理を続行します。

詳細については、[HDInsight クラスターの新規バージョンへのアップグレード](../hdinsight-upgrade-cluster.md)に関するページを参照してください

## <a name="patch-cluster-operating-systems"></a>クラスターのオペレーティング システムにパッチを適用する

管理された Hadoop サービスとして、HDInsight では、HDInsight クラスターで使用される VM の OS の修正プログラムを適用処理します。

詳細については、「[HDInsight 用の OS の修正プログラム](../hdinsight-os-patching.md)」の記事を参照してください

## <a name="post-migration"></a>移行後

1. **アプリケーションを修復**: ジョブ、プロセス、およびスクリプトに対して必要な変更を繰り返し加えます。
2. **テストの実行**: 機能とパフォーマンスのテストを繰り返し実行します。
3. **最適化**: 上記のテスト結果に基づいてパフォーマンスの問題に対処し、再テストを行ってパフォーマンスが改善されたかどうかを確認します。

## <a name="next-steps"></a>次の手順

- [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction) についての詳細情報