---
title: Azure Site Recovery で使用するリソースを作成する | Microsoft Docs
description: Azure Site Recovery を使用したオンプレミス マシンのレプリケーションのために Azure を準備する方法について説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/06/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6a113169cb3f8fea1012643efcb56e5cf6c7e908
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915971"
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>オンプレミス マシンのレプリケーションために Azure リソースを準備する

 [Azure Site Recovery](site-recovery-overview.md) は、計画された停止や計画外の停止の際にビジネス アプリを実行し続け、使用できるようにすることで、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 Site Recovery は、レプリケーション、フェールオーバー、フェールバックなど、オンプレミスのマシンと Azure Virtual Machines (VM) のディザスター リカバリーを管理し、調整します。

この記事は、オンプレミスの VM のディザスター リカバリーを設定する方法について説明するシリーズの 1 番目のチュートリアルです。 オンプレミスの VMware VM、Hyper-V VM、または物理サーバーの保護に関係します。

これらのチュートリアルは、シナリオの最も簡単な展開パスを示すことを目的として作られています。 可能であれば既定のオプションを使い、すべての可能な設定とパスを示してはいません。 

この記事では、オンプレミス VM (Hyper-V または VMware) や Windows/Linux 物理サーバーを Azure にレプリケートするときの Azure コンポーネントを準備する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * お使いの Azure アカウントにレプリケーションのアクセス許可があることを確認します。
> * Azure ストレージ アカウントを作成します。 レプリケートされたマシンのイメージはそこに保存されます。
> * Recovery Services コンテナーを作成します。 コンテナーには、VM および他のレプリケーション コンポーネントのメタデータと構成情報が保持されます。
> * Azure ネットワークをセットアップします。 フェールオーバー後に作成された Azure VM は、この Azure ネットワークに参加します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure ポータル](http://portal.azure.com)にサインインします。

## <a name="verify-account-permissions"></a>アカウントのアクセス許可を確認する

無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの管理者になっています。 サブスクリプションの管理者でなければ、管理者に協力を求め、必要なアクセス許可を割り当てます。 新しい仮想マシンのレプリケーションを有効にするには、次を実行するためのアクセス許可が必要です。

- 選択したリソース グループ内に VM を作成する。
- 選択した仮想ネットワーク内に VM を作成する。
- 選択したストレージ アカウントに書き込む。

これらのタスクを遂行するには、お使いのアカウントに、"仮想マシン共同作成者" 組み込みロールが割り当てられている必要があります。 また、Site Recovery 操作をコンテナーで管理するには、お使いのアカウントに、"Site Recovery 共同作成者" 組み込みロールが割り当てられている必要があります。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

レプリケートされたマシンのイメージは Azure Storage に保存されます。 オンプレミスから Azure にフェールオーバーするとき、ストレージから Azure VM が作成されます。 ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。 このチュートリアルでは、西ヨーロッパを使用しています。

1. [Azure portal](https://portal.azure.com) のメニューで、**[リソースの作成]** > **[Storage]** > **[ストレージ アカウント - Blob、File、Table、Queue]** の順に選択します。
2. **[ストレージ アカウントの作成]** で、アカウントの名前を入力します。 この一連のチュートリアルでは、**contosovmsacct1910171607** を使用します。 選択する名前は Azure 内で一意である必要があります。長さは 3 から 24 文字で、使用できるのは数字と小文字のみです。
3. **[デプロイ モデル]** で、**[Resource Manager]** を選択します。
4. **[アカウントの種類]** で **[ストレージ (汎用 v1)]** を選択します。 Blob ストレージを選択しないでください。
5. **[レプリケーション]** では、ストレージの冗長性のために、既定の **[読み取りアクセス geo 冗長ストレージ]** を選択します。 **[安全な転送が必須]** は **[無効]** のままにしています。
6. **[パフォーマンス]** には **[標準]** を選択し、**[アクセス層]** には既定のオプションの **[ホット]** を選択します。
7. **[サブスクリプション]** で、新しいストレージ アカウントを作成するサブスクリプションを選択します。
8. **[リソース グループ]** で、新しいリソース グループ名を入力します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 この一連のチュートリアルでは、**ContosoRG** を使用します。
9. **[場所]** で、ストレージ アカウントの地理的な場所を選択します。 

   ![ストレージ アカウントの作成](media/tutorial-prepare-azure/create-storageacct.png)

9. **[作成]** をクリックしてストレージ アカウントを作成します。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

1. Azure portal で、**[リソースの作成]** > **[Storage]** > **[Backup and Site Recovery (OMS)]** の順に選択します。
2. **[名前]** ボックスに、コンテナーを識別する表示名を入力します。 この一連のチュートリアルでは、**ContosoVMVault** を使用します。
3. **[リソース グループ]** では、**contosoRG** を使います。
4. **[場所]** では、 **[西ヨーロッパ]** を使います。
5. ダッシュボードから資格情報コンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]** > **[作成]** の順に選択します。

   ![新しい資格情報コンテナーの作成](./media/tutorial-prepare-azure/new-vault-settings.png)

   新しい資格情報コンテナーは、**[ダッシュボード]** > **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ページに表示されます。

## <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップ

フェールオーバー後にストレージから作成された Azure VM は、このネットワークに参加します。

1. [Azure Portal](https://portal.azure.com) で、**[リソースの作成]** > **[ネットワーク]** > **[仮想ネットワーク]** の順に選択します。
2. デプロイ モデルとして **[リソース マネージャー]** をそのまま選択します。
3. **[名前]** で、ネットワーク名を入力します。 Azure リソース グループ内で一意となる名前を使用してください。 このチュートリアルでは **ContosoASRnet** を使います。
4. ネットワークを作成するリソース グループを指定します。 既存のリソース グループ **contosoRG** を使っています。
5. **[アドレス範囲]** には、ネットワーク **10.0.0.0/24** の範囲を入力します。 このネットワークでは、サブネットは使っていません。
6. **[サブスクリプション]** で、ネットワークを作成するサブスクリプションを選択します。
7. **[場所]** では **[西ヨーロッパ]** を選択します。 ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。
8. 基本的な DDoS 保護の既定のオプションのままにし、ネットワーク上にサービス エンドポイントはありません。
9. **Create** をクリックしてください。

   ![仮想ネットワークの作成](media/tutorial-prepare-azure/create-network.png)

   仮想ネットワークの作成には数秒かかります。 作成が完了すると、Azure Portal ダッシュボードに表示されます。

## <a name="useful-links"></a>便利なリンク

- [Azure ネットワーク](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)についての学習。
- [Azure Storage の種類](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)についての学習。
- [ストレージの冗長性](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs#read-access-geo-redundant-storage)についての詳細、およびストレージの[セキュリティで保護された転送](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)。



## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure にディザスター リカバリーするためにオンプレミス VMware インフラストラクチャを準備する](tutorial-prepare-on-premises-vmware.md)
