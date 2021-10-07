---
title: シークレット管理のベスト プラクティス - Azure Key Vault | Microsoft Docs
description: Azure Key Vault でのシークレット管理のベスト プラクティスについて説明します
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: mbaldwin
ms.openlocfilehash: ae4d2bd17dc1a233be71f48ebd106f1db2a31de6
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094464"
---
# <a name="best-practices-for-secrets-management-in-key-vault"></a>Key Vault でのシークレットの管理に関するベスト プラクティス

Azure Key Vault を使用すると、サービスやアプリケーションのパスワードやアクセス キーなどの資格情報をシークレットとして安全に保存できます。 キー コンテナー内のすべてのシークレットは、ソフトウェア キーで暗号化された状態で格納されます。 Key Vault を使用すると、アプリケーション開発者は、アプリケーションにセキュリティ情報を格納する必要がなくなります。 アプリケーションにセキュリティ情報を格納する必要がなくなると、コードのこの情報部分を作成する必要がなくなります。 

Key Vault に格納する必要があるシークレットの例:

- クライアント シークレットのシークレット
- Connection strings
- パスワード
- アクセス キー (Redis Cache、イベント ハブ、Cosmos DB)
- SSH キー

IP アドレス、サービス名、その他の構成設定などの他の機密情報は、[Azure App Configuration](../../azure-app-configuration/overview.md) に格納する必要があります。

各キー コンテナーによってシークレットのセキュリティ境界が定義されます。 アプリケーションごと、リージョンごと、環境ごとに 1 つのキー コンテナーを使用して、アプリケーションのシークレットを細かく分離することをお勧めします。 

キー コンテナーのベスト プラクティスの詳細については、以下を参照してください。
- [Key Vault を使用するためのベスト プラクティス](../general/best-practices.md)

## <a name="configuration-and-storing"></a>構成と格納 

資格情報の動的な部分、つまりローテーション中に生成されるもの (クライアント アプリケーションのシークレット、パスワード、アクセス キーなど) は、シークレットに値として格納することをお勧めします。 関連する静的属性および識別子 (ユーザー名、AppId、サービス URL など) は、シークレットのタグとして格納し、ローテーション中に新しいバージョンのシークレットにコピーする必要があります。

シークレットの詳細については、次を参照してください。
- [Azure Key Vault のシークレットについて](about-secrets.md)

## <a name="secrets-rotation"></a>シークレットのローテーション
アプリケーションのライフサイクル全体を通して、シークレットは環境変数または構成設定としてアプリケーションのメモリに何度も格納され、結果として望ましくない露出の影響を受けやすくなります。 シークレットは漏えいや露出の影響を受けやすいため、シークレットのローテーションを頻繁に行う (少なくとも 60 日ごと) ことが重要です。 

シークレットのローテーション プロセスの詳細については、以下を参照してください。
- [2 セットの認証資格情報があるリソースを対象にシークレットのローテーションを自動化する](tutorial-rotation-dual.md) 

## <a name="access-and-network-isolation"></a>アクセスとネットワークの分離

コンテナーに対するアクセス権を持つ IP アドレスを指定することで、コンテナーの公開を縮小できます。 アプリケーションと関連サービスのみがコンテナー内のシークレットにアクセスできるようにファイアウォールを構成して、攻撃者がシークレットにアクセスしにくくします。 

ネットワーク セキュリティの詳細については、以下を参照してください。
- [Azure Key Vault のネットワーク設定を構成する](../general/how-to-azure-key-vault-network-security.md)

また、アプリケーションにシークレットの読み取りアクセス許可のみを与えることにより、最低特権アクセスに従う必要があります。 シークレットのアクセスは、アクセス ポリシーか、Azure のロールベースのアクセス制御で制御できます。 

Azure Key Vault でのアクセス制御の詳細については、次を参照してください。
- [Azure のロールベースのアクセス制御を使用して Key Vault のキー、証明書、シークレットへのアクセス権を付与する](../general/rbac-guide.md)
- [Key Vault アクセス ポリシーを割り当てる](../general/assign-access-policy.md)
 
## <a name="service-limits-and-caching"></a>サービスの制限とキャッシュ
Key Vault は、最初は [Azure Key Vault サービスの制限](../general/service-limits.md)で指定された制限を使用して作成されました。 Key Vault のスループット率を最大化するための、いくつかの推奨ガイドラインおよびベスト プラクティスを次に示します。
- 少なくとも 8 時間はアプリケーションにシークレットをキャッシュする
- サービスの制限を超えた場合のシナリオを処理するため、エクスポネンシャル バックオフ再試行ロジックを実装する

スロットルのガイダンスの詳細については、次を参照してください。
- [Azure Key Vault のスロットル ガイダンス](../general/overview-throttling.md)

## <a name="monitoring"></a>監視
お使いのコンテナーでログ記録を有効にして、シークレットへのアクセスとそのライフサイクルを監視します。 [Azure Monitor](https://docs.microsoft.com//azure/azure-monitor/overview) を使用してお使いのすべてのコンテナー内にあるすべてのシークレット アクティビティを一元的に監視することも、[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) により Logic Apps や Azure Functions と簡単に統合してシークレットのライフサイクルを監視することもできます。

詳細については、次を参照してください。
- [Event Grid ソースとして Azure Key Vault](https://docs.microsoft.com/azure/event-grid/event-schema-key-vault?tabs=event-grid-event-schema)
- [Azure Key Vault のログ記録](../general/logging.md)
- [Azure Key Vault の監視とアラート](../general/alert.md)

## <a name="backup-and-purge-protection"></a>バックアップと消去保護
シークレットの強制削除から保護するには、[消去保護](../general/soft-delete-overview.md#purge-protection)を有効にします。 お使いのコンテナーに入っているシークレットの更新、削除、作成を行う際は、コンテナーの定期的バックアップを取ります。

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell バックアップ コマンド

* [シークレットのバックアップ](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Azure CLI バックアップ コマンド

* [シークレットのバックアップ](/cli/azure/keyvault/secret#az_keyvault_secret_backup)

## <a name="learn-more"></a>詳細情報
- [Azure Key Vault のシークレットについて](about-secrets.md)
- [Key Vault を使用するためのベスト プラクティス](../general/best-practices.md)
