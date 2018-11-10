---
title: Azure のロールベースのアクセス制御を使用したバックアップの管理
description: ロールベースのアクセス制御を使用して、Recovery Services コンテナーのバックアップ管理操作へのアクセスを管理します。
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/11/2018
ms.author: trinadhk
ms.openlocfilehash: f293f642db2bd526e761ff570ce97a33845808b7
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412807"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>ロール ベースのアクセス制御を使用した Azure Backup の回復ポイントの管理
Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。 RBAC を使用して、チーム内で職務を分離し、職務に必要なアクセス許可のみをユーザーに付与します。

> [!IMPORTANT]
> Azure Backup で提供されるロールが実行できるの、Azure Portal または Recovery Services コンテナーの PowerShell コマンドレットで実行できるアクションに制限されています。 これらのロールが、Azure Backup エージェント クライアント UI または System Center Data Protection Manager UI または Azure Backup Server UI で実行されるアクションを使用することはできません。

Azure Backup では、バックアップの管理操作を制御する 3 つの組み込みロールが提供されます。 [Azure RBAC の組み込みのロール](../role-based-access-control/built-in-roles.md)について説明します。

* [バックアップ共同作成者](../role-based-access-control/built-in-roles.md#backup-contributor) - このロールは、Recovery Services コンテナーの作成と他のロールへの権限付与を除き、バックアップの作成と管理のすべての権限を持ちます。 このロールは、すべてのバックアップ管理操作を実行できる、バックアップ管理の管理者と考えてください。
* [バックアップ オペレーター](../role-based-access-control/built-in-roles.md#backup-operator) - このロールは、バックアップの削除とバックアップ ポリシーの管理を除き、共同作成者が行うすべての操作の権限を持ちます。 このロールは共同作成者と同等ですが、データの削除によるバックアップの停止やオンプレミス リソースの登録解除など、削減する操作は実行できません。
* [バックアップ リーダー](../role-based-access-control/built-in-roles.md#backup-reader) - このロールは、すべてのバックアップ管理操作を見る権限を持ちます。 このロールは監視役と考えてください。

制御を強化するために独自のロールを定義する場合は、Azure RBAC で [カスタム ロールを作成](../role-based-access-control/custom-roles.md)する方法を参照してください。



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>バックアップ管理アクションへの組み込みバックアップ ロールのマッピング
次の表に、バックアップ管理アクションと、その操作を実行するために必要な最小限の RBAC ロールを示します。

| 管理操作 | 必要最小限 RBAC ロール | 必要なスコープ |
| --- | --- | --- |
| Recovery Services コンテナーの作成 | Contributor | コンテナーを含むリソース グループ |
| Azure VM のバックアップの有効化 | Backup Operator | コンテナーを含むリソース グループ |
| | Virtual Machine Contributor | VM リソース |
| VM のオンデマンド バックアップ | Backup Operator | 復旧コンテナー リソース |
| VM の復元 | Backup Operator | VM がデプロイされるリソース グループ |
| | Virtual Machine Contributor | VM がデプロイされるリソース グループ |
| アンマネージド ディスク VM バックアップの復元 | Backup Operator | 復旧コンテナー リソース |
| | Virtual Machine Contributor | VM リソース |
| | Storage Account Contributor | ストレージ アカウント リソース |
| VM バックアップからのマネージド ディスクの復元 | Backup Operator | 復旧コンテナー リソース |
| | Virtual Machine Contributor | VM リソース |
| | Storage Account Contributor | ストレージ アカウント リソース |
| | Contributor | マネージド ディスクの復元先となるリソース グループ |
| VM バックアップからの個々のファイルの復元 | Backup Operator | 復旧コンテナー リソース |
| | Virtual Machine Contributor | VM リソース |
| Azure VM バックアップのバックアップ ポリシーの作成 | Backup Contributor | 復旧コンテナー リソース |
| Azure VM バックアップのバックアップ ポリシーの変更 | Backup Contributor | 復旧コンテナー リソース |
| Azure VM バックアップのバックアップ ポリシーの削除 | Backup Contributor | 復旧コンテナー リソース |
| VM バックアップでのバックアップの停止 (データを保持またはデータを削除) | Backup Contributor | 復旧コンテナー リソース |
| オンプレミスの Windows Server/クライアント/SCDPM または Azure Backup Server での登録 | Backup Operator | 復旧コンテナー リソース |
| オンプレミスの Windows Server/クライアント/SCDPM または Azure Backup Server での登録解除 | Backup Contributor | 復旧コンテナー リソース |

## <a name="next-steps"></a>次の手順
* [ロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md): Azure ポータルでの RBAC の基本について説明します。
* 次の要素を使用したアクセス管理方法の詳細
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [ロールベースのアクセス制御のトラブルシューティング](../role-based-access-control/troubleshooting.md): 一般的な問題の修正に関する推奨事項を紹介します。
