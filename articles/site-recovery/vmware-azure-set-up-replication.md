---
title: Azure Site Recovery を使用して VMware レプリケーションのレプリケーション ポリシーを構成および管理する | Microsoft Docs
description: Azure Site Recovery を使用して Azure への VMware レプリケーションのレプリケーション設定を構成する方法について説明します。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: 03197d1f42a17d6fc99b85d3fbc3635468b1e6ae
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423608"
---
# <a name="configure-and-manage-replication-policies-for-vmware-replication"></a>VMware レプリケーションのレプリケーション ポリシーの構成と管理
この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して VMware VM を Azure に レプリケートする場合の、レプリケーション ポリシーの構成方法について説明します。


## <a name="create-a-policy"></a>ポリシーの作成

1. **[管理]** > **[Site Recovery インフラストラクチャ]** を選択します。
1. **[For VMware and Physical machines (VMware と物理マシン用)]** で、**[レプリケーション ポリシー]** を選択します。 
1. **[+ レプリケーション ポリシー]** をクリックし、ポリシー名を指定します。
1. **[RPO しきい値]** で、RPO の制限を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
1. **[復旧ポイントのリテンション期間]** で、各復旧ポイントのリテンション期間の長さ (時間単位) を指定します。 保護されたマシンは、このリテンション期間内のどのポイントにでも復旧できます。 Premium Storage にレプリケートされたマシンでは、最大 24 時間のリテンション期間がサポートされます。 Standard Storage については、最大 72 時間がサポートされます。
1. **[アプリ整合性スナップショットの頻度]** で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (分単位) を指定します。
1. Click **OK**. 30 ～ 60 秒でポリシーが作成されます。

レプリケーション ポリシーを作成すると、対応するフェールバック ポリシーが自動的に作成され、サフィックス "failback" が付けられます。 ポリシーを作成した後は、そのポリシーを選択し、**[設定の編集]** を選択することで、ポリシーを編集できます。

## <a name="associate-a-configuration-server"></a>構成サーバーを関連付ける 

レプリケーション ポリシーをオンプレミスの構成サーバーに関連付けます。

1. **[関連付け]** をクリックし、構成サーバーを選択します。

    ![構成サーバーの関連付け](./media/vmware-azure-set-up-replication/associate1.png)

1. Click **OK**. 1 ～ 2 分で構成サーバーが関連付けられます。

    ![構成サーバーの関連付け](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>レプリケーション ポリシーの関連付け解除と削除
1. レプリケーション ポリシーを選択します。
    a. 構成サーバーからポリシーの関連付けを解除するには、まずレプリケートされたマシンでそのポリシーが使用されていないことを確認します。 その後、**[関連付けの解除]** をクリックします。
    b. ポリシーを削除するには、まずそのポリシーが構成サーバーに関連付けられていないことを確認します。 その後、 **[削除]** をクリックします。 削除には 30 ～ 60 秒かかります。
1. Click **OK**.
