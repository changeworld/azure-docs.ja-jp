---
title: Data Lake Store のセキュリティの概要 | Microsoft Docs
description: Azure Data Lake Store がセキュリティが強化されたビッグ データ ストアであることを学習します
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 4dc1b070f7933f23ee615501dfbc68551068210c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="security-in-azure-data-lake-store"></a>Azure Data Lake Store のセキュリティ
多くの企業が、ビッグ データの分析によってビジネスに関する洞察を獲得し、それを意思決定に活かしています。 ただ、一部の組織はユーザーが多様化し、その数も増加の一途を辿っているばかりか、各種の規制が存在する複雑な環境に直面しています。 このため、重要なビジネス データはこれまで以上に強固なセキュリティをもって保管すると同時に、個々のユーザーには適切な水準のアクセス権を付与することがきわめて重要になっています。 Azure Data Lake Store は、このようなセキュリティ要件を満たすことを目指して設計されています。 この記事では、以下に示す Data Lake Store のセキュリティ機能について説明します。

* 認証
* 承認
* ネットワークの分離
* データ保護
* 監査

## <a name="authentication-and-identity-management"></a>認証と ID 管理
認証とは、ユーザーが Data Lake Store または Data Lake Store に接続しているサービスとやり取りするにあたり、ユーザーの ID を確認するプロセスを指します。 Data Lake Store では、ID の管理と認証に [Azure Active Directory](../active-directory/active-directory-whatis.md) を使用します。Azure Active Directory は、ユーザーとグループの管理を簡素化する包括的な ID およびアクセス管理クラウド ソリューションです。

Azure サブスクリプションはそれぞれ、Azure Active Directory のインスタンスに関連付けることができます。 ユーザーとサービスの ID のうち、Azure Active Directory サービスで定義されているものに限り、Data Lake Store アカウントにアクセスできます。アカウントへのアクセスには、Azure ポータル、コマンドライン ツールのほか、組織が Azure Data Lake Store SDK を使って作成したクライアント アプリケーションを使用できます。 一元化されたアクセス制御メカニズムとして Azure Active Directory を使用する主な利点は次のとおりです。

* ID ライフサイクル管理の簡素化。 ユーザーまたはサービスの ID (サービス プリンシパル ID) を迅速に作成することができ、ディレクトリでアカウントを削除または無効にするだけで ID を取り消すことができます。
* 多要素認証。 [多要素認証](../active-directory/authentication/multi-factor-authentication.md) により、ユーザーのサインインとトランザクションにセキュリティ レイヤーを追加できます。
* OAuth、OpenID などの標準的なオープン プロトコルを使ったクライアント認証。
* エンタープライズ ディレクトリ サービスおよびクラウド ID プロバイダーとのフェデレーション。

## <a name="authorization-and-access-control"></a>承認とアクセス制御
Azure Active Directory の認証によってユーザーが Azure Data Lake Store にアクセスできるようになった後は、承認によって Data Lake Store のアクセス許可を制御します。 Data Lake Store では、アカウント関連アクティビティとデータ関連アクティビティの承認が次のように区別されます。

* [ロールベースのアクセス制御](../role-based-access-control/overview.md) (RBAC)
* ストア内のデータにアクセスするための POSIX ACL

### <a name="rbac-for-account-management"></a>RBAC を使用したアカウント管理
Data Lake Store には既定で基本のロールが 4 つ定義されています。 これらのロールでは、Azure ポータル、PowerShell コマンドレット、REST API を使用して Data Lake Store アカウントに実行できる操作が異なります。 所有者ロールと共同作成者ロールは、アカウントに対してさまざまな管理機能を実行できます。 アカウント管理データを表示するだけのユーザーには閲覧者ロールを割り当てることができます。

![RBAC のロール](./media/data-lake-store-security-overview/rbac-roles.png "RBAC のロール")

ロールはアカウント管理のために割り当てられるものですが、一部のロールはデータへのアクセスにも影響しますので注意してください。 ユーザーがファイル システムに対して実行できる操作へのアクセス制御には、ACL を使用する必要があります。 以下の表は、既定のロールの管理権限とデータ アクセス権限の概要を示したものです。

| ロール | 管理権限 | データ アクセス権限 | 説明 |
| --- | --- | --- | --- |
| ロールの割り当てなし |なし |ACL によって管理 |Azure ポータルまたは Azure PowerShell コマンドレットを使って Data Lake Store を参照することはできません。 ユーザーは、コマンドライン ツールのみ使用できます。 |
| Owner |すべて |すべて |所有者ロールは、スーパーユーザーです。 このロールではあらゆるものを管理できるほか、データに対するフル アクセスが認められます。 |
| Reader |読み取り専用 |ACL によって管理 |閲覧者ロールでは、どのユーザーがどのロールに割り当てられているかなど、アカウント管理に関するあらゆる情報を表示できます。 ただし、閲覧者ロールでは変更を加えることはできません。 |
| Contributor |ロールの追加と削除を除くすべて |ACL によって管理 |共同作業者ロールでは、デプロイ、アラートの作成と管理など、アカウントの一部の側面を管理できます。 共同作業者ロールでは、ロールを追加または削除することはできません。 |
| User Access Administrator |ロールの追加と削除 |ACL によって管理 |ユーザー アクセス管理者ロールでは、アカウントへのユーザー アクセスを管理できます。 |

手順については、「 [ユーザーまたはセキュリティ グループを Azure Data Lake Store アカウントに割り当てる](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts)」をご覧ください。

### <a name="using-acls-for-operations-on-file-systems"></a>ACL を使用したファイル システムでの操作
Data Lake Store は Hadoop 分散ファイル システム (HDFS) と同じく階層構造を備えたファイル システムであり、 [POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)をサポートしています。 所有者ロール、所有者グループ、その他のユーザーおよびグループのリソースに対する読み取り (r)、書き込み (w) および実行 (x) のアクセス許可を制御します。 Data Lake Store では、ルート フォルダー、サブフォルダー、個々のファイルで ACL を有効にすることができます。 Data Lake Store のコンテキストにおける ACL のしくみの詳細については、[Data Lake Store のアクセス制御](data-lake-store-access-control.md)に関する記事をご覧ください。

複数ユーザーを対象に ACL を定義するときは、 [セキュリティ グループ](../active-directory/active-directory-groups-create-azure-portal.md)を使うことをお勧めします。 ユーザーをセキュリティ グループに追加し、ファイルまたはフォルダーの ACL をそのセキュリティ グループに割り当てます。 割り当てられたアクセス許可のエントリ数は最大 28 に制限されているため、これは割り当てられたアクセス許可を提供する場合に役立ちます。 Azure Active Directory のセキュリティ グループを使用して Data Lake Store に保存されているデータのセキュリティを強化する方法について詳しくは、「 [ユーザーまたはセキュリティ グループを ACL として Azure Data Lake Store ファイル システムに割り当てる](data-lake-store-secure-data.md#filepermissions)」をご覧ください。

![アクセス許可の一覧表示](./media/data-lake-store-security-overview/adl.acl.2.png "アクセス許可の一覧表示")

## <a name="network-isolation"></a>ネットワークの分離
データ ストアに対するアクセスをネットワーク レベルで制御する場合にも、Data Lake Store が役立ちます。 ファイアウォールを設定し、信頼されたクライアントの IP アドレス範囲を定義できます。 IP アドレス範囲が定義されていると、IP アドレスがその範囲に該当するクライアントだけが Data Lake Store に接続できます。

![ファイアウォール設定と IP アクセス](./media/data-lake-store-security-overview/firewall-ip-access.png "ファイアウォール設定と IP アドレス")

## <a name="data-protection"></a>データ保護
Azure Data Lake Store は、データをそのライフ サイクル全体にわたって保護します。 転送中のデータについては、Data Lake Store では業界標準のトランスポート層セキュリティ (TLS) プロトコルを使用して、ネットワーク上のデータを保護します。

![Data Lake Store での暗号化](./media/data-lake-store-security-overview/adls-encryption.png "Data Lake Store での暗号化")

Data Lake Store では、アカウントに格納されているデータを暗号化することもできます。 データを暗号化するかどうかを選択できます。 暗号化することを選択した場合、Data Lake Store に保存されているデータは、永続メディアに保存される前に暗号化されます。 その場合、データの永続化の前の暗号化と取得前の暗号化解除が Data Lake Store によって自動的に行われるため、この操作はデータにアクセスするクライアントに完全に透過的になります。 データの暗号化および暗号化解除を行うためにクライアント側でコードを変更する必要はありません。

キーの管理に関して、Data Lake Store には、Data Lake Store に格納されているすべてのデータを暗号化解除するのに必要なマスター暗号化キー (MEK) を管理するための 2 つのモードが用意されています。 Data Lake Store に MEK の管理を任せることも、Azure Key Vault アカウントを使用して MEK の所有権を保持することもできます。 キー管理のモードは、Data Lake Store アカウントの作成時に指定します。 暗号化関連の構成を提供する方法の詳細については、「[Azure Portal で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」をご覧ください。

## <a name="activity-and-diagnostic-logs"></a>アクティビティ ログと診断ログ
アカウント管理関連のアクティビティまたはデータ関連のアクティビティのどちらのログを探しているかに応じて、アクティビティ ログまたは診断ログを使用できます。

* アカウント管理関連のアクティビティは Azure Resource Manager API を使用し、アクティビティ ログを使用して Azure Portal に表示されます。
* データ関連のアクティビティでは、WebHDFS REST API を使用します。これらのアクティビティは、診断ログを使用して Azure ポータルに表示されます。

### <a name="activity-log"></a>アクティビティ ログ
組織は、特定のインシデントを詳しく調査する必要がある場合、規制に準拠するためにアカウント管理アクティビティの十分な監査証跡が必要になることがあります。 Data Lake Store は組み込みの監視機能を備えており、すべてのアカウント管理アクティビティをログに記録します。

アカウント管理の監査証跡では、ログが必要な列を選んで表示できます。 また、アクティビティ ログを Azure Storage にエクスポートすることもできます。

![アクティビティ ログ](./media/data-lake-store-security-overview/activity-logs.png "アクティビティ ログ")

アクティビティ ログの操作の詳細については、「[リソースのアクションを監査するアクティビティ ログの表示](../azure-resource-manager/resource-group-audit.md)」を参照してください。

### <a name="diagnostics-logs"></a>[診断ログ]
Azure Portal でデータ アクセス監査および診断ログを有効にし、それらのログを Azure Blob Storage アカウント、イベント ハブ、または Log Analytics に送信できます。

![診断ログ](./media/data-lake-store-security-overview/diagnostic-logs.png "診断ログ")

Azure Data Lake Store の診断ログの操作の詳細については、「[Data Lake Store の診断ログへのアクセス](data-lake-store-diagnostic-logs.md)」を参照してください。

## <a name="summary"></a>まとめ
企業顧客は、セキュリティで保護された使いやすいデータ分析クラウド プラットフォームを求めています。 Azure Data Lake Store は、Azure Active Directory 統合による ID 管理と認証、ACL ベースの承認、ネットワーク分離、転送中のデータや保存データの暗号化、および監査を通して、これらの要件への対処に役立つように設計されています。

Data Lake Store に追加を希望する機能がある場合には、 [Data Lake Store UserVoice フォーラム](https://feedback.azure.com/forums/327234-data-lake)からフィードバックをお送りください。

## <a name="see-also"></a>関連項目
* [Azure Data Lake Store の概要](data-lake-store-overview.md)
* [Data Lake Store の使用](data-lake-store-get-started-portal.md)
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)

