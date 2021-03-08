---
title: 概念 - ID とアクセス
description: Azure VMware Solution の ID とアクセスの概念について説明します
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 68f4ce9136cca1cf9bf0824395e31704d8ed1a17
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364887"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Azure VMware Solution の ID の概念

Azure VMware Solution のプライベート クラウドは、vCenter サーバーと NSX-T Manager を使用してプロビジョニングされます。 vCenter は、仮想マシン (VM) のワークロードを管理するために使用します。 NSX-T Manager は、プライベート クラウドを拡張するために使用します。

アクセスと ID 管理では、vCenter の場合は CloudAdmin グループ特権が、NSX-T Manager の場合は制限付き管理者権限が使用されます。 これにより、プライベート クラウド プラットフォームは、最新の機能とパッチを使用して自動的にアップグレードされることが保証されます。  詳細については、[プライベート クラウドのアップグレードの概念に関する記事][concepts-upgrades]を参照してください。

## <a name="vcenter-access-and-identity"></a>vCenter のアクセスと ID

CloudAdmin グループにより、vCenter での特権が提供されます。 グループは、vCenter でローカルに管理します。 もう 1 つの方法は、vCenter LDAP シングル サインオンと Azure Active Directory の統合です。 プライベート クラウドをデプロイした後、その統合を有効にします。 

この表は、**CloudAdmin** および **CloudGlobalAdmin** の特権を示しています。

|  特権セット           | CloudAdmin | CloudGlobalAdmin | 解説 |
| :---                     |    :---:   |       :---:      |   :--:  |
|  アラーム                  | CloudAdmin ユーザーには、Compute-ResourcePool と VM のアラームに関するすべてのアラーム特権があります。     |          --        |  -- |
|  自動展開             |  --  |        --        |  Microsoft がホスト管理を行います。  |
|  証明書            |  --  |        --       |  Microsoft が証明書の管理を行います。  |
|  コンテンツ ライブラリ         | CloudAdmin ユーザーには、コンテンツ ライブラリ内のファイルを作成および使用する特権があります。    |         SSO で有効になります。         |  Microsoft がコンテンツ ライブラリ内のファイルを ESXi ホストに配布します。  |
|  データセンター              |  --  |        --          |  Microsoft がデータセンターのすべての操作を行います。  |
|  データストア               | Datastore.AllocateSpace、Datastore.Browse、Datastore.Config、Datastore.DeleteFile、Datastore.FileManagement、Datastore.UpdateVirtualMachineMetadata     |    --    |   -- |
|  ESX エージェント マネージャー       |  --  |         --       |  Microsoft がすべての操作を行います。  |
|  Folder                  |  CloudAdmin ユーザーには、すべてのフォルダー特権があります。     |  --  |  --  |
|  グローバル                  |  Global.CancelTask、Global.GlobalTag、Global.Health、Global.LogEvent、Global.ManageCustomFields、Global.ServiceManagers、Global.SetCustomField、Global.SystemTag         |                  |    |
|  Host                    |  Host.Hbr.HbrManagement      |        --          |  Microsoft が他のすべてのホスト操作を行います。  |
|  InventoryService        |  InventoryService.Tagging      |        --          |  --  |
|  ネットワーク                 |  Network.Assign    |                  |  Microsoft が他のすべてのネットワーク操作を行います。  |
|  アクセス許可             |  --  |        --       |  Microsoft がすべてのアクセス許可操作を行います。  |
|  プロファイル駆動型ストレージ  |  --  |        --       |  Microsoft がすべてのプロファイル操作を行います。  |
|  リソース                |  CloudAdmin ユーザーには、すべてのリソース特権があります。        |      --       | --   |
|  スケジュールされたタスク          |  CloudAdmin ユーザーには、すべての ScheduleTask 特権があります。   |   --   | -- |
|  セッション                |  Sessions.GlobalMessage、Sessions.ValidateSession      |   --   |  Microsoft が他のすべてのセッション操作を行います。  |
|  ストレージ ビュー           |  StorageViews.View   |        --          |  Microsoft が他のすべてのストレージ ビュー操作 (サービスの構成) を行います。  |
|  タスク                   |  --  |  --   |  Microsoft が、タスクを管理する拡張機能を管理します。  |
|  vApp                    |  CloudAdmin ユーザーには、すべての vApp 特権があります。  |  --  |  --  |
|  仮想マシン         |  CloudAdmin ユーザーには、すべての VirtualMachine 特権があります。  |  --  |  --  |
|  vService                |  CloudAdmin ユーザーには、すべての vService 特権があります。  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>NSX-T Manager のアクセスと ID

"*管理者*" アカウントを使用して、NSX-T Manager にアクセスします。 これには完全な特権があるため、 Tier-1 (T1) ゲートウェイ、セグメント (論理スイッチ)、およびすべてのサービスを作成および管理できます。 この特権により、NSX-T Tier-0 (T0) ゲートウェイへのアクセスが可能になります。 T0 ゲートウェイを変更すると、ネットワーク パフォーマンスが低下したり、プライベート クラウドにアクセスできなくなったりする可能性があります。 Azure portal でサポート リクエストを開いて、NSX-T T0 ゲートウェイに対する変更を依頼してください。
  
## <a name="next-steps"></a>次のステップ

Azure VMware Solution のアクセスと ID に関する概念を理解したら、次の事項の学習に進むことができます。

- [プライベート クラウドのアップグレードの概念](concepts-upgrades.md)
- [Azure VMware Solution の vSphere ロールベースのアクセス制御](concepts-role-based-access-control.md)
- [Azure VMware Solution リソースを有効にする方法](enable-azure-vmware-solution.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md