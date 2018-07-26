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
ms.openlocfilehash: 855b75652fca421df12766f7711152d1e3ca2aeb
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39012060"
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

| 管理操作 | 必要最小限 RBAC ロール |
| --- | --- |
| Recovery Services コンテナーの作成 | コンテナーのリソース グループの共同作成者 |
| Azure VM のバックアップの有効化 | コンテナーを含むリソース グループのスコープで定義されたバックアップ オペレーター、VM の仮想マシン共同作成者 |
| VM のオンデマンド バックアップ | バックアップ オペレーター |
| VM の復元 | バックアップ オペレーター、VM がデプロイされるリソース グループの共同作成者、Vnet 上での読み取り、選択したサブネットへの参加 |
| VM バックアップからのディスクや個々のファイルの復元 | バックアップ オペレーター、VM の仮想マシン共同作成者 |
| Azure VM バックアップのバックアップ ポリシーの作成 | バックアップの共同作成者 |
| Azure VM バックアップのバックアップ ポリシーの変更 | バックアップの共同作成者 |
| Azure VM バックアップのバックアップ ポリシーの削除 | バックアップの共同作成者 |
| VM バックアップでのバックアップの停止 (データを保持またはデータを削除) | バックアップの共同作成者 |
| オンプレミスの Windows Server/クライアント/SCDPM または Azure Backup Server での登録 | バックアップ オペレーター |
| オンプレミスの Windows Server/クライアント/SCDPM または Azure Backup Server での登録解除 | バックアップの共同作成者 |

## <a name="next-steps"></a>次の手順
* [ロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md): Azure ポータルでの RBAC の基本について説明します。
* 次の要素を使用したアクセス管理方法の詳細
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [ロールベースのアクセス制御のトラブルシューティング](../role-based-access-control/troubleshooting.md): 一般的な問題の修正に関する推奨事項を紹介します。
