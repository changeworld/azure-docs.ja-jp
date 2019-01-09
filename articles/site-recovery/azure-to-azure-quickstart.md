---
title: Azure IaaS VM のセカンダリ Azure リージョンへのディザスター リカバリーの設定
description: このクイック スタートでは、Azure Site Recovery サービスを使用した Azure リージョン間での Azure IaaS VM のディザスター リカバリーに必要な手順を紹介します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 12/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 881ec1a12f42c56f3ef3b4c3d858102ac36d2eb0
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793667"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Azure VM のセカンダリ Azure リージョンへのディザスター リカバリーの設定 

[Azure Site Recovery](site-recovery-overview.md) サービスは、計画された停止や計画外の停止の際にビジネス アプリを実行し続け、使用できるようにすることで、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 Site Recovery は、レプリケーション、フェールオーバー、フェールバックなど、オンプレミスのマシンと Azure Virtual Machines (VM) のディザスター リカバリーを管理し、調整します。

このクイック スタートでは、Azure VM を別の Azure リージョンにレプリケートする方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!NOTE]
> この記事は、既定のオプションと最小限のカスタマイズで Azure Site Recovery のエクスペリエンスを新しいユーザーに紹介することを意図しています。 カスタマイズ可能な各種の設定について、さらに詳しく知りたい場合は、[Azure VM のレプリケーションの有効化に関するチュートリアル](azure-to-azure-tutorial-enable-replication.md)を参照してください。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal (http://portal.azure.com) にログインします。

## <a name="enable-replication-for-the-azure-vm"></a>Azure VM のレプリケーションの有効化

1. Azure Portal で **[仮想マシン]** をクリックし、レプリケートする VM を選択します。
2. **[操作]** で、**[ディザスター リカバリー]** をクリックします。
3. **[Configure disaster recovery]\(ディザスター リカバリーを構成する\)** > **[ターゲット リージョン]** で、レプリケート先のターゲット リージョンを選択します。
4. このクイック スタートでは、その他は既定の設定をそのまま使用します。
5. **[レプリケーションを有効にする]** をクリックします。 これにより VM レプリケーションを有効にするジョブが開始されます。

    ![レプリケーションの有効化](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>設定の確認

レプリケーション ジョブの完了後、レプリケーションの状態を確認し、レプリケーションの設定を変更して、デプロイをテストできます。

1. VM のメニューで、**[ディザスター リカバリー]** をクリックします。
2. レプリケーションの正常性、作成された復旧ポイント、およびマップ上のソース リージョンとターゲット リージョンを確認できます。

   ![レプリケーションの状態](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

プライマリ リージョンの VM のレプリケーションは、そのレプリケーションを無効にすると停止されます。

- ソース レプリケーションの設定は自動的にクリーンアップされます。 レプリケーションの一部としてインストールされた Site Recovery 拡張機能は削除されないため、手動で削除する必要がある点に注意してください。 
- VM の Site Recovery の課金も停止します。

次の手順でレプリケーションを停止します

1. VM を選択します。
2. **[ディザスター リカバリー]** で、**[レプリケーションを無効にする]** をクリックします。

   ![レプリケーションを無効にする](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>次の手順

このクイック スタートでは、1 つの VM をセカンダリ リージョンにレプリケートしました。 さらに他のオプションについても考察し、復旧計画を使用して一連の Azure VM をレプリケートしてみましょう。

> [!div class="nextstepaction"]
> [Azure VM のディザスター リカバリーを構成する](azure-to-azure-tutorial-enable-replication.md)
