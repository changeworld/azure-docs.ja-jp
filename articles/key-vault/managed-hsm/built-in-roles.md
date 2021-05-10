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
ms.openlocfilehash: 01e96922d9c0c47eaf4d430e92eafcd9d0964e13
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557226"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>Managed HSM のローカル RBAC の組み込みロール

Managed HSM のローカル RBAC には、いくつかの組み込みロールがあります。 これらのロールを、ユーザー、サービス プリンシパル、グループ、マネージド ID に割り当てることができます。 プリンシパルが操作を実行できるようにするには、その操作を実行するためのアクセス許可が付与されるロールを割り当てる必要があります。 これらのロールと操作で管理できるのは、データ プレーン操作に対するアクセス許可のみです。 Managed HSM リソースのコントロール プレーンのアクセス許可を管理するには、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を使用する必要があります。 コントロール プレーンの操作の例としては、新しいマネージド HSM を作成したり、既存のものを更新、移動、削除したりすることが考えられます。

## <a name="built-in-roles"></a>組み込みのロール

|ロール名|説明|id|
|---|---|---|
|Managed HSM 管理者| セキュリティ ドメイン、完全バックアップと復元、ロール管理に関連するあらゆる操作を実行するためのアクセス許可を付与します。 キー管理操作の実行は許可されません。|a290e904-7015-4bba-90c8-60543313cdb4|
|Managed HSM 暗号化担当者|すべてのロール管理の実行、削除されたキーの削除または回復、およびキーのエクスポートを行うアクセス許可を付与します。 他のキー管理操作を実行することは許可されません。|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|Managed HSM 暗号化ユーザー|キーの消去、削除されたキーの復元、キーのエクスポートを除くあらゆるキー管理操作を実行するためのアクセス許可を付与します。|21dbd100-6940-42c2-9190-5d6cb909625b|
|Managed HSM ポリシー管理者| ロールの割り当てを作成、削除するためのアクセス許可を付与します。|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|Managed HSM 暗号化監査担当者|キーの属性を読み取るための読み取りアクセス許可を付与します (キーの属性を使用することはできません)。|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|Managed HSM 暗号化サービスの暗号化| サービスを暗号化するためにキーを使用できるアクセス許可を付与します。 |33413926-3206-4cdd-b39a-83574fe37a17|
|Managed HSM バックアップ| 単一のキーまたは HSM 全体でのバックアップを実行するためのアクセス許可を付与します。|7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

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
|/keys/read/action|||<center>X</center>||<center>X</center>||<center>X</center>|
|/keys/write/action|||<center>X</center>||||
|/keys/create|||<center>X</center>||||
|/keys/delete|||<center>X</center>||||
|/keys/deletedKeys/read/action||<center>X</center>|||||
|/keys/deletedKeys/recover/action||<center>X</center>|||||
|/keys/deletedKeys/delete||<center>X</center>|||||<center>X</center>|
|/keys/backup/action|||<center>X</center>|||<center>X</center>|
|/keys/restore/action|||<center>X</center>||||
|/keys/export/action||<center>X</center>|||||
|/keys/release/action|||<center>X</center>||||
|/keys/import/action|||<center>X</center>||||
|**キーの暗号化操作**|
|/keys/encrypt/action|||<center>X</center>||||
|/keys/decrypt/action|||<center>X</center>||||
|/keys/wrap/action|||<center>X</center>||<center>X</center>||
|/keys/unwrap/action|||<center>X</center>||<center>X</center>||
|/keys/sign/action|||<center>X</center>||||
|/keys/verify/action|||<center>X</center>||||
|**ロール管理**|
|/roleAssignments/read/action|<center>X</center>|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>
|/roleAssignments/write/action|<center>X</center>|<center>X</center>||<center>X</center>|||
|/roleAssignments/delete/action|<center>X</center>|<center>X</center>||<center>X</center>|||
|/roleDefinitions/read/action|<center>X</center>|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>
|/roleDefinitions/write/action|<center>X</center>|<center>X</center>||<center>X</center>|||
|/roleDefinitions/delete/action|<center>X</center>|<center>X</center>||<center>X</center>|||
|**バックアップまたは復元の管理**|
|/backup/start/action|<center>X</center>|||||<center>X</center>|
|/backup/status/action|<center>X</center>|||||<center>X</center>|
|/restore/start/action|<center>X</center>||||||
|/restore/status/action|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>次のステップ

- [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) の概要を確認します。
- [Managed HSM のロール管理](role-management.md)に関するチュートリアルを確認します。
