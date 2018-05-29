---
title: Azure SQL Database のセキュリティの概要 | Microsoft Docs
description: Azure SQL Database と SQL Server のセキュリティについて説明します。クラウドとオンプレミス SQL Server の違いなどが含まれます。
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/20/2018
ms.author: giladm
ms.openlocfilehash: ea76bc4cc182902fb5618d23358579ec229a5ef4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189719"
---
# <a name="securing-your-sql-database"></a>SQL Database の保護

この記事は、Azure SQL Database を使用してアプリケーションのデータ層をセキュリティで保護するための基本事項について説明します。 特にこの記事では、データの保護、アクセスの制御、プロアクティブな監視を行うためのリソースの概要を説明します。 

SQL のすべてのエディションで使用できるセキュリティ機能の概要については、 [SQL Server Database Engine と Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)を参照してください。 [セキュリティと Azure SQL Database のテクニカル ホワイト ペーパー](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF) でも追加情報を参照できます。

## <a name="protect-data"></a>データの保護

### <a name="encryption"></a>暗号化
SQL Database は、暗号化によってデータのセキュリティを保護します。移動中のデータには[トランスポート層セキュリティ](https://support.microsoft.com/kb/3135244)を使用し、保存データには [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用し、使用中のデータには [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) を使用します。 

> [!IMPORTANT]
>データがデータベースとの間で "送受信中" である間は、常に Azure SQL Database への接続をすべて (SSL/TLS を使用して) 暗号化する必要があります。 アプリケーションの接続文字列で、接続を暗号化するパラメーターを指定する必要があります。サーバーの証明書は、信頼*しないで*ください (Azure Portal から接続文字列をコピーする場合は、自動的にこのようになります)。そうしないと、接続でサーバーの ID が検証されず、"man-in-the-middle" 攻撃を受けやすくなります。 たとえば ADO.NET ドライバーの場合、これらの接続文字列のパラメーターは、**Encrypt=True** と **TrustServerCertificate=False** です。 TLS と接続の詳細については、[TLS に関する考慮事項](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)に関するセクションを参照してください。

その他の方法でデータを暗号化するには、次を検討してください。

* [セルレベルの暗号化](https://msdn.microsoft.com/library/ms179331.aspx) により、暗号化キーが異なるデータの特定の列またはセルを暗号化できます。
* ハードウェア セキュリティ モジュールか、暗号化キー階層のサーバーの中央管理が必要な場合は、 [Azure VM で Azure Key Vault と SQL Server](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)を併用することを検討してください。

### <a name="data-discovery--classification"></a>データの検出と分類
データの検出と分類 (現在プレビュー段階) では、Azure SQL Database に組み込まれる、データベースの機微なデータの検出、分類、ラベル付け、保護を行う高度な機能が用意されます。 最も機微なデータの検出と分類 (ビジネス/金融、医療、PII など) は、組織の情報保護水準において極めて重要な役割を果たします。 これは、以下のケースのインフラストラクチャとして機能します。

- さまざまなセキュリティ シナリオ (機微なデータに対する異常なアクセスの監視 (監査) とアラートなど)。
- 非常に機微なデータを含むデータベースへのアクセスの制御と、セキュリティの強化。
- データのプライバシー基準および規制のコンプライアンス要件を満たす支援。

詳細については、[SQL DB データの検出と分類の概要](sql-database-data-discovery-and-classification.md)に関するページを参照してください。 

## <a name="control-access"></a>アクセスの制御
SQL Database では、ファイアウォール規則、ユーザーに ID の入力を求める認証メカニズム、データに対する承認 (ロール ベースのメンバーシップとアクセス許可のほか、行レベルのセキュリティと動的データ マスクを使用) を使用して、データベースへのアクセスを制限することで、データを保護します。 SQL Database でのアクセス制御機能の使用については、[アクセス制御](sql-database-control-access.md)に関するページを参照してください。

> [!IMPORTANT]
> Azure でのデータベースと論理サーバーの管理は、ポータル ユーザー アカウントのロール割り当てによって制御されます。 この記事の詳細については、「[Azure Portal でのロール ベースのアクセス制御](../role-based-access-control/overview.md)」を参照してください。
>

### <a name="firewall-and-firewall-rules"></a>ファイアウォールとファイアウォール規則
データを保護するため、ファイアウォールは、どのコンピューターに権限を持たせるかが[ファイアウォール規則](sql-database-firewall-configure.md)によって指定されるまで、データベース サーバーへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。

### <a name="authentication"></a>認証
SQL データベース認証とは、データベースへの接続時に ID を証明する方法のことです。 SQL Database は、2 種類の認証をサポートしています。
* **SQL 認証**。ユーザー名とパスワードを使用します。 データベースの論理サーバーを作成したときに、ユーザー名とパスワードによる "サーバー管理" ログインを指定したとします。 これらの資格情報を使用すると、データベース所有者、つまり "dbo" として、そのサーバーにある任意のデータベースを認証できます。 
* **Azure Active Directory 認証**。Azure Active Directory で管理されている ID を使用します。管理および統合されたドメインの場合にサポートされます。 [可能であれば](https://msdn.microsoft.com/library/ms144284.aspx)、Active Directory 認証 (統合セキュリティ) を使用します。 Azure Active Directory 認証を使用する場合は、"Azure AD 管理者" という、Azure AD ユーザーとグループを管理できるサーバー管理者を別に作成する必要があります。 この管理者は、通常のサーバー管理者が実行できるすべての操作も実行できます。 Azure AD 管理者を作成して Azure Active Directory 認証を有効にする方法のチュートリアルについては、「 [Azure Active Directory の認証を使用して SQL Database に接続する](sql-database-aad-authentication.md) 」を参照してください。

### <a name="authorization"></a>承認
承認とは、Azure SQL Database でユーザーが許可される操作を示すものであり、ユーザー アカウントのデータベース ロールのメンバーシップとオブジェクト レベルのアクセス許可によって制御されます。 ベスト プラクティスとして、必要最低限の特権をユーザーに付与することをお勧めします。 接続しているサーバー管理者のアカウントは db_owner のメンバーであり、データベース内ですべての操作を実行する権限を持ちます。 スキーマのアップグレードやその他の管理操作をデプロイするために、このアカウントを保存します。 アクセス許可が限定された "ApplicationUser" アカウントを使用して、アプリケーションで必要な最小限の特権により、アプリケーションをデータベースに接続します。

### <a name="row-level-security"></a>行レベルのセキュリティ
行レベルのセキュリティを使用すると、クエリを実行しているユーザーの特性 (たとえば、グループ メンバーシップや実行コンテキストなど) に基づいて、データベース テーブル内の行へのアクセスを制御できます。 詳細については、「[行レベルのセキュリティ](https://msdn.microsoft.com/library/dn765131)」を参照してください。

### <a name="dynamic-data-masking"></a>動的データ マスク 
SQL Database 動的データ マスクは、特権のないユーザーに対してデリケートなデータをマスクし、データの公開を制限します。 動的データ マスクは、Azure SQL Database 内で機密の可能性があるデータを自動的に検出し、アプリケーション層への影響を最小限に抑えながらそれらのフィールドをマスクするための具体的な推奨事項を提示します。 指定されたデータベース フィールドに対するクエリの結果セットに含まれる機密データを難読化しますが、データベース内のデータは変更しません。 詳細については、「[SQL Database の動的データ マスク](sql-database-dynamic-data-masking-get-started.md)」を参照してください。

## <a name="proactive-monitoring"></a>プロアクティブな監視
SQL Database には、データのセキュリティを保護するために、監査および脅威検出機能が用意されています。 

### <a name="auditing"></a>監査
SQL Database Auditing は、データベース イベントを Azure Storage アカウントの監査ログに記録することによって、データベース アクティビティを追跡し、規制の遵守を維持できるようにします。 Auditing を使用すると、実行中のデータベース アクティビティを把握し、過去のアクティビティを分析および調査して、潜在的な脅威、不正使用の可能性、およびセキュリティ違反を特定することができます。 詳細については、「[SQL Database 監査の使用](sql-database-auditing.md)」を参照してください。  

### <a name="threat-detection"></a>脅威の検出
脅威の検出では、Azure SQL Database サービスに組み込まれたセキュリティ インテリジェンスの追加レイヤーを提供することにより、監査を補完します。このレイヤーでは、データベースにアクセスしたりデータベースを悪用したりしようとする、異常で有害な可能性がある動作を検出します。 不審なアクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセス パターンについて、アラートが送信されます。 脅威の検出のアラートは、[Azure Security Center](https://azure.microsoft.com/services/security-center/) で見ることができます。不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法が表示されます。 脅威の検出の料金は、15 ドル/サーバー/月です。 最初の 60 日間は無料です。 詳細については、「 [SQL Database 脅威の検出の概要](sql-database-threat-detection.md)」をご覧ください。
 
## <a name="compliance"></a>コンプライアンス
アプリケーションがさまざまなセキュリティ要件を満たすために役立つ上記の機能以外にも、Azure SQL Database は定期的な監査に参加し、さまざまなコンプライアンス基準に認定されています。 詳細については、「[Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/)」をご覧ください。ここから最新の [SQL Database コンプライアンス証明書](https://azure.microsoft.com/support/trust-center/services/)の一覧を入手できます。


## <a name="security-management"></a>セキュリティ管理

SQL Database には、データベース スキャン機能と [SQL の脆弱性評価](sql-vulnerability-assessment.md)を使用する一元化されたセキュリティ ダッシュボードがあるので、データのセキュリティ管理に役立ちます。

**脆弱性評価**: [SQL の脆弱性評価](sql-vulnerability-assessment.md) (現在プレビュー中) は、Azure SQL Database に組み込まれている構成が簡単なツールです。潜在的なデータベースの脆弱性の検出、追跡、修復に役立ちます。 脆弱性評価では、データベースに対して脆弱性スキャンが実行され、レポートが生成されます。このレポートで、セキュリティの問題を解決し、データベースのセキュリティを強化できる実践的な手順を含め、セキュリティの状態を把握できます。 アクセス許可の構成、機能の構成、データベースの設定の許容されるベースラインを設定することで、環境に合わせて評価レポートをカスタマイズできます。 脆弱性評価は以下のために役立ちます。

- データベース スキャン レポートが必要なコンプライアンス要件を満たします。 

- データのプライバシー基準を満たします。 

- 変更の追跡が困難な動的データベース環境を監視します。

詳細については、「[SQL の脆弱性評価](sql-vulnerability-assessment.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- SQL Database でのアクセス制御機能の使用については、[アクセス制御](sql-database-control-access.md)に関するページを参照してください。
- データベース監査については、[SQL Database の監査に関する記事](sql-database-auditing.md)をご覧ください。
- 脅威の検出については、「[SQL Database Threat Detection](sql-database-threat-detection.md)」(SQL Database の脅威の検出) をご覧ください。
