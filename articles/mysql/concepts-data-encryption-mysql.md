---
title: カスタマー マネージド キーを使用した Azure Database for MySQL のデータの暗号化
description: 'カスタマー マネージド キーを使用した Azure Database for MySQL のデータ暗号化を使用すると、保存データの保護に独自のキーを使用 (BYOK: Bring Your Own Key) でき、組織がキーとデータの管理における職務の分離を実施できるようになります。'
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 7c54b3010b42d56ffa9b701b76c7aef51095404c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028652"
---
# <a name="azure-database-for-mysql-data-encryption-with-customer-managed-key"></a>カスタマー マネージド キーを使用した Azure Database for MySQL のデータの暗号化

> [!NOTE]
> この時点で、この機能を使用するためにアクセスを要求する必要があります。 それを行うには、AskAzureDBforMySQL@service.microsoft.com にお問い合わせください。

カスタマー マネージド キーを使用した Azure Database for MySQL のデータ暗号化を使用すると、保存データの保護に独自のキーを使用 (BYOK: Bring Your Own Key) でき、組織がキーとデータの管理における職務の分離を実施できるようになります。 カスタマー マネージド暗号化では、キーのライフサイクル (キーの作成、アップロード、ローテーション、削除)、キーの使用アクセス許可、およびキーに対する操作の監査は、顧客の責任となり、顧客がこれらを完全に制御できます。

Azure Database for MySQL では、データの暗号化はサーバーレベルで設定されます。 この形式のデータ暗号化では、キーは、データベース暗号化キー (DEK) の暗号化に使用されます。DEK は、顧客が所有し、顧客が管理する [Azure Key Vault (AKV)](../key-vault/key-Vault-secure-your-key-Vault.md) (クラウドベースの外部キー管理システム) に格納される、カスタマー マネージド非対称キーです。 AKV は、FIPS 140-2 Level 2 認定のハードウェア セキュリティ モジュール (HSM) をオプションで使用できる、RSA 暗号化キー向けの、可用性が高くスケーラブルでセキュリティで保護されたストレージです。 格納されているキーに直接アクセスすることはできませんが、キーを使用して暗号化および暗号化解除のサービスを認証されたエンティティに提供します。 キーは、Key Vault で生成したり、インポートしたり、[オンプレミスの HSM デバイスから Key Vault に転送](../key-vault/key-Vault-hsm-protected-keys.md)したりすることができます。

> [!NOTE]
> この機能は、Azure Database for MySQL で汎用およびメモリ最適化の価格レベルがサポートされているすべての Azure リージョンで使用できます。

## <a name="benefits"></a>メリット

Azure Database for MySQL のデータ暗号化には、次の利点があります。

* 暗号化キーの透過性の向上、きめ細かい制御、管理。
* Azure Key Vault でキーをホストすることによる、キーの一元的な管理と整理。
* 組織内でキーとデータの管理における職務の分離を実施することができる。
* 組織内のキー管理とデータ管理の分離。これにより、Key Vault 管理者は、暗号化されたデータベースにアクセスできないようにキーのアクセス許可を取り消すことができる。
* Azure Key Vault は Microsoft が暗号化キーを表示したり抽出したりできないように設計されているため、エンド カスタマーからの信頼が高まる。

## <a name="terminology-and-description"></a>用語と説明

**データ暗号化キー (DEK)** – パーティションまたはデータ ブロックの暗号化に使用する対称 AES256 キー。 データの各ブロックを異なるキーで暗号化することによって、暗号化分析攻撃がより困難になります。 DEK へのアクセスは、特定のブロックを暗号化および暗号化するリソース プロバイダーまたはアプリケーション インスタンス必要になります。 DEK が新しいキーで置き換えられた場合、新しいキーを使用して再暗号化する必要があるのは、関連ブロック内のデータのみになります。

**キーの暗号化キー (KEK)** - データ暗号化キーの暗号化に使用される暗号化キー。 Key Vault を離れることがないキーの暗号化キーを使用すると、データの暗号化キー自体を暗号化および管理できます。 KEK へのアクセス権を持つエンティティは、DEK を必要とするエンティティとは異なるエンティティに設定できます。 D の複合化には KEK が必要であるため、KEK を削除することにより DEK を効率的に削除できる有効な単一ポイントになります。

キーの暗号化キーで暗号化されたデータ暗号化キー (DEK) は個別に格納され、キーの暗号化キーへのアクセス許可を持つエンティティのみがそれらのデータ暗号化キーを暗号化解除できます。 詳細については、[保存時の暗号化のセキュリティ](../security/fundamentals/encryption-atrest.md)に関するページを参照してください。

## <a name="how-data-encryption-with-customer-managed-key-works"></a>カスタマー マネージド キーによるデータ暗号化のしくみ

![独自のキーの使用 (BYOK: Bring Your Own Key) の概要](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

MySQL サーバーで DEK の暗号化のために AKV に格納されているカスタマー マネージド キーを使用できるようにするには、Key Vault 管理者が一意の ID を使用して、サーバーに次のアクセス権を付与する必要があります。

* **get** - Key Vault 内のキーの公開部分とプロパティを取得します
* **wrapKey** - DEK を暗号化できるようにします
* **unwrapKey** - DEK を暗号化解除できるようにします

Key Vault 管理者は、後で監査できるように、[Key Vault の監査イベントのログ記録を有効](../azure-monitor/insights/azure-key-vault.md)にすることもできます。

Key Vault に格納されているカスタマー マネージド キーを使用するようにサーバーが構成されている場合、サーバーは暗号化のために DEK を Key Vault に送信します。 Key Vault から暗号化された DEK が返され、ユーザー データベースに格納されます。 同様に、必要に応じて、保護された DEK が暗号化解除のためにサーバーから Key Vault に送信されます。 ログ記録が有効になっている場合、監査者は Azure Monitor を使用して Key Vault の AuditEvent ログを確認できます。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL のデータ暗号化を構成するための要件

### <a name="requirements-for-configuring-akv"></a>AKV を構成するための要件

* Key Vault と Azure Database for MySQL は、同じ Azure Active Directory (AAD) テナントに属している必要があります。 Key Vault とサーバーのテナント間の対話はサポートされていません。 後でリソースを移動する場合は、データ暗号化を再構成する必要があります。 リソースの移動の詳細を確認してください。
* キー (またはキー コンテナー) を誤って削除したときのデータ損失から保護するには、Key Vault で論理的な削除機能を有効にする必要があります。 論理的に削除されたリソースは、顧客が復旧または削除しない限り、90 日間保持されます。 復旧アクションと消去アクションには、Key Vault のアクセス ポリシーに関連付けられた独自のアクセス許可があります。 論理的な削除機能は、既定ではオフになっており、PowerShell または CLI を使用して有効にすることができます。 Azure portal で有効にすることはできません。
* Azure Database for MySQL に対し、その一意のマネージド ID を使用して、**get、wrapKey、および unwrapKey** アクセス許可による Key Vault へのアクセスを付与します。 Azure portal を使用する場合、MySQL でのデータ暗号化を有効にすると、一意の ID が自動的に作成されます。 Azure portal を使用する場合の詳細な手順については、[MySQL のデータ暗号化の構成](howto-data-encryption-portal.md)に関するページを参照してください。

* AKV を使用してファイアウォールを使用する場合は、 *[Allow trusted Microsoft services to bypass the firewall]\(信頼された Microsoft サービスがファイアウォールをバイパスすることを許可する\)* オプションを有効にする必要があります。

### <a name="requirements-for-configuring-customer-key"></a>カスタマー キーを構成するための要件

* DEK の暗号化に使用されるカスタマー マネージド キーは、非対称の RSA 2028 のみです。
* キーがアクティブ化された日時 (設定する場合) は、過去の日付と時刻にする必要があります。 有効期限の日時 (設定する場合) は、将来の日付と時刻にする必要があります。
* キーは、"*有効*" 状態になっている必要があります。
* Key Vault に既存のキーをインポートする場合は、必ずサポートされているファイル形式 (`.pfx`、`.byok`、`.backup`) で提供してください。

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>カスタマー マネージド キーを使用してデータ暗号化を使用する場合の推奨事項

### <a name="recommendation-for-configuring-akv"></a>AKV を構成する場合の推奨事項

* Key Vault でリソース ロックを設定して、この重要なリソースを削除できるユーザーを制御し、誤削除や許可されていない削除を防ぎます。 リソース ロックの詳細を確認してください。
* すべての暗号化キーの監査およびレポートを有効にします。Key Vault で提供されるログは、他のセキュリティ情報およびイベント管理ツールに簡単に挿入できます。 Azure Monitor Log Analytics は、既に統合されているサービスの一例です。

* DEK のラップおよびラップ解除操作のアクセスを高速化するために、Key Vault と Azure Database for MySQL が同じリージョンにあることを確認します。

### <a name="recommendation-for-configuring-customer-managed-key"></a>カスタマー マネージド キーを構成する場合の推奨事項

* カスタマー マネージド キー (KEK) のコピーを安全な場所に保管するか、エスクロー サービスにエスクローします。

* キーが Key Vault で生成された場合は、初めて AKV でキーを使用する前に、キーのバックアップを作成します。 バックアップは Azure Key Vault にのみ復元できます。 [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey) コマンドの詳細については、こちらをご覧ください。

## <a name="inaccessible-customer-managed-key-condition"></a>"アクセス不可" カスタマー マネージド キー状態

Azure Key Vault (AKV) でカスタマー マネージド キーを使用してデータ暗号化が構成されている場合、サーバーをオンラインに保つには、このキーへの継続的なアクセスが必要です。 サーバーが AKV 内のカスタマー マネージド キーにアクセスできなくなると、サーバーは、10 分以内に対応するエラー メッセージですべての接続を拒否するようになり、サーバー状態を**アクセス不可**に変更します。 アクセス不可状態のデータベースで許可される唯一のアクションは、削除だけです。

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>Azure Key Vault (AKV) からの誤ったキー アクセスの失効

Key Vault に対する十分なアクセス権を持つユーザーが、次のことを行うことで、キーへのサーバー アクセスを誤って無効にしてしまうことがあります。

* サーバーから Key Vault の get、wrapKey、unwrapKey アクセス許可を取り消す
* キーを削除する
* Key Vault を削除する
* Key Vault のファイアウォール規則を変更する

* Azure Active Directory でサーバーのマネージド ID を削除する

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>Key Vault 内のカスタマー マネージド キーの監視

データベースの状態を監視したり、TDE 保護機能にアクセスできなくなった場合のアラートを有効にしたりするには、Azure の次の機能を構成します。

* [Azure Resource Health](../service-health/resource-health-overview.md) - 顧客キーへのアクセスが失われたアクセス不可能なデータベースには、データベースへの最初の接続が拒否された後、"アクセス不可" と表示されます。
* [アクティビティ ログ](../service-health/alerts-activity-log-service-notifications.md) - 顧客によって管理される Key Vault 内の顧客キーへのアクセスに失敗すると、アクティビティ ログにエントリが追加されます。 これらのイベントに対してアラートを作成すると、できるだけ早くアクセスを再開できるようになります。

* [アクション グループ](../azure-monitor/platform/action-groups.md)を定義して、設定 (たとえば、メール、SMS、プッシュ、音声、ロジック アプリ、Webhook、ITSM、Automation Runbook) に基づいて通知やアラートを送信できます。

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>Key Vault 内のカスタマー マネージド キーを使用した復元とレプリカの作成

Azure Database for MySQL が、Key Vault に格納されているカスタマー マネージド キーで暗号化されると、サーバーの (ローカルまたは geo 復元操作または読み取りレプリカを介して) 新しく作成されるコピーもすべて同じカスタマー マネージド キーで暗号化されます。 ただし、暗号化のための新しいカスタマー マネージド キーを反映するようにこれらを変更することができます。 カスタマー マネージド キーを変更すると、サーバーの古いバックアップが最新のキーを使用して開始されます。

復元中または読み取りレプリカの作成中にカスタマー マネージド データ暗号化の設定を確立する際の問題を回避するには、マスター サーバーと復元またはレプリカ サーバーで次の手順に従うことが重要です。

* マスター Azure Database for MySQL から、復元または読み取りレプリカの作成プロセスを開始します。
* 新しく作成されたサーバー (復元またはレプリカ) は、その一意の ID に Azure Key Vault (AKV) へのアクセス許可がまだ付与されていないため、アクセス不能状態のままになります
* 復元またはレプリカ サーバーでデータ暗号化設定のカスタマー マネージド キーを再検証して、AKV に格納されているキーへのラップおよびラップ解除アクセス許可が新しく作成されたサーバーに付与されるようにします。

* マスター サーバーと復元またはレプリカ サーバーでデータ暗号化が保持されるようにするには、上記の両方の手順を実行する必要があります。

## <a name="next-steps"></a>次のステップ

[Azure portal を使用して Azure Database for MySQL のカスタマー マネージド キーによるデータ暗号化を設定する](howto-data-encryption-portal.md)方法について学習します。
