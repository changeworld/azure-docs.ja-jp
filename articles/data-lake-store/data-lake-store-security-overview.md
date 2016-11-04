---
title: Data Lake Store のセキュリティの概要 | Microsoft Docs
description: Azure Data Lake Store がセキュリティが強化されたビッグ データ ストアであることを学習します
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2016
ms.author: nitinme

---
# Azure Data Lake Store のセキュリティ
多くの企業が、ビッグ データの分析によってビジネスに関する洞察を獲得し、それを意思決定に活かしています。ただ、一部の組織はユーザーが多様化し、その数も増加の一途を辿っているばかりか、各種の規制が存在する複雑な環境に直面しています。このため、重要なビジネス データはこれまで以上に強固なセキュリティをもって保管すると同時に、個々のユーザーには適切な水準のアクセス権を付与することがきわめて重要になっています。Azure Data Lake Store は、このようなセキュリティ要件を満たすことを目指して設計されています。この記事では、以下に示す Data Lake Store のセキュリティ機能について説明します。

* 認証
* 承認
* ネットワークの分離
* データ保護
* 監査

## 認証と ID 管理
認証とは、ユーザーが Data Lake Store または Data Lake Store に接続しているサービスとやり取りするにあたり、ユーザーの ID を確認するプロセスを指します。Data Lake Store では、ID の管理と認証に [Azure Active Directory](../active-directory/active-directory-whatis.md) を使用します。Azure Active Directory は、ユーザーとグループの管理を簡素化する包括的な ID およびアクセス管理クラウド ソリューションです。

Azure サブスクリプションはそれぞれ、Azure Active Directory のインスタンスに関連付けることができます。ユーザーとサービスの ID のうち、Azure Active Directory サービスで定義されているものに限り、Data Lake Store アカウントにアクセスできます。アカウントへのアクセスには、Azure ポータル、コマンドライン ツールのほか、組織が Azure Data Lake Store SDK を使って作成したクライアント アプリケーションを使用できます。一元化されたアクセス制御メカニズムとして Azure Active Directory を使用する主な利点は次のとおりです。

* ID ライフサイクル管理の簡素化。ユーザーまたはサービスの ID (サービス プリンシパル ID) を迅速に作成することができ、ディレクトリでアカウントを削除または無効にするだけで ID を取り消すことができます。
* 多要素認証。[多要素認証](../multi-factor-authentication/multi-factor-authentication.md)により、ユーザーのサインインとトランザクションにセキュリティ レイヤーを追加できます。
* OAuth、OpenID などの標準的なオープン プロトコルを使ったクライアント認証。
* エンタープライズ ディレクトリ サービスおよびクラウド ID プロバイダーとのフェデレーション。

## 承認とアクセス制御
Azure Active Directory の認証によってユーザーが Azure Data Lake Store にアクセスできるようになった後は、承認によって Data Lake Store のアクセス許可を制御します。Data Lake Store では、アカウント関連アクティビティとデータ関連アクティビティの承認が次のように区別されます。

* アカウント管理に Azure が提供する[ロールベースのアクセス制御 ](../active-directory/role-based-access-control-what-is.md) (RBAC)
* ストア内のデータにアクセスするための POSIX ACL

### RBAC を使用したアカウント管理
Data Lake Store には既定で基本のロールが 4 つ定義されています。これらのロールでは、Azure ポータル、PowerShell コマンドレット、REST API を使用して Data Lake Store アカウントに実行できる操作が異なります。所有者ロールと共同作成者ロールは、アカウントに対してさまざまな管理機能を実行できます。データを操作するだけのユーザーには、閲覧者ロールを割り当てます。

![RBAC のロール](./media/data-lake-store-security-overview/rbac-roles.png "RBAC のロール")

ロールはアカウント管理のために割り当てられるものですが、一部のロールはデータへのアクセスにも影響しますので注意してください。ユーザーがファイル システムに対して実行できる操作へのアクセス制御には、ACL を使用する必要があります。以下の表は、既定のロールの管理権限とデータ アクセス権限の概要を示したものです。

| ロール | 管理権限 | データ アクセス権限 | 説明 |
| --- | --- | --- | --- |
| ロールの割り当てなし |なし |ACL によって管理 |Azure ポータルまたは Azure PowerShell コマンドレットを使って Data Lake Store を参照することはできません。ユーザーは、コマンドライン ツールのみ使用できます。 |
| 所有者 |すべて |すべて |所有者ロールは、スーパーユーザーです。このロールではあらゆるものを管理できるほか、データに対するフル アクセスが認められます。 |
| 閲覧者 |読み取り専用 |ACL によって管理 |閲覧者ロールでは、どのユーザーがどのロールに割り当てられているかなど、アカウント管理に関するあらゆる情報を表示できます。ただし、閲覧者ロールでは変更を加えることはできません。 |
| 共同作成者 |ロールの追加と削除を除くすべて |ACL によって管理 |共同作業者ロールでは、デプロイ、アラートの作成と管理など、アカウントの一部の側面を管理できます。共同作業者ロールでは、ロールを追加または削除することはできません。 |
| ユーザーアクセスの管理者 |ロールの追加と削除 |ACL によって管理 |ユーザー アクセス管理者ロールでは、アカウントへのユーザー アクセスを管理できます。 |

手順については、「[ユーザーまたはセキュリティ グループを Azure Data Lake Store アカウントに割り当てる](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts)」をご覧ください。

### ACL を使用したファイル システムでの操作
Data Lake Store は Hadoop 分散ファイル システム (HDFS) と同じく階層構造を備えたファイル システムであり、[POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists) をサポートしています。所有者ロール、所有者グループ、その他のユーザーおよびグループのリソースに対する読み取り (r)、書き込み (w) および実行 (x) のアクセス許可を制御します。Data Lake Store パブリック プレビュー (現在のリリース) では、ルート フォルダー、サブフォルダーのほか、個々のファイルでも ACL が有効になっています。ルート フォルダーに適用した ACL は、その小フォルダーとファイルにもすべて適用されます。

複数ユーザーを対象に ACL を定義するときは、[セキュリティ グループ](../active-directory/active-directory-accessmanagement-manage-groups.md)を使うことをお勧めします。ユーザーをセキュリティ グループに追加し、ファイルまたはフォルダーの ACL をそのセキュリティ グループに割り当てます。カスタム アクセスで追加できるエントリは 9 個までという制約があるため、カスタム アクセスを使用する場合には、この機能が便利です。Azure Active Directory のセキュリティ グループを使用して Data Lake Store に保存されているデータのセキュリティを強化する方法について詳しくは、「[ユーザーまたはセキュリティ グループを ACL として Azure Data Lake Store ファイル システムに割り当てる](data-lake-store-secure-data.md#filepermissions)」をご覧ください。

![標準アクセスとカスタム アクセスを一覧表示する](./media/data-lake-store-security-overview/adl.acl.2.png "標準アクセスとカスタム アクセスを一覧表示する")

## ネットワークの分離
データ ストアに対するアクセスをネットワーク レベルで制御する場合にも、Data Lake Store が役立ちます。ファイアウォールを設定し、信頼されたクライアントの IP アドレス範囲を定義できます。IP アドレス範囲が定義されていると、IP アドレスがその範囲に該当するクライアントだけが Data Lake Store に接続できます。

![ファイアウォール設定と IP アクセス](./media/data-lake-store-security-overview/firewall-ip-access.png "ファイアウォール設定と IP アドレス")

## データ保護
組織では、データのライフサイクル全体を通じて、ビジネスに不可欠なデータのセキュリティを確保する必要があります。転送中のデータについては、Data Lake Store では業界標準のトランスポート層セキュリティ (TLS) プロトコルを使用して、クライアントと Data Lake Store の間を移動するデータを保護します。

今後のリリースでは、保存データのデータ保護も提供する予定です。

## 監査ログと診断ログ
管理関連のアクティビティとデータ関連のアクティビティのどちらのログを探しているかに応じて、監査ログまたは診断ログを使用できます。

* 管理関連のアクティビティでは、Azure Resource Manager API を使用します。これらのアクティビティは、監査ログを使用して Azure ポータルに表示されます。
* データ関連のアクティビティでは、WebHDFS REST API を使用します。これらのアクティビティは、診断ログを使用して Azure ポータルに表示されます。

### 監査ログ
組織によっては、規制に準拠する目的から、特定のインシデントを詳しく調査する必要が生じた場合に十分な監査証跡が必要になることがあります。Data Lake Store には監視および監査機能が組み込まれており、すべてのアカウント管理アクティビティがログに記録されます。

アカウント管理の監査証跡では、ログが必要な列を選んで表示できます。また、監査ログを Azure Storage にエクスポートすることもできます。

![監査ログ](./media/data-lake-store-security-overview/audit-logs.png "監査ログ")

### 診断ログ
Azure ポータル (の [診断設定]) でデータ アクセスの監査証跡を設定し、ログを保存する Azure BLOB ストレージ アカウントを作成できます。

![診断ログ](./media/data-lake-store-security-overview/diagnostic-logs.png "診断ログ")

診断設定を構成した後は、**[診断ログ]** タブでログを確認できます。

Azure Data Lake Store の診断ログの利用について詳しくは、「[Azure Data Lake Store の診断ログへのアクセス](data-lake-store-diagnostic-logs.md)」を参照してください。

## 概要
企業顧客は、セキュリティで保護された使いやすいデータ分析クラウド プラットフォームを求めています。Azure Data Lake Store は、Azure Active Directory の統合による ID 管理と認証、ACL ベースの承認、ネットワークの分離、転送中のデータの暗号化と保存データの暗号化 (将来提供)、および監査によって、これらの要件に対処するように設計されています。

Data Lake Store に追加を希望する機能がある場合には、[Data Lake Store UserVoice フォーラム](https://feedback.azure.com/forums/327234-data-lake)からフィードバックをお送りください。

## 関連項目
* [Azure Data Lake Store の概要](data-lake-store-overview.md)
* [Data Lake Store の使用](data-lake-store-get-started-portal.md)
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0914_2016-->