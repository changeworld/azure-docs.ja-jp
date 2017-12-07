---
title: "Azure Site Recovery で Azure VM をセカンダリ リージョンにレプリケートする | Microsoft Docs"
description: "この記事では、Azure Site Recovery サービスを使用して、1 つの Azure リージョンで実行している Azure VM を別のリージョンへレプリケートする方法について説明します。"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/29/2017
ms.author: asgang
ms.openlocfilehash: 114390712f5e5bf62ec515db62469e09361b8f85
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Azure 仮想マシンを別の Azure リージョンにレプリケートする


この記事では、Azure Site Recovery サービスを使用して、1 つの Azure リージョンにある Azure 仮想マシン (VM) をセカンダリの Azure リージョンへレプリケートする方法について説明します。

>[!NOTE]
>
> Site Recovery を使った Azure VM レプリケーションは現在プレビューの段階です。

## <a name="prerequisites"></a>前提条件

* Recovery Services コンテナーが適切な場所にある。 VM をレプリケートするターゲット リージョンにコンテナーを作成することをお勧めします。
* ネットワーク セキュリティ グループ (NSG) ルールまたはファイアウォール プロキシを使用して、Azure VM での送信インターネット接続へのアクセスを制御する場合は、必要な URL と IP を必ず許可してください。 [詳細情報](./site-recovery-azure-to-azure-networking-guidance.md)
* オンプレミスと Azure のソースの場所が ExpressRoute または VPN で接続されている場合は、[設定方法を確認](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration)してください。
* Azure ユーザー アカウントには、Azure VM のレプリケートを可能にするための特定の[アクセス許可](../site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)が必要です。
ディザスター リカバリー リージョンとして使用するターゲットの場所で VM を作成するには、Azure サブスクリプションを有効にする必要があります。 サポートに連絡して、必要なクォータを有効にしてください。

## <a name="enable-replication"></a>Enable replication

この手順では、東アジアがソースの場所、東南アジアがターゲットの場所として使用されています。

1. コンテナーの **[+ レプリケート ]** をクリックし、仮想マシンのレプリケーションを有効にします。
2. **[ソース:]** が **[Azure]** に設定されていることを確認します。
3. **[ソースの場所]** を [東アジア] に設定します。
4. **[デプロイメント モデル]** で、**[クラシック]** または **[Resource Manager]** を選択します。
5. **[リソース グループ]** で、ソース VM が属するグループを選択します。 選択したリソース グループ下のすべての VM が一覧表示されます。

    ![Enable replication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

6. **[仮想マシン] > [仮想マシンの選択]** で、レプリケートする各 VM をクリックして選択します。 選択できるのは、レプリケーションを有効にできるマシンのみです。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)
    
7. **[設定]** > **[ターゲットの場所]** で、ソース VM データのレプリケート先となる場所を指定します。 Site Recovery は、選択された VM のリージョンに応じて、適切なターゲット リージョンの一覧を提供します。
8. Site Recovery では、既定のターゲット設定を行います。 これらは、必要に応じて変更できます。

    - **ターゲット リソース グループ**。 既定では、Site Recovery は "asr" というサフィックスを持つターゲット リージョンに、新しいリソース グループを作成します。 作成されたリソース グループが既に存在する場合は、再利用します。
    - **ターゲット仮想ネットワーク**。 既定では、Site Recovery は "asr" というサフィックスを持つターゲット リージョンに、新しい仮想ネットワークを作成します。 このネットワークは、ソース ネットワークにマップされます。 ネットワーク マッピングの詳細については、[こちら](site-recovery-network-mapping-azure-to-azure.md)を参照してください。
    - **ターゲット ストレージ アカウント**。 既定では、Site Recovery は、ソース VM のストレージ構成に一致する新しいターゲット ストレージ アカウントを作成します。 作成されたアカウントが既に存在する場合は、再利用します。
    - **キャッシュ ストレージ アカウント**。 Azure Site Recovery は追加のキャッシュ ストレージ アカウント、つまりソース リージョンを作成します。 ソース VM での変更はすべて追跡され、ターゲットの場所にレプリケートする前に、キャッシュ ストレージ アカウントに送信されます。
    - **可用性セット**。 既定では、Site Recovery は "asr" というサフィックスを持つターゲット リージョンに、新しい可用性セットを作成します。 作成されたセットが既に存在する場合は、再利用します。
    - **レプリケーション ポリシー**。 Site Recovery では、復旧ポイントのリテンション期間の履歴とアプリ整合性スナップショットの頻度の設定が定義されます。 既定では、新しいレプリケーション ポリシーが、既定の設定で Site Recovery によって作成されます。つまり、このポリシーの復旧ポイントのリテンション期間は "24 時間" に、アプリ整合性スナップショットの頻度は "60 分" に設定されます。

    ![Enable replication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
9. **[レプリケーションを有効にする]** をクリックして、VM の保護を開始します。

## <a name="customize-target-resources"></a>ターゲット リソースのカスタマイズ

1. 以下のターゲットの既定値のいずれかを変更します。

    - **ターゲット リソース グループ**。 サブスクリプション内のターゲットの場所にあるリソース グループの一覧から、リソース グループを選択できます。
    - **ターゲット仮想ネットワーク**。 ターゲットの場所にあるすべての仮想ネットワークの一覧から選択します。
    - **可用性セット**。可用性セットの設定をソース リージョンのセットに配置されている VM に追加することのみが可能です。
    - **ターゲット ストレージ アカウント**。利用可能な任意のアカウントを追加します。

        ![Enable replication](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. **[Create target resource\(ターゲット リソースを作成する)]** > **[レプリケーションを有効にする]** の順にクリックします。 初期レプリケーションの間は、VM の状態の更新に少し時間がかかる場合があります。 **[更新]** をクリックして、最新の状態を取得します。

    ![Enable replication](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)
    
3. VM が保護された後、**[レプリケートされたアイテム]** で VM の正常性をチェックします。



## <a name="next-steps"></a>次のステップ
[テスト フェールオーバーの実行方法](../azure-to-azure-tutorial-dr-drill.md)を確認する

