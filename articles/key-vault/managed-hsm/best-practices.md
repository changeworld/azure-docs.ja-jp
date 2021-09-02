---
title: Azure Key Vault Managed HSM を使用しているときのベスト プラクティス
description: このドキュメントでは、Key Vault を使用するためのいくつかのベスト プラクティスについて説明します。
services: key-vault
author: mbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: mbaldwin
ms.openlocfilehash: 99a313f64e9f5bafd13dbbf45a6881e1c6e2f275
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459698"
---
# <a name="best-practices-when-using-managed-hsm"></a>Managed HSM を使用しているときのベスト プラクティス

## <a name="control-access-to-your-managed-hsm"></a>Managed HSM へのアクセスを制御する

Managed HSM は、暗号化キーを保護するクラウド サービスです。 これらのキーは機密性が高く、ビジネスに不可欠であるため、承認されたアプリケーションとユーザーのみを許可することによって、Managed HSM へのアクセスを確実にセキュリティで保護するようにしてください。 この[記事](access-control.md)では、アクセス モデルの概要について説明します。 認証と承認、ロールベースのアクセス制御について説明しています。
- 個人に管理者ロールを割り当てるのではなく、HSM 管理者の [Azure Active Directory セキュリティ グループ](../../active-directory/fundamentals/active-directory-manage-groups.md)を作成します。 これにより、個々のアカウントが削除された場合の "管理のロックアウト" が防止されます。
- 管理グループ、サブスクリプション、リソース グループ、Managed HSM へのアクセスをロックダウンします。Azure RBAC を使用して、管理グループ、サブスクリプション、リソース グループへのアクセスを制御します。
- [マネージド HSM のローカル RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac) を使用して、キーごとのロールの割り当てを作成します。
- 職務の分離を維持するため、複数のロールを同じプリンシパルに割り当てないようにします。 
- 最小特権アクセス プリンシパルを使用してロールを割り当てます。
- 明確なアクセス許可セットを使用してカスタム ロール定義を作成します。

## <a name="choose-regions-that-support-availability-zones"></a>可用性ゾーンをサポートするリージョンを選択する

- 最大の高可用性とゾーン回復性を確保するには、[可用性ゾーン](../../availability-zones/az-overview.md)がサポートされている Azure リージョンを選択します。 これらのリージョンは、Azure portal で "推奨されるリージョン" として表示されます。

## <a name="backup"></a>バックアップ

- HSM の定期的なバックアップを必ず作成してください。 バックアップは、HSM レベルで特定のキーに対して実行できます。 

## <a name="turn-on-logging"></a>ログ記録を有効にする

- HSM の[ログ記録を有効にします](logging.md)。 また、アラートを設定します。

## <a name="turn-on-recovery-options"></a>回復オプションの有効化

- [論理的な削除](soft-delete-overview.md)は、既定で有効になります。 7 日間から 90 日間までの保有期間を選択できます。
- HSM またはキーが直ちに完全に削除されないようにするには、消去保護をオンにします。 消去保護がオンにされている場合、HSM またはキーは、保有日数が経過するまで、キーは削除された状態のままになります。

## <a name="generate-and-import-keys-from-on-premise-hsm"></a>キーを生成してオンプレミスの HSM からインポートする

> [!NOTE]
> 作成されたキーまたは Managed HSM にインポートされたキーはエクスポートできません。

- 長期的な移植性とキーの持続性を確保するには、オンプレミスの HSM でキーを生成し、[それを Managed HSM にインポート](hsm-protected-keys-byok.md)します。 キーのコピーが、将来の使用のためにオンプレミスの HSM に安全に格納されます。

## <a name="next-steps"></a>次のステップ

- HSM の完全バックアップ/復元については、[完全バックアップ/復元](backup-restore.md)に関するページを参照してください。
- Azure Monitor を使用してログ記録を構成する方法については、「[Managed HSM のログ記録](logging.md)」を参照してください。
- キー管理については、[Managed HSM キーの管理](key-management.md)に関するページを参照してください。
- ロールの割り当ての管理については、「[Managed HSM のロール管理](role-management.md)」を参照してください。
- 回復オプションについては、「[マネージド HSM の論理的な削除](soft-delete-overview.md)」を参照してください。
