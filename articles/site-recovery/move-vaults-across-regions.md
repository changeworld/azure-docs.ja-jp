---
title: Azure Site Recovery 構成を別の Azure リージョンに移動する | Microsoft Docs
description: Site Recovery 構成を別の Azure リージョンに移動するためのガイダンス
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: ba0e2577d6fb8bac66322917936fe06825af0d96
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708465"
---
# <a name="moving-recovery-services-vault-and--azure-site-recovery-configuration-to-another-azure-region"></a>Recovery Services コンテナーと Azure Site Recovery 構成を別の Azure リージョンに移動する

既存の Azure リソースを別のリージョンに移動したいシナリオは、さまざまなです。管理容易性のため、ガバナンス上の理由、会社の合併や買収による場合などです。Azure VM を移動するときに同様に移動したい関連リソースの 1 つは、ディザスター リカバリー構成です。 既存のディザスター リカバリー構成をあるリージョンから別のリージョンに移動するための、簡単な方法はありません。 これは基本的に、ターゲット リージョンはソース VM リージョンに基づいて構成されており、それを変更することになると、ターゲット リージョンの以前の既存の構成は再利用できず、リセットする必要があるためです。 この記事では、ディザスター リカバリーの設定を再構成して別のリージョンに移動するための段階的な手順を定義します。

このドキュメントの内容は、次のとおりです。

> [!div class="checklist"]
> * 移動の前提条件を確認する
> * Azure Site Recovery によって使用されたリソースを特定する 
> * レプリケーションを無効にする
> * リソースの削除 
> * VM の新しいソース リージョンに基づいて、サイトの回復を再設定します。

> [!IMPORTANT]
> 現在、Recovery Services コンテナーと DR 構成を別のリージョンにそのまま移動するための簡単な方法はありません。このドキュメントでは、レプリケーションを無効にして新しいリージョンで再設定するプロセスを説明します。

## <a name="prerequisites"></a>前提条件

- Azure VM を別のリージョンに移動する前に、必ずディザスター リカバリー構成を削除してください。 

> [!NOTE]
> Azure VM の新しいターゲット リージョンがディザスター リカバリーのターゲット リージョンと同じである場合は、既存のレプリケーション構成を使用して、[ここ](azure-to-azure-tutorial-migrate.md)で説明されている手順に基づいて移動することができます 

- VM の移行が完了するまでは VM が災害から保護されないため、十分な情報に基づいて意思決定を行い、関係者に通知してください。 

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Azure Site Recovery によって使用されたリソースを特定する
VM がまだレプリケートされている間の方が、関連するリソースを識別しやすいため、次の手順に進む前にこの手順を実行することをお勧めします

レプリケートされている各 Azure VM について、 **[保護されたアイテム]**  >  **[レプリケートされたアイテム]** > **[プロパティ]** の順に移動し、以下のリソースを特定します

- ターゲット リソース グループ
- キャッシュ ストレージ アカウント
- ターゲット ストレージ アカウント (アンマネージドのディスクベースの Azure VM の場合) 
- ターゲット ネットワーク


## <a name="disable-existing-disaster-recovery-configuration"></a>既存のディザスター リカバリー構成を無効にする

1. **[Recovery Services コンテナー]** に移動します 
2.  **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** で、マシンを右クリックして **[レプリケーションの無効化]** をクリックします。
3. 移動するすべての VM に対して、この手順を繰り返します。
> [!NOTE]
> モビリティ サービスは保護されたサービスからアンインストールされないため、手動でアンインストールする必要があります。 再度サーバーを保護する場合は、モビリティ サービスのアンインストールを省略できます。

## <a name="delete-the-resources"></a>リソースの削除

1. **[Recovery Services コンテナー]** に移動します
2. **[削除]** をクリックします
3. [前の手順](#identify-the-resources-that-were-used-by-azure-site-recovery)で特定した他のすべてのリソースの削除に進みます
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Azure VM を新しいターゲット リージョンに移動する

要件に基づき、以下の手順に従って、Azure VM をターゲット リージョンに移動します。

- [Azure VM を別のリージョンに移動する](azure-to-azure-tutorial-migrate.md)
- [Azure VM を Availability Zones に移動する](move-azure-VMs-AVset-Azone.md)

## <a name="re-set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>VM の新しいソース リージョンに基づいて Site Recovery を再設定する

[ここ](azure-to-azure-tutorial-enable-replication.md)で説明されている手順を使用して、新しいリージョンに移動された Azure VM のディザスター リカバリーを構成します
