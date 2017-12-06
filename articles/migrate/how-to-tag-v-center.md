---
title: "VMware vCenter でタグ付けを使用して VM をグループ化する | Microsoft Docs"
description: "Azure Migrate サービスでアセスメントを実行する前にグループを作成する方法について説明します。"
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: db33e31cdef143aa70809442457e447446a1681a
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2017
---
# <a name="group-vms-with-vcenter-tagging"></a>vCenter でのタグ付けを使用した VM のグループ化

この記事では、VMware vCenter でタグ付けを使用して、[Azure Migrate](migrate-overview.md) でのアセスメントのためにマシンのグループを作成する方法について説明します。 グループを作成するときに、使用するタグ カテゴリを指定します。 

## <a name="set-up-tagging"></a>タグ付けの設定

Azure Migrate のデプロイ時に、オンプレミスの Azure Migrate VM は、vCenter サーバーによって管理された ESXi ホストで実行されているマシンを検出します。 この検出プロセスの前に、vCenter でタグ付けを設定する必要があります。

1. VMware vSphere Web Client で、vCenter サーバーに移動します。
2. **[Tags]\(タグ\)** をクリックして現在のタグを確認します。
3. VM にタグ付けするには、**[Related Objects]\(関連オブジェクト\)** > **[Virtual Machines]\(仮想マシン\)** を選択し、タグ付けする VM を選択します。
4. **[Summary]\(概要\)** > **[Tags]\(タグ\)** で、**[Assign]\(割り当て\)** をクリックします。 
5. **[New Tag]\(新しいタグ\)** をクリックし、タグ名と説明を入力します。
6. タグのカテゴリを作成するには、ドロップダウン リストで **[New Category]\(新しいカテゴリ\)** を選択します。
7. カテゴリ名と説明を入力し、基数を指定します。 次に、 **[OK]**をクリックします

    ![VM のタグ](./media/how-to-tag-v-center/vm-tag.png)

## <a name="use-tagging-to-create-groups"></a>タグ付けを使用したグループの作成

1. [VMware のアセスメントに関するチュートリアル](tutorial-assessment-vmware.md#run-the-collector-to-discover-vms)の説明に従って、オンプレミスのマシンの検出を設定します。
2. **[Tag category for grouping]\(グループ化用のタグ カテゴリ\)** で、アセスメント グループが基づく vCenter のタグ カテゴリを選択します。 Azure Migrate により、選択したカテゴリのグループが自動的に作成されます。

    

## <a name="next-steps"></a>次のステップ

[VMware VM のアセスメントを設定](tutorial-assessment-vmware.md)します。
