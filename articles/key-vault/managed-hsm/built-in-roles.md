---
title: Managed HSM のローカル RBAC の組み込みロール - Azure Key Vault | Microsoft Docs
description: ユーザー、サービス プリンシパル、グループ、マネージド ID に割り当てることのできる Managed HSM 組み込みロールの概要
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 6b39c1d6fe2e84055e7c94dde076514d04a24f36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90992442"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>Managed HSM のローカル RBAC の組み込みロール

Managed HSM のローカル RBAC には、ユーザー、サービス プリンシパル、グループ、マネージド ID に割り当てることのできる組み込みロールがいくつかあります。 プリンシパルが操作を実行できるようにするには、その操作を実行するためのアクセス許可が付与されるロールを割り当てる必要があります。 これらのロールと操作で管理できるのは、データ プレーン操作に対するアクセス許可のみです。 Managed HSM リソースの制御プレーンのアクセス許可を管理する (新しいマネージド HSM を作成したり、既存のものを更新、移動、削除したりする) には、[Azure のロールベースのアクセス制御 (RBAC)](../../role-based-access-control/overview.md) を使用する必要があります。

## <a name="built-in-roles"></a>組み込みのロール

|ロール名|説明|id|
|---|---|---|
|Managed HSM 管理者| すべてのデータ アクションへのフル アクセスを許可します。|a290e904-7015-4bba-90c8-60543313cdb4|
|Managed HSM 暗号化担当者| すべてのキー管理およびキー暗号化操作へのフル アクセスを許可します。|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|Managed HSM 暗号化ユーザー|暗号化操作を行えるように、キーを作成、使用するためのアクセスを許可します。 キーを完全に削除することはできません。|21dbd100-6940-42c2-9190-5d6cb909625b|
|Managed HSM ポリシー管理者| ロールの割り当てを作成、削除するためのアクセス許可を付与します。|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|Managed HSM 暗号化監査担当者|キーを読み取るための読み取りアクセス許可を付与します (キーを使用することはできません)。|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|Managed HSM 暗号化サービスの暗号化| サービスを暗号化するためにキーを使用できるアクセス許可を付与します。 |33413926-3206-4cdd-b39a-83574fe37a17|
|Managed HSM バックアップ| 単一のキーまたは HSM 全体でのバックアップを実行するためのアクセス許可を付与します。 |7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>許容される演算
> [!NOTE]  
> - "X" は、ロールでデータ アクションの実行が許可されていることを示します。 空のセルは、そのロールにそのデータ アクションを実行するためのアクセス許可がないことを示します。
> - すべてのデータ アクション名に "Microsoft.KeyVault/managedHsm" というプレフィックスが付きますが、この表では簡潔にするために省略されています。
> - すべてのロール名に "Managed HSM" というプレフィックスが付きますが、この表では簡潔にするために省略されています。

|データ アクション | 管理者 | Crypto Officer | 暗号化ユーザー | ポリシー管理者 | 暗号化サービスの暗号化 | バックアップ | 暗号化監査担当者|
|---|---|---|---|---|---|---|---|
|**セキュリティ ドメインの管理**|
/securitydomain/download/action|<center>X</center>||||||
/securitydomain/upload/action|<center>X</center>||||||
/securitydomain/upload/read|<center>X</center>||||||
/securitydomain/transferkey/read|<center>X</center>||||||
|**キー管理**|
|/keys/read/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||<center>X</center>|
|/keys/write/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/create|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/delete|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/read/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/recover/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/delete|<center>X</center>|<center>X</center>|||||<center>X</center>|
|/keys/backup/action|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>|
|/keys/restore/action|<center>X</center>|<center>X</center>|||||
|/keys/export/action|<center>X</center>|<center>X</center>|||||
|/keys/import/action|<center>X</center>|<center>X</center>|||||
|**キーの暗号化操作**|
|/keys/encrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/decrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/wrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/unwrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/sign/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/verify/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|**ロール管理**|
|/roleAssignments/delete/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/read/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/write/action|<center>X</center>|||<center>X</center>|||
|/roleDefinitions/read/action|<center>X</center>|||<center>X</center>|||
|**バックアップまたは復元の管理**|
|/backup/start/action|<center>X</center>|||||<center>X</center>|
|/backup/status/action|<center>X</center>|||||<center>X</center>|
|/restore/start/action|<center>X</center>||||||
|/restore/status/action|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>次のステップ

- [Azure ロールベースのアクセス制御 (RBAC) の概要](../../role-based-access-control/overview.md)を確認します。
- [Managed HSM のロール管理](role-management.md)に関するチュートリアルを確認します。
