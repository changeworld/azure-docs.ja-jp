---
title: 'チュートリアル: 管理者の追加の概要'
description: このチュートリアルでは、ワークスペースに別の管理ユーザーを追加する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.openlocfilehash: 8b854dfcff7dfb4d03038b542308b6f3ebb6d491
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553500"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Synapse ワークスペースに管理者を追加する

このチュートリアルでは、Synapse ワークスペースに管理者を追加する方法について説明します。 このユーザーは、ワークスペースを完全に制御できます。

## <a name="overview"></a>概要

これまで、このファースト ステップ ガイドでは、ワークスペースで "*あなた*" が実行するアクティビティに重点を置いてきました。 手順 1. でワークスペースを作成したあなたが Synapse ワークスペースの管理者となります。 ここでは、別のユーザー Ryan (`ryan@contoso.com`) を管理者にします。 完了すると、Ryan はあなたがワークスペースで実行できるすべての操作を実行できるようになります。

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC: ワークスペースの所有者ロール

`ryan@contoso.com` を、ワークスペースの Azure RBAC **所有者** ロールに割り当てます。

1. Azure portal を開き、Synapse ワークスペースを開きます。
1. 左側で、 **[アクセス制御 (IAM)]** を選択します。
1. `ryan@contoso.com` を **所有者** ロールに追加します。 
1. **[保存]** をクリックします。
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Synapse RBAC: ワークスペースの Synapse 管理者ロール

`ryan@contoso.com` を、ワークスペースの Synapse RBAC **Synapse 管理者** ロールに割り当てます。

1. Synapse Studio でワークスペースを開きます。
1. 左側で、 **[管理]** をクリックして管理ハブを開きます。
1. **[セキュリティ]** で、 **[アクセス制御]** をクリックします。
1. **[追加]** をクリックします。
1. **[スコープ]** は **[ワークスペース]** に設定されたままにします。
1. `ryan@contoso.com` を、**Synapse 管理者** ロールに割り当てます。 
1. 次に、 **[適用]** をクリックします。
 
## <a name="azure-rbac-role-assignments-on-the-primary-storage-account"></a>Azure RBAC: プライマリ ストレージ アカウントでのロールの割り当て

`ryan@contoso.com` を、ワークスペースのプライマリ ストレージ アカウントで **所有者** ロールに割り当てます。
`ryan@contoso.com` を、ワークスペースのプライマリ ストレージ アカウントで **Azure Storage Blob データ共同作成者** ロールに割り当てます。

1. Azure portal で、ワークスペースのプライマリ ストレージ アカウントを開きます。
1. 左側で、 **[アクセス制御 (IAM)]** をクリックします。
1. `ryan@contoso.com` を **所有者** ロールに追加します。 
1. `ryan@contoso.com` を、**Azure Storage Blob データ共同作成者** ロールに割り当てます

## <a name="dedicated-sql-pools-db_owner-role"></a>専用 SQL プール: db_owner ロール

`ryan@contoso.com` を、ワークスペース内の専用 SQL プールごとに **db_owner** に割り当てます。

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>次のステップ

* [Azure Synapse Analytics の使用を開始する](get-started.md)
* [ワークスペースを作成する](quickstart-create-workspace.md)
* [サーバーレス SQL プールを使用する](quickstart-sql-on-demand.md)
