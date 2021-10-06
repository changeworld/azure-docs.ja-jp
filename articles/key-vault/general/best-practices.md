---
title: Key Vault を使用するためのベスト プラクティス - Azure Key Vault | Microsoft Docs
description: アクセスの制御、別のキー コンテナーを使用する状況、バックアップ、ログ、回復オプションなど、Azure Key Vault のベスト プラクティスについて説明します。
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 0461228678762adbc4db936c35849f16a482c1a9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605508"
---
# <a name="best-practices-to-use-key-vault"></a>Key Vault を使用するためのベスト プラクティス

## <a name="use-separate-key-vaults"></a>別々のキー コンテナーを使用する

環境 (開発、実稼働前、および実稼働) ごとにアプリケーションごとのコンテナーを使用することをお勧めします。 これにより複数の環境でシークレットを共有することがなくなり、セキュリティ違反が発生した際の脅威を軽減するのにも役立ちます。

### <a name="why-we-recommend-separate-key-vaults"></a>別々のキー コンテナーをお勧めする理由

Azure Key Vault では、アクセス ポリシーは、"オール オア ナッシング" の概念です。 ID に特定のアクセス許可 (たとえば **Get**) がある場合、その ID はコンテナー内の "*任意の*" シークレット、キー、または証明書を取得できます。 これは、機密データを同じコンテナーにグループ化すると、攻撃によってさまざまな事項にわたる機密情報にアクセスできるおそれがあるため、セキュリティ イベントの "*爆発半径*" が大きくなることを意味します。 これを軽減するには、特定のアプリケーションがアクセス権限を持つ "*必要がある*" 機密情報を検討し、この説明に基づいてキー コンテナーを分離します。 アプリ別にキー コンテナーを分離するのが最も一般的な境界です。

## <a name="control-access-to-your-vault"></a>コンテナーへのアクセスの制御

Azure Key Vault は、暗号化キーとシークレット (証明書、接続文字列、パスワードなど) を保護するクラウド サービスです。 このデータは機密性が高く、ビジネス上重要であるため、承認されたアプリケーションとユーザーだけを許可することで、ご利用のキー コンテナーへのアクセスをセキュリティで保護する必要があります。 この[記事](security-features.md)では、Key Vault アクセス モデルの概要について説明します。 認証と承認について、およびご利用のキー コンテナーへのアクセスをセキュリティで保護する方法について説明します。

コンテナーへのアクセスを制御している間の推奨事項としては次のようなものがあります。
1. サブスクリプション、リソース グループ、および Key Vaults (Azure RBAC) へのアクセスをロックダウンする
2. すべてのコンテナーのアクセス ポリシーを作成する
3. 最小特権アクセス プリンシパルを使用してアクセス権を付与する
4. ファイアウォールと [VNET サービス エンドポイント](overview-vnet-service-endpoints.md)を有効にする

## <a name="backup"></a>バックアップ

コンテナー内のオブジェクトの更新、削除、作成の際には、必ずコンテナーの定期的バックアップを取るようにしてください。

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell バックアップ コマンド

* [証明書のバックアップ](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [キーのバックアップ](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [シークレットのバックアップ](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Azure CLI バックアップ コマンド

* [証明書のバックアップ](/cli/azure/keyvault/certificate#az_keyvault_certificate_backup)
* [キーのバックアップ](/cli/azure/keyvault/key#az_keyvault_key_backup)
* [シークレットのバックアップ](/cli/azure/keyvault/secret#az_keyvault_secret_backup)


## <a name="turn-on-logging"></a>ログ記録の有効化

コンテナーの[ログ記録有効にします](logging.md)。 また、アラートを設定します。

## <a name="turn-on-recovery-options"></a>回復オプションの有効化

1. [論理的な削除](soft-delete-overview.md)を有効にします。
2. 論理的な削除が有効になってもシークレットまたはコンテナーの強制削除を防ぐには、パージ保護を有効にします。

## <a name="learn-more"></a>詳細情報
- [Key Vault でのシークレットの管理に関するベスト プラクティス](../secrets/secrets-best-practices.md)