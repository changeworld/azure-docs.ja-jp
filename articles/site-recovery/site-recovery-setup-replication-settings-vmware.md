---
title: "Azure Site Recovery 用のレプリケーション設定のセットアップ | Microsoft Docs"
description: "Site Recovery をデプロイし、VMM クラウド内の Hyper-V VM の Azure へのレプリケーション、フェールオーバー、復旧を調整する方法を説明します。"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/19/2017
ms.author: sutalasi
translationtype: Human Translation
ms.sourcegitcommit: 74d51269cdd55e39eaa2963fec2b409decb47d0a
ms.openlocfilehash: a279fdc35c62c76e6f0858d52c64240032669f15


---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>VMware から Azure へのレプリケーション ポリシーの管理


## <a name="create-a-new-replication-policy"></a>新しいレプリケーション ポリシーの作成

1. 左側のメニューで、[Manage (管理)]、[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)] の順にクリックします。 
2. [For VMware and Physical machines (VMware と物理マシン用)] セクションの下の [Replication policies (レプリケーション ポリシー)] を選択します。
3. 上部の [+Replication policy (+レプリケーション ポリシー)] をクリックします。

    ![レプリケーション ポリシーを作成する](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. ポリシー名を入力します。

5. [RPO しきい値] で、RPO の制限を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
6. [復旧ポイントのリテンション期間] で、各復旧ポイントの保持期間の長さを時間単位で指定します。 保護されたマシンはこの期間内のどのポイントにも復旧できます。 

    > [!NOTE] 
    > Premium Storage にレプリケートされたマシンでは、最大 24 時間のリテンション期間がサポートされており、Standard ストレージにレプリケートされたマシンでは、72 時間のリテンション期間がサポートされています。
    
    > [!NOTE] 
    > フェールバック用のレプリケーション ポリシーは、自動的に作成されます。

7. [アプリ整合性スナップショットの頻度] で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (分単位) を指定します。

8. [OK] をクリックします。 ポリシーは、30 秒 ～ 1 分ほどで作成されます。

![レプリケーション ポリシーを作成する](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-configuration-server-with-replication-policy"></a>構成サーバーをレプリケーション ポリシーに関連付ける
1. 構成サーバーを関連付けるレプリケーション ポリシーをクリックします。
2. 上部の [関連付け] をクリックします。
![レプリケーション ポリシーを作成する](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. サーバーの一覧から [構成サーバー] を選択します。
4. [OK] をクリックします。 構成サーバーは、1 ～ 2 分ほどで関連付けられます。

![レプリケーション ポリシーを作成する](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-replication-policy"></a>レプリケーション ポリシーを編集する
1. レプリケーション設定を編集するレプリケーション ポリシーをクリックします。
![レプリケーション ポリシーを作成する](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. 上部の [設定の編集] をクリックします。
![レプリケーション ポリシーを作成する](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. 必要に応じて設定を変更します。
4. 上部の [保存] をクリックします。 ポリシーは、レプリケーション ポリシーを使用している VM の数に応じて、2 ～ 5 分ほどで保存されます。

![レプリケーション ポリシーを作成する](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-configuration-server-from-replication-policy"></a>構成サーバーとレプリケーション ポリシーの関連付けを解除する
1. 構成サーバーを関連付けるレプリケーション ポリシーをクリックします。
2. 上部の [関連付け解除] をクリックします。
3. サーバーの一覧から [構成サーバー] を選択します。
4. [OK] をクリックします。 構成サーバーは、1 ～ 2 分ほどで関連付けが解除されます。
    
    > [!NOTE] 
    > そのポリシーを使用してレプリケートされた項目が&1; つでもある場合は、構成サーバーの関連付けを解除することはできません。 構成サーバーの関連付けを解除する前に、そのポリシーを使用してレプリケートされた項目がないことを確認してください。

## <a name="delete-replication-policy"></a>レプリケーション ポリシーを削除する 

1. 削除するレプリケーション ポリシーをクリックします。
2. [削除] をクリックします。 ポリシーは、30 秒 ～ 1 分ほどで削除されます。

    > [!NOTE] 
    > 関連付けられている構成サーバーが 1 つでもある場合は、レプリケーション ポリシーを削除することはできません。 ポリシーを削除する前に、そのポリシーを使用してレプリケートされた項目がないことを確認し、関連付けられているすべての構成サーバーを削除してください。


<!--HONumber=Jan17_HO4-->


