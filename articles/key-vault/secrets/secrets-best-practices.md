---
title: シークレット管理のベスト プラクティス - Azure Key Vault | Microsoft Docs
description: Azure Key Vault でのシークレット管理のベスト プラクティスについて説明します。
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: mbaldwin
ms.openlocfilehash: 4735fcfd28830c8469ddfbed7b521a507b3ca521
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534685"
---
# <a name="best-practices-for-secrets-management-in-key-vault"></a>Key Vault でのシークレットの管理に関するベスト プラクティス

Azure Key Vault を使用すると、ユーザーはサービスやアプリケーションのパスワードやアクセス キーなどの資格情報をシークレットとして安全に保存できます。 キー コンテナー内のすべてのシークレットは、ソフトウェア キーで暗号化されています。 Key Vault を使用すると、ユーザーはアプリケーションにセキュリティ情報を格納する必要がなくなります。 アプリケーションにセキュリティ情報を格納する必要がなくなると、コードのこの情報部分を作成する必要がなくなります。

Key Vault に格納する必要があるシークレットの例:

- クライアント シークレットのシークレット
- Connection strings
- パスワード
- アクセス キー (Redis Cache、Azure Event Hubs、Azure Cosmos DB)
- SSH キー

IP アドレス、サービス名、その他の構成設定などの他の機密情報は、キー コンテナーではなく [Azure App Configuration](../../azure-app-configuration/overview.md) に格納する必要があります。

各キー コンテナーによってシークレットのセキュリティ境界が定義されます。 アプリケーションごと、リージョンごと、環境ごとの 1 つのキー コンテナーに対して、アプリケーションのシークレットを細かく分離することをお勧めします。

キー コンテナーのベスト プラクティスの詳細については、「[Key Vault を使用するためのベスト プラクティス](../general/best-practices.md)」を参照してください。

## <a name="configuration-and-storing"></a>構成と格納 

ローテーション中に生成される資格情報の動的な部分を値として格納します。 例としては、クライアント アプリケーション シークレット、パスワード、アクセス キーなどが該当します。 関連する静的属性および識別子 (ユーザー名、アプリケーション ID、サービス URL など) は、シークレットのタグとして格納し、ローテーション中に新しいバージョンのシークレットにコピーする必要があります。

シークレットの詳細については、「[Azure Key Vault のシークレットについて](about-secrets.md)」を参照してください。

## <a name="secrets-rotation"></a>シークレットのローテーション
アプリケーションのライフサイクル全体を通して、シークレットは環境変数または構成設定としてアプリケーションのメモリに頻繁に格納され、結果として望ましくない露出の影響を受けやすくなります。 シークレットは漏えいや露出の影響を受けやすいため、ローテーションを頻繁に行う (少なくとも 60 日ごと) ことが重要です。

シークレットのローテーション プロセスの詳細については、「[2 セットの認証資格情報があるリソースを対象にシークレットのローテーションを自動化する](tutorial-rotation-dual.md)」を参照してください。 

## <a name="access-and-network-isolation"></a>アクセスとネットワークの分離

コンテナーに対するアクセス権を持つ IP アドレスを指定することで、コンテナーの公開を縮小できます。 アプリケーションと関連サービスのみがコンテナー内のシークレットにアクセスできるようにファイアウォールを構成して、攻撃者がシークレットにアクセスしにくくします。

ネットワーク セキュリティの詳細については、「[Azure Key Vault のネットワーク設定を構成する](../general/how-to-azure-key-vault-network-security.md)」を参照してください。

また、アプリケーションにシークレットの読み取りアクセス許可のみを与えることにより、最低特権アクセスに従う必要があります。 シークレットへのアクセスは、アクセス ポリシーか、Azure ロールベースのアクセス制御で制御できます。 

Azure Key Vault でのアクセス制御の詳細については、次を参照してください。
- [Azure ロールベースのアクセス制御を使用して Key Vault のキー、証明書、シークレットへのアクセス権を付与する](../general/rbac-guide.md)
- [Key Vault アクセス ポリシーを割り当てる](../general/assign-access-policy.md)
 
## <a name="service-limits-and-caching"></a>サービスの制限とキャッシュ
Key Vault は、最初は [Azure Key Vault サービスの制限](../general/service-limits.md)で指定されたスロットリング制限を使用して作成されました。 スループット率を最大化するために推奨される 2 つのベスト プラクティスは、以下のとおりです。
- 少なくとも 8 時間は、アプリケーションにシークレットをキャッシュする。
- サービスの制限を超えた場合のシナリオを処理するため、エクスポネンシャル バックオフ再試行ロジックを実装する。

スロットリングのガイダンスについて詳しくは、「[Azure Key Vault のスロットル ガイダンス](../general/overview-throttling.md)」を参照してください。

## <a name="monitoring"></a>監視
シークレットへのアクセスとそのライフサイクルを監視するには、Key Vault のログ記録を有効します。 すべてのコンテナー内のすべてのシークレット アクティビティを 1 か所で監視するには、[Azure Monitor](../../azure-monitor/overview.md) を使用します。 または、Azure Logic Apps や Azure Functions との統合が容易な [Azure Event Grid](../../event-grid/overview.md) を使用して、シークレットのライフサイクルを監視します。

詳細については、次を参照してください。
- [Event Grid ソースとして Azure Key Vault](../../event-grid/event-schema-key-vault.md?tabs=event-grid-event-schema.md)
- [Azure Key Vault のログ記録](../general/logging.md)
- [Azure Key Vault の監視とアラート](../general/alert.md)

## <a name="backup-and-purge-protection"></a>バックアップと消去保護
シークレットの強制削除から保護するには、[消去保護](../general/soft-delete-overview.md#purge-protection)を有効にします。 コンテナー内でシークレットを更新、削除、または作成する際には、コンテナーの定期的なバックアップを作成しておきます。

- Azure PowerShell のバックアップ コマンドについては、[シークレットのバックアップ](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)に関するページを参照してください。
- Azure CLI のバックアップ コマンドについては、[シークレットのバックアップ](/cli/azure/keyvault/secret#az_keyvault_secret_backup)に関するページを参照してください。

## <a name="learn-more"></a>詳細情報
- [Azure Key Vault のシークレットについて](about-secrets.md)
- [Key Vault を使用するためのベスト プラクティス](../general/best-practices.md)
