---
title: Azure Site Recovery での VMware ディザスター リカバリーのために構成サーバーを展開する | Microsoft Docs
description: この記事では、Azure Site Recovery での VMware ディザスター リカバリーのために構成サーバーを展開する方法について説明します
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 4d944bec9d1cf73263d9deb54d0d9b3f6fc5532f
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285035"
---
# <a name="deploy-a-configuration-server"></a>構成サーバーをデプロイする

Azure への VMware 仮想マシンと物理サーバーのディザスター リカバリーに [Azure Site Recovery](site-recovery-overview.md) を使うときは、オンプレミスの構成サーバーを展開します。 構成サーバーは、オンプレミスの VMware と Azure の間の通信を調整します。 データのレプリケーションも管理します。 この記事では、VMware VM を Azure にレプリケートするときの構成サーバーの展開に必要な手順について説明します。 物理サーバーのレプリケーション用に構成サーバーを設定する必要がある場合は、[こちらの記事に従ってください](physical-azure-set-up-source.md)。

## <a name="prerequisites"></a>前提条件

高可用性の VMware VM として構成サーバーを展開することをお勧めします。 構成サーバーの要件を次の表に示します。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]



### <a name="prepare-for-mysql-installation"></a>MySQL をインストールする準備

構成サーバーには MySQL がインストールされている必要があります。 これは、次のいずれか方法で実行できます:

- 構成サーバー管理ウィザードの実行時に Site Recovery にダウンロードとインストールを行わせます。 ユーザーが特定のアクションを実行する必要はありません。
- 手動で MySQL をダウンロードして C:\Temp\ASRSetup フォルダーに配置します。 その上でインストールを実行します。 Site Recovery は、ウィザードの実行時にインストール済みであることを認識します。
- 手動で MySQL をダウンロードして C:\Temp\ASRSetup フォルダーに配置します。 ウィザードを実行すると、セットアップ ファイルが検出され、その場所からインストールが行われます。 


## <a name="capacity-planning"></a>容量計画

構成サーバーのサイズ要件は、潜在的なデータ変更率に依存します。 次の表を参考にしてください。

| **CPU** | **メモリ** | **キャッシュ ディスク サイズ** | **データの変更率** | **保護されたマシン** |
| --- | --- | --- | --- | --- |
| 8 vCPU (2 ソケット * 4 コア @ 2.5 GHz) |16 GB |300 GB |500 GB 以下 |100 台未満のマシンをレプリケートします。 |
| 12 vCPU (2 ソケット * 6 コア @ 2.5 GHz) |18 GB |600 GB |500 GB ～ 1 TB |100 ～ 150 台のマシンをレプリケートします。 |
| 16 vCPU (2 ソケット * 8 コア @ 2.5 GHz) |32 GB |1 TB (テラバイト) |1 TB ～ 2 TB |150 ～ 200 台のマシンをレプリケートします。 |


VMware VM をレプリケートする場合は、[容量計画に関する考慮事項](/site-recovery-plan-capacity-vmware.md)の詳細をご覧ください。 VMware のレプリケーションの場合は [Deployment Planner ツール](site-recovery-deployment-planner.md)を実行します。



## <a name="download-the-template"></a>テンプレートをダウンロードする

Site Recovery では、高可用性の VMware VM として構成サーバーを設定するためのテンプレートをダウンロードできます。 

1. コンテナーで、**[インフラストラクチャの準備]** > **[ソース]** の順に移動します。
2. **[ソースの準備]** で **[+ 構成サーバー]** を選びます。
3. **[サーバーの追加]** で、**[サーバーの種類]** に **[VMware の構成サーバー]** が表示されていることを確認します。
4. 構成サーバー用の Open Virtualization Format (OVF) テンプレートをダウンロードします。

  > [!TIP]
  構成サーバー テンプレートの最新バージョンは、[Microsoft ダウンロード センター](https://aka.ms/asrconfigurationserver)から直接ダウンロードできます。


## <a name="import-the-template-in-vmware"></a>VMware にテンプレートをインポートする


1. VMWare vSphere Client を使って、VMware vCenter サーバーまたは vSphere ESXi ホストにサインインします。
2. **[File]\(ファイル\)** メニューの **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** を選び、Deploy OVF Template (OVF テンプレートのデプロイ) ウィザードを起動します。

     ![OVF テンプレート](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. **[Select source]\(ソースの選択\)** で、ダウンロードした OVF の場所を入力します。
4. **[Review details]\(詳細の確認\)** で、**[Next]\(次へ\)** を選択します。
5. **[Select name and folder]\(名前とフォルダーの選択\)** および **[Select configuration]\(構成の選択\)** は、既定の設定のままにします。
6. **[Select storage]\(ストレージの選択\)** で、パフォーマンスを最大にするために、**[Select virtual disk format]\(仮想ディスクの形式の選択\)** の **[Thick Provision Eager Zeroed]\(シック プロビジョニング Eager Zeroed\)** を選びます。
4. ウィザードの残りのページでは、既定の設定をそのまま使います。
5. **[Ready to complete]\(完了の準備\)** で、次の操作を行います。

    * 既定の設定で VM をセットアップするには、**[Power on after deployment]\(デプロイ後に電源をオンにする\)** > **[Finish]\(完了\)** の順に選びます。

    * 追加のネットワーク インターフェイスを追加するには、**[Power on after deployment]\(デプロイ後に電源をオンにする\)** をオフにし、**[Finish]\(完了\)** を選びます。 既定でデプロイされる構成サーバー テンプレートの NIC は 1 つだけですが、 デプロイ後にさらに NIC を追加することができます。


## <a name="add-an-additional-adapter"></a>さらにアダプターを追加する

構成サーバーにさらに NIC を追加する場合は、サーバーをコンテナーに登録する前に追加します。 登録後のアダプターの追加はサポートされていません。

1. vSphere Client インベントリで VM を右クリックし、**[Edit Settings]\(設定の編集\)** を選びます。
2. **[Hardware]\(ハードウェア\)** で、**[Add]\(追加\)** > **[Ethernet Adapter]\(イーサネット アダプター\)** の順に選択します。 次に、**[次へ]** を選択します。
3. アダプターの種類およびネットワークを選びます。 
4. VM がオンになったときに仮想 NIC を接続するには、**[Connect at power on]\(電源をオンにしたときに接続する\)** をオンにします。 次に、**[Next]\(次へ\)** > **[Finish]\(完了\)** > **OK** の順に選びます。
 

## <a name="register-the-configuration-server"></a>構成サーバーを登録する 

1. VMWare vSphere Client のコンソールで、VM をオンにします。
2. VM が Windows Server 2016 のインストール エクスペリエンスで起動します。 使用許諾契約書に同意し、管理者パスワードを入力します。
3. インストールの完了後に、管理者として VM にサインインします。
4. 初めてサインインすると、Azure Site Recovery 構成ツールが起動されます。
5. 構成サーバーを Site Recovery に登録するために使う名前を入力します。 次に、**[次へ]** を選択します。
6. このツールは、VM が Azure に接続できることを確認します。 接続が確立された後、**[サインイン]** を選択して、自分の Azure サブスクリプションにサインインします。 この資格情報は、構成サーバーを登録するコンテナーにアクセスできる必要があります。
7. ツールがいくつかの構成タスクを実行した後、再起動されます。
8. 再度マシンにサインインします。 構成サーバーの管理ウィザードが自動的に起動されます。

### <a name="configure-settings"></a>設定を構成する

1. 構成サーバー管理ウィザードで、**[接続の設定]** を選びます。 レプリケーション トラフィックを受信する NIC を選び、**[保存]** を選びます。 構成後、この設定を変更することはできません。
2. **[Recovery Services コンテナーを選択する]** で、Azure サブスクリプションと、関連するリソース グループおよびコンテナーを選びます。
3. **[サードパーティ製ソフトウェアのインストール]** でライセンス契約に同意します。 [MySQL のインストール方法](#prepare-for-mysql-installation)に従って MySQL をインストールします。
4. **[VMware PowerCLI のインストール]** を選択します。 このステップを行う前に、すべてのブラウザー ウィンドウを閉じてください。 その後 **[続行]** を選択します。
5. **[アプライアンス構成の検証]** で、続行する前に前提条件が検証されます。
6. **[Configure vCenter Server/vSphere ESXi server]\(vCenter Server/vSphere ESXi サーバーの構成\)** で、レプリケートする VM が存在している vCenter サーバーまたは vSphere ホストの FQDN または IP アドレスを入力します。 サーバーがリッスンしているポートと、コンテナーでの VMware サーバーのフレンドリ名を入力します。
7. VMware サーバーに接続するために構成サーバーによって使われる資格情報を入力します。 Site Recovery はこれらの資格情報を使って、レプリケーションに利用できる VMware VM を自動的に検出します。 **[追加]**、**[続行]** の順に選択します。
8. **[仮想マシンの資格情報の構成]** で、レプリケーションが有効になったときに Azure Site Recovery モビリティ サービスをマシンに自動的にインストールするために使われるユーザー名とパスワードを入力します。 Windows マシンの場合、このアカウントは、レプリケートするマシンに対するローカル管理者特権を持っている必要があります。 Linux の場合は、ルート アカウントの詳細を指定します。
9. **[構成の確定]** を選択して、登録を完了します。 
10. 登録が完了したら、Azure Portal で、構成サーバーおよび VMware サーバーがコンテナーの **[ソース]** ページの一覧に表示されていることを確認します。 その後、**[OK]** を選択して、ターゲットの設定を構成します。


## <a name="upgrade-the-configuration-server"></a>構成サーバーをアップグレードする

構成サーバーを最新バージョンにアップグレードするには、[こちら](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)にある手順を参照してください。


## <a name="troubleshoot-deployment-issues"></a>デプロイに関する問題のトラブルシューティング

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>次の手順

Azure への [VMware VM](vmware-azure-tutorial.md) のディザスター リカバリーを設定する。
