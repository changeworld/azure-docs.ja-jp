<properties 
   pageTitle="Data Lake Store のセキュリティの概要 | Microsoft Azure" 
   description="Azure Data Lake Store がセキュリティで保護されたビッグ データ ストアであることを理解します。" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/18/2016"
   ms.author="nitinme"/>

# Azure Data Lake Store のセキュリティ

ビジネスの洞察を得て賢明な意思決定を行うために、多くの企業がビッグ データ分析を活用しています。これらの組織では、多様なユーザーの増加に伴い、複雑で規制された環境である可能性があります。そのため、企業は適切なユーザーに適切なレベルのアクセス権を付与して、重要なビジネス データが安全に保存されていることを確認することが不可欠となります。Azure Data Lake Store は、これらのセキュリティ要件を念頭に置いて設計されています。この記事では、Azure Data Lake Store (ADLS) の次のようなセキュリティ機能について説明します。

* 認証
* 承認
* ネットワークの分離
* データ保護
* 監査

 
## 認証と ID 管理

認証は、Data Lake Store や Data Lake Store に接続するサービスとやり取りするときに、ユーザーが自分の ID を証明するプロセスです。Data Lake Store では、ID 管理と認証に [Azure Active Directory](../active-directory/active-directory-whatis.md) (AAD) を使用します。Azure Active Directory は、ユーザーとグループの管理を簡素化する包括的な ID およびアクセス管理クラウド ソリューションです。

現在、各 Azure サブスクリプションを Azure Active Directory に関連付けることができます。Azure ポータル、コマンド ライン ツール、または Data Lake Store SDK を使用して構築されたクライアント アプリケーションを使用して Data Lake Store にアクセスできるのは、このディレクトリで定義されているユーザーとサービス ID に限られます。一元化されたアクセス制御メカニズムとして Azure Active Directory を使用する主な利点は次のとおりです。

* ID のライフ サイクル管理が簡素化されます。ユーザーまたはサービスの ID (サービス プリンシパル ID) を迅速に作成することができ、ディレクトリでアカウントを削除または無効にするだけで ID を取り消すことができます。

* ユーザーのサインインとトランザクションのセキュリティ レイヤーを追加する[多要素認証](../multi-factor-authentication/multi-factor-authentication.md)がサポートされます。

* ユーザーは、OAuth や OpenID などの標準オープン プロトコルを使用して、あらゆるクライアントから認証できます。

* エンタープライズ ディレクトリ サービスおよびクラウド ID プロバイダーとのフェデレーションが可能になります。

## 承認とアクセス制御

Azure Data Lake Store にアクセスするために、ユーザーが AAD によって認証されたら、承認によって Data Lake Store のアクセス許可が制御されます。Data Lake Store では、アカウント関連アクティビティとデータ関連アクティビティの承認は、次のように区別されます。

* アカウント管理に Azure が提供する[ロールベースのアクセス制御 ](../active-directory/role-based-access-control-what-is.md) (RBAC)
* ストア内のデータにアクセスするための POSIX ACL。

### RBAC を使用したアカウント管理

4 つの基本的なロールが既定で定義されています。これらのロールでは、ポータル、PowerShell コマンドレット、REST API を使用して、Data Lake Store アカウントに対してさまざまな操作を実行できます。**所有者**ロールと**共同作成者**ロールは、アカウントに対してさまざまな管理機能を実行できます。データを操作するだけのユーザーは、**閲覧者**ロールに追加できます。

![RBAC のロール](./media/data-lake-store-security-overview/rbac-roles.png "RBAC のロール")

これらのロールの割り当てはアカウント管理を目的としていますが、ロールによっては、データ アクセス許可に影響を及ぼす場合もあります。ユーザーがファイル システムで実行できる操作については、アクセス制御リスト (ACL) を使用する必要があります。これらのロールの管理権限とデータ アクセス権限の概要を次に示します。

| ロール | 管理権限 | データ アクセス権限 | 説明 |
|--------------------------|---------------------------------|--------------------|-------------|
| ロールの割り当てなし | なし | ACL によって管理 | この場合、ユーザーは Azure ポータルまたは Azure PowerShell コマンドレットを使用して Data Lake Store を参照することはできません。このようなユーザーは、コマンド ライン ツールだけを利用する必要があります。 |
| 所有者 | すべて | すべて | 所有者はスーパーユーザーです。したがって、所有者ロールはすべてを管理することができ、データへのフル アクセス権を持ちます。 | 
| 閲覧者 | 読み取り専用 | ACL によって管理 | 閲覧者は、どのユーザーがどのロールに割り当てられているかなど、アカウント管理に関するすべての情報を表示できますが、変更を加えることはできません。 |
| 共同作成者 | ロールの追加と削除を除くすべて | ACL によって管理 | 共同作業者は、警告の作成/管理やデプロイメントなど、アカウントの他の側面を管理できます。共同作業者は、ロールを追加または削除することはできません。 |
| ユーザーアクセスの管理者 | ロールの追加と削除 | ACL によって管理 | ユーザー アクセス管理者は、アカウントへのユーザー アクセスを管理できます。 |

手順については、「[ユーザーまたはセキュリティ グループを Azure Data Lake Store アカウントに割り当てる](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts)」をご覧ください。

### ACL を使用したファイル システムでの操作

Azure Data Lake Store は HDFS のような階層型ファイル システムであり、[POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists) をサポートしています。POSIX ACL を使用すると、グループや他のユーザー/グループを所有する所有者に、リソースに対する読み取り (r)、書き込み (w)、実行 (x) アクセス権限を付与することができます。Data Lake Store パブリック プレビュー (現在のリリース) では、ACL はルート フォルダーでのみ有効化されるので、ルート フォルダーに適用した ACL はすべての子フォルダー/ファイルにも適用されます。今後のリリースでは、任意のファイルやフォルダーで ACL を設定できるようになります。

多数のユーザーの ACL を定義する場合は、[セキュリティ グループ](../active-directory/active-directory-accessmanagement-manage-groups.md)を使用することをお勧めします。ユーザーをセキュリティ グループにグループ化し、ファイルとフォルダーの ACL をそのセキュリティ グループに割り当てます。これは、カスタム アクセスを提供する場合に役立ちます。カスタム アクセスの一部として追加できるエントリは最大 9 個に制限されているためです。AAD セキュリティ グループを使用して、Data Lake Store に保存されているデータをセキュリティで保護する方法の詳細については、「[ユーザーまたはセキュリティ グループを ACL として Azure Data Lake Store ファイル システムに割り当てる](data-lake-store-secure-data.md#filepermissions)」をご覧ください。

![標準アクセスとカスタム アクセスを一覧表示する](./media/data-lake-store-security-overview/adl.acl.2.png "標準アクセスとカスタム アクセスを一覧表示する")

## ネットワークの分離

Azure Data Lake Store では、データ ストアへのアクセスをネットワーク レベルでさらにロック ダウンできます。ファイアウォールを有効にし、信頼されたクライアントの IP アドレス範囲を定義できます。ファイアウォールが有効になると、IP アドレスが定義済みの範囲内にあるクライアントだけがストアに接続できます。

![ファイアウォール設定と IP アクセス](./media/data-lake-store-security-overview/firewall-ip-access.png "ファイアウォール設定と IP アドレス")

## データ保護

組織では、データ ライフ サイクル全体を通じて、ビジネスに不可欠なデータのセキュリティを確保する必要があります。転送中のデータについては、Data Lake Store では業界標準の TLS (トランスポート層セキュリティ プロトコル) を使用して、クライアントと Data Lake Security 間でデータを保護します。

今後のリリースで保存データのデータ保護も提供されます。

## 監査ログと診断ログ

管理関連のアクティビティとデータ関連のアクティビティのどちらのログを探しているかに応じて、監査ログまたは診断ログを使用できます。

*  管理関連のアクティビティでは、Azure Resource Manager API を使用します。これらのアクティビティは、監査ログを使用して Azure ポータルに表示されます。
*  データ関連のアクティビティでは、WebHDFS API を使用します。これらのアクティビティは、診断ログを使用して Azure ポータルに表示されます。

### 監査ログ

組織では、規制に準拠するために、インシデントを詳しく調査する必要がある場合に適切な監査証跡が必要になります。Data Lake Store には、すべてのアカウント管理アクティビティをログに記録する監視および監査機能が組み込まれています。

アカウント管理の監査証跡では、ログの対象となる列を表示および選択できます。また、監査ログを Azure Storage にエクスポートすることもできます。

![監査ログ](./media/data-lake-store-security-overview/audit-logs.png "監査ログ")

### 診断ログ

Azure ポータルからデータ アクセスの監査証跡を有効にし (**[診断設定]**)、ログを保存する Azure BLOB ストレージ アカウントを提供できます。

![診断ログ](./media/data-lake-store-security-overview/diagnostic-logs.png "診断ログ")

診断設定を有効にしたら、**[診断ログ]** タブでログを確認できます。

## 概要

企業顧客は、セキュリティで保護された使いやすいデータ分析クラウド プラットフォームを求めています。Azure Data Lake Store は、Azure Active Directory の統合による ID 管理と認証、ACL ベースの承認、ネットワークの分離、転送中のデータの暗号化と保存データの暗号化 (将来提供)、および監査によって、これらの要件に対処するように設計されています。

Data Lake Store の新機能を確認する場合は、[UserVoice フォーラム](https://feedback.azure.com/forums/327234-data-lake)でフィードバックをお送りください。

## 関連項目

- [Azure Data Lake Store の概要](data-lake-store-overview.md)
- [Data Lake Store の使用](data-lake-store-get-started-portal.md)
- [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0720_2016-->