---
title: "Azure Site Recovery で使用するリソースを作成する | Microsoft Docs"
description: "Azure Site Recovery を使用したオンプレミス マシンのレプリケーションのために Azure を準備する方法について説明します。"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2f6ff1d30eef1fe34e55457d9bdd4295804ec16a
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2018
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>オンプレミス マシンのレプリケーションために Azure リソースを準備する

 [Azure Site Recovery](site-recovery-overview.md) は、計画された停止や計画外の停止の際にビジネス アプリを実行し続け、使用できるようにすることで、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 Site Recovery は、レプリケーション、フェールオーバー、フェールバックなど、オンプレミスのマシンと Azure Virtual Machines (VM) のディザスター リカバリーを管理し、調整します。

このチュートリアルでは、オンプレミス VM (Hyper-V または VMware) や Windows/Linux 物理サーバーを Azure にレプリケートするときの Azure コンポーネントを準備する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * お使いのアカウントにレプリケーションのためのアクセス許可があることを確認します。
> * Azure ストレージ アカウントを作成します。
> * Azure ネットワークを設定します。 フェールオーバー後に作成された Azure VM は、この Azure ネットワークに参加します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure ポータル](http://portal.azure.com)にサインインします。

## <a name="verify-account-permissions"></a>アカウントのアクセス許可を確認する

無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの管理者になっています。 サブスクリプションの管理者でなければ、管理者に協力を求め、必要なアクセス許可を割り当てます。 新しい仮想マシンのレプリケーションを有効にするには、次を実行するためのアクセス許可が必要です。

- 選択したリソース グループ内に VM を作成する。
- 選択した仮想ネットワーク内に VM を作成する。
- 選択したストレージ アカウントに書き込む。

"仮想マシン共同作成者" 組み込みロールには、以上のアクセス許可が与えられています。 このほか、Site Recovery 操作を管理するためのアクセス許可も必要です。 "Site Recovery 共同作成者" ロールには、Recovery Services コンテナーでの Site Recovery の操作の管理に必要なアクセス許可がすべて付与されています。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

レプリケートされたマシンのイメージは Azure Storage に保存されます。 オンプレミスから Azure にフェールオーバーするとき、ストレージから Azure VM が作成されます。

1. [Azure Portal](https://portal.azure.com) のメニューで、**[新規]** > **[ストレージ]** > **[ストレージ アカウント]** を選択します。
2. **[ストレージ アカウントの作成]** で、アカウントの名前を入力します。 この一連のチュートリアルでは、**contosovmsacct1910171607** という名前を使用します。 名前は Azure 内で一意にする必要があります。長さは 3 から 24 文字で、使用できるのは数字と小文字のみです。
3. **[デプロイ モデル]** で、**[Resource Manager]** を選択します。
4. **[アカウントの種類]** で **[汎用]** を選択します。 **[パフォーマンス]** で **[Standard]** を選択します。 Blob ストレージを選択しないでください。
5. **[レプリケーション]** では、ストレージの冗長性のために、既定の **[読み取りアクセス geo 冗長ストレージ]** を選択します。
6. **[サブスクリプション]** で、新しいストレージ アカウントを作成するサブスクリプションを選択します。
7. **[リソース グループ]** で、新しいリソース グループ名を入力します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 この一連のチュートリアルでは、**ContosoRG** という名前を使用します。
8. **[場所]** で、ストレージ アカウントの地理的な場所を選択します。 ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。 この一連のチュートリアルでは、**[西ヨーロッパ]** リージョンを使用します。

   ![ストレージ アカウントの作成](media/tutorial-prepare-azure/create-storageacct.png)

9. **[作成]** をクリックしてストレージ アカウントを作成します。

## <a name="create-a-vault"></a>コンテナーの作成

1. Azure Portal で、**[リソースの作成]** > **[監視 + 管理]** > **[Backup and Site Recovery]\(Backup と Site Recovery\)** の順に選択します。
2. **[名前]**ボックスに、コンテナーを識別する表示名を入力します。 このチュートリアルでは **ContosoVMVault** を使用します。
3. **[リソース グループ]** で、**contosoRG** という名前の既存のリソース グループを選択します。
4. **[場所]** で、この一連のチュートリアルで使用している Azure リージョン **[西ヨーロッパ]** を指定します。
5. ダッシュボードから資格情報コンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]** > **[作成]** の順に選択します。

   ![新しい資格情報コンテナーの作成](./media/tutorial-prepare-azure/new-vault-settings.png)

   新しい資格情報コンテナーは、**[ダッシュボード]** > **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ページに表示されます。

## <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップ

フェールオーバー後にストレージから作成された Azure VM は、このネットワークに参加します。

1. [Azure Portal](https://portal.azure.com) で、**[リソースの作成]** > **[ネットワーク]** > **[仮想ネットワーク]** の順に選択します。
2. デプロイ モデルとして **[リソース マネージャー]** をそのまま選択します。 [リソース マネージャー] が推奨されるデプロイ モデルです。 その後、次の手順に従います。

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[名前]** で、ネットワーク名を入力します。 Azure リソース グループ内で一意となる名前を使用してください。 **ContosoASRnet** という名前を使用します。

   b. **[リソース グループ]** で、既存のリソース グループ **contosoRG** を選択します。

   c. **[アドレス範囲]** で、ネットワーク アドレス範囲として **10.0.0.0/24** を入力します。

   d. このチュートリアルでは、サブネットは必要ありません。

   e. **[サブスクリプション]** で、ネットワークを作成するサブスクリプションを選択します。

   f. **[場所]** では **[西ヨーロッパ]** を選択します。 ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。

3. **[作成]**を選択します。

   ![仮想ネットワークの作成](media/tutorial-prepare-azure/create-network.png)

   仮想ネットワークの作成には数秒かかります。 作成が完了すると、Azure Portal ダッシュボードに表示されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure にディザスター リカバリーするためにオンプレミス VMware インフラストラクチャを準備する](tutorial-prepare-on-premises-vmware.md)
