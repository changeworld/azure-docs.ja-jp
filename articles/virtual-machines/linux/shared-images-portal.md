---
title: ポータルを使用して共有 Azure Linux VM イメージを作成する
description: Azure portal を使用して、Linux 仮想マシン イメージを作成して共有する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/21/2021
ms.author: cynthn
ms.openlocfilehash: 515d836e9b36a1fb20a712ab30a561ff68e3f715
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424668"
---
# <a name="create-an-azure-compute-gallery-using-the-portal"></a>ポータルを使用して Azure Compute Gallery を作成する

**適用対象:** :heavy_check_mark: Linux VMs :heavy_check_mark: フレキシブル スケール セット **適用対象:** :heavy_check_mark: :heavy_check_mark: ユニフォーム スケール セット 

[Azure Compute Gallery](../shared-image-galleries.md) により、組織全体でのカスタム イメージの共有が簡素化されます。 カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、デプロイ タスク (アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成など) のブートストラップを実行するために使用できます。 

Azure Compute Gallery を使用すると、組織内、リージョン内またはリージョン間、Azure AD テナント内で、他のユーザーとご自身のカスタム VM イメージを共有できます。 どのイメージを共有するか、どのリージョンでそのイメージを使用できるようにするか、および、だれと共有するかを選択することができます。 複数のギャラリーを作成することで、イメージを論理的にグループ化できます。 

ギャラリーは最上位リソースで、完全な Azure ロールベースのアクセス制御 (Azure RBAC) が可能です。 イメージのバージョン管理もできるため、Azure リージョンの別のセットに各イメージのバージョンをレプリケートできます。 ギャラリーは、マネージド イメージでのみ機能します。

Azure Compute Gallery 機能には、複数のリソースの種類があります。 それらを、この記事の中で使用または作成していきます。


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>


## <a name="before-you-begin"></a>開始する前に

この記事の例を完了するには、一般化された VM の既存のマネージド イメージか、特殊化された VM のスナップショットが必要です。 お持ちでない場合は、「[チュートリアル: Azure PowerShell を使用して Azure VM のカスタム イメージを作成する](tutorial-custom-images.md)」に従って、マネージド イメージを作成するか、特殊化された VM の場合は[スナップショットを作成](../windows/snapshot-copy-managed-disk.md)してください。 マネージド イメージの場合もスナップショットの場合も、データ ディスクのサイズが 1 TB を超えることはできません。

この記事の作業に出現するリソース グループと VM の名前は適宜置き換えてください。

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>VM の作成 

これで、1 つ以上の新しい VM を作成できるようになりました。 この例では、"*米国東部*" のデータセンターにある *myResourceGroup* に、*myVMfromImage* という名前の VM を作成します。

1. イメージ定義に移動します。 リソース フィルターを使用すると、使用可能なすべてのイメージ定義を表示できます。
1. イメージ定義のページで、ページの上部にあるメニューから **[VM の作成]** を選択します。
1. **[リソース グループ]** で、 **[新規作成]** を選択し、名前として「*myResourceGroup*」と入力します。
1. **[仮想マシン名]** に、「*myVM*」と入力します。
1. **[リージョン]** で、 *[米国東部]* を選択します。
1. **[可用性オプション]** で、既定の *[インフラストラクチャ冗長は必要ありません]* のままにします。
1. イメージ定義のページから操作を開始していれば、 **[イメージ]** の値に `latest` イメージ バージョンが自動的に入力されます。
1. **[サイズ]** では、利用可能なサイズの一覧から VM サイズを選択し、 **[選択]** を選択します。
1. ソース VM が一般化されている場合は、 **[管理者アカウント]** で、 **[ユーザー名]** と **[SSH 公開キー]** を入力します。 ソース VM が特殊化されている場合は、ソース VM からの情報が使用されるため、これらのオプションは灰色で表示されます。
1. VM へのリモート アクセスを許可する場合、 **[パブリック受信ポート]** で、 **[選択したポートを許可する]** を選択し、ドロップダウンから **[SSH (22)]** を選択します。 VM へのリモート アクセスを許可しない場合、 **[パブリック受信ポート]** で、 **[なし]** を選択したままにします。
1. イメージが Red Hat または SLES に基づいている場合を除き、[ライセンス] で **[その他の]** を選択します。
1. 完了したら、ページの下部にある **[Review + create]\(確認と作成\)** ボタンを選択します。
1. VM が検証に合格したら、ページの下部にある **[作成]** を選択し、デプロイを開始します。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、仮想マシン、およびすべての関連リソースを削除できます。 これを行うには、仮想マシンのリソース グループを選択し、 **[削除]** を選択して、削除するリソース グループの名前を確認します。

個々のリソースを削除する場合は、逆の順序でそれらを削除する必要があります。 たとえば、イメージ定義を削除するには、そのイメージから作成されたすべてのイメージ バージョンを削除する必要があります。

## <a name="next-steps"></a>次のステップ

Azure Compute Gallery リソースは、テンプレートを使用して作成することもできます。 いくつかの Azure クイック スタート テンプレートが用意されています。 

- [Azure Compute Gallery の作成](https://azure.microsoft.com/resources/templates/sig-create/)
- [Azure Compute Gallery でのイメージ定義の作成](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Azure Compute Gallery でのイメージ バージョンの作成](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

Azure Compute Gallery の詳細については、[概要](../shared-image-galleries.md)のページをご覧ください。 問題が生じた場合は、「[ギャラリーのトラブルシューティング](../troubleshooting-shared-images.md)」を参照してください。