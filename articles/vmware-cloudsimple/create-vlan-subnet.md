---
title: VLAN/サブネットの作成
description: Azure VMware Solution (AVS) - AVS プライベート クラウド用の VLAN またはサブネットを作成して管理し、ファイアウォール規則を適用する方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 249c48500dbcd75f62f856b3345b3a2c02502d1a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024774"
---
# <a name="create-and-manage-vlanssubnets-for-your-avs-private-clouds"></a>AVS プライベート クラウド用の VLAN またはサブネットを作成して管理する

[ネットワーク] ページで [VLAN/サブネット] タブを開き、AVS プライベート クラウド用の VLAN またはサブネットを作成して管理します。 VLAN/サブネットを作成したら、ファイアウォール規則を適用できます。

## <a name="create-a-vlansubnet"></a>VLAN/サブネットを作成する

1. [AVS ポータルにアクセス](access-cloudsimple-portal.md)し、サイド メニューの **[ネットワーク]** を選択します。
2. **[VLANs/Subnets]\(VLAN/サブネット\)** を選択します。
3. **[Create VLAN/Subnet]\(VLAN/サブネットの作成\)** をクリックします。

    ![[VLANs/Subnets]\(VLAN/サブネット\) ページ](media/vlan-subnet-page.png)

4. 新しい VLAN またはサブネットの AVS プライベート クラウドを選択します。
5. VLAN ID を入力します。
6. サブネット名を入力します。
7. VLAN (サブネット) でルーティングを有効にするには、サブネットの CIDR 範囲を指定します。 CIDR 範囲がオンプレミスのサブネット、Azure サブネット、ゲートウェイ サブネットのいずれとも重複しないことを確認します。
8. **[送信]** をクリックします。

    ![VLAN/サブネットの作成](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> プライベート クラウドあたり 30 VLAN のクォータがあります。 これらの制限は、[サポートに連絡](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)することによって増やすことができます。

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>VLAN 情報を使用し、vSphere で分散ポート グループを設定する

vSphere で分散ポート グループを作成するには、<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">vSphere Networking Guide</a> にある VMware トピックの「Add a distributed port group」 (分散ポート グループの追加) の指示に従います。 分散ポート グループを設定するときに、AVS 構成から VLAN 情報を指定します。

![分散ポート グループ](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>ファイアウォール テーブルの選択

ファイアウォールのテーブルと関連ルールは、 **[ネットワーク] の [Firewall Tables]\(ファイアウォール テーブル\)** ページで定義されます。 AVS プライベート クラウドの VLAN またはサブネットに適用するファイアウォール テーブルを選択するには、VLAN またはサブネットを選び、 **[VLAN/サブネット]** ページで **[ファイアウォール テーブルのアタッチ]** をクリックします。 ファイアウォール テーブルの設定方法とルールの定義方法については、「[ファイアウォール テーブル](firewall.md)」を参照してください。

![ファイアウォール テーブルのリンク](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> 1 つのサブネットは、1 つのファイアウォール テーブルに関連付けることができます。 1 つのファイアウォール テーブルは、複数のサブネットに関連付けることができます。

## <a name="edit-a-vlansubnet"></a>VLAN/サブネットの編集

VLAN/サブネットの設定を編集するには、 **[VLANs/Subnets]\(VLAN/サブネット\)** ページでそれを選択し、 **[編集]** アイコンをクリックします。 変更を加え、 **[送信]** をクリックします。

## <a name="delete-a-vlansubnet"></a>VLAN/サブネットの削除

VLAN/サブネットを削除するには、 **[VLANs/Subnets]\(VLAN/サブネット\)** ページでそれを選択し、 **[削除]** アイコンをクリックします。 **[削除]** をクリックして確定します。
