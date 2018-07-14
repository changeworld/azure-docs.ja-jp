---
title: " Azure Site Recovery で VMware vCenter サーバーを管理する | Microsoft Docs"
description: この記事では、Azure Site Recovery でVMware vCenter を追加して管理する方法について説明します。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: ramamill
ms.openlocfilehash: 6f3edf8e5d7a6fda1795991ac0a21cc316c29414
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950446"
---
# <a name="manage-vmware-vcenter-servers"></a>VMware vCenter サーバーを管理する 

この記事では、VMware vCenter で実行できるさまざまな Site Recovery 操作について説明します。 開始する前に、[前提条件](vmware-physical-azure-support-matrix.md#replicated-machines)を確認してください。


## <a name="set-up-an-account-for-automatic-discovery"></a>自動検出用のアカウントを設定する

Site Recovery は、プロセス サーバーで仮想マシンを自動検出するため、また仮想マシンのフェールオーバーおよびフェールバックのために、VMware にアクセスできる必要があります。 アクセス用のアカウントを次のように作成します。

1. 構成サーバー マシンにログオンします。
2. デスクトップのショートカットを使用して cspsconfigtool.exe を起動します。
3. **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。

  ![アカウントの追加](./media/vmware-azure-manage-vcenter/addaccount.png)
1. アカウントの詳細を指定し、**[OK]** をクリックしてそれを追加します。  このアカウントには、次の表に要約されている特権が必要です。 

アカウント情報を Site Recovery サービスと同期するには、15 分程度かかります。

### <a name="account-permissions"></a>アカウントのアクセス許可

|**タスク** | **アカウント** | **アクセス許可** | **詳細**|
|--- | --- | --- | ---|
|**自動検出/移行 (フェールバックなし)** | 少なくとも読み取り専用ユーザーが必要です。 | データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、仮想マシン、ネットワーク) に割り当てます。|
|**レプリケーション/フェールオーバー** | 少なくとも読み取り専用ユーザーが必要です。| データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、仮想マシン、ネットワーク) に割り当てます。<br/><br/> 移行の目的では役立ちますが、完全なレプリケーション、フェールオーバー、フェールバックでは役立ちません。|
|**レプリケーション/フェールオーバー/フェールバック** | 必要なアクセス許可を備えたロール (AzureSiteRecoveryRole) を作成し、このロールを VMware のユーザーまたはグループに割り当てることをお勧めします。 | データ センター オブジェクト –> 子オブジェクトへの伝達、ロール = AzureSiteRecoveryRole<br/><br/> データストア -> 空間の割り当て、データストアの参照、ファイルの低レベルの操作、ファイルの削除、仮想マシン ファイルの更新<br/><br/> ネットワーク -> ネットワークの割り当て<br/><br/> リソース -> リソース プールへの VM の割り当て、電源がオフの VM の移行、電源がオンの VM の移行<br/><br/> タスク -> タスクの作成、タスクの更新<br/><br/> 仮想マシン -> 構成<br/><br/> 仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール<br/><br/> 仮想マシン -> インベントリ -> 作成、登録、登録解除<br/><br/> 仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可<br/><br/> 仮想マシン -> スナップショット -> スナップショットの削除 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、仮想マシン、ネットワーク) に割り当てます。|


## <a name="add-vmware-server-to-the-vault"></a>コンテナーに VMware サーバーを追加する

1. Azure Portal で、コンテナー > **[Site Recovery インフラストラクチャ]** > **[Configuration Severs] (構成サーバー)** を開き、構成サーバーを開きます。
2. **[詳細]** ページで、**[+vCenter]** をクリックします。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>資格情報を変更する

vCenter サーバーまたは ESXi ホストに接続するために使用される資格情報を次のように変更します。

1. 構成サーバーにログオンし、デスクトップから cspsconfigtool.exe を起動します。
2. **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。

  ![アカウントの追加](./media/vmware-azure-manage-vcenter/addaccount.png)
3. 新しいアカウントの詳細を指定し、**[OK]** をクリックしてそれを追加します。 このアカウントには、[上に](#account-permissions)一覧表示されている特権が必要です。
4. Azure Portal で、コンテナー > **[Site Recovery インフラストラクチャ]** > **[Configuration Severs] (構成サーバー)** を開き、構成サーバーを開きます。
5. **[詳細]** ページで、**[サーバーを最新の情報に更新する]** をクリックします。
6. [サーバーを最新の情報に更新する] ジョブが完了したら、vCenter サーバーを選択して vCenter の **[概要]** ページを開きます。
7. **[vCenter server/vSphere host account] (vCenter サーバー/vSphere ホストのアカウント)** フィールドで新しく追加されたアカウントを選択し、**[保存]** をクリックします。

    ![アカウントの変更](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>vCenter サーバーを削除する

1. Azure Portal で、コンテナー > **[Site Recovery インフラストラクチャ]** > **[Configuration Severs] (構成サーバー)** を開き、構成サーバーを開きます。
2. **[詳細]** ページで、vCenter サーバーを選択します。
3. **[削除]** ボタンをクリックします。

  ![アカウントの削除](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
vCenter の IP アドレス、FQDN、またはポートを変更する必要がある場合は、vCenter サーバーを削除し、それをポータルに追加し直す必要があります。
