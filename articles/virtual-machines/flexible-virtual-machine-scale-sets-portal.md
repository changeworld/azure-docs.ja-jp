---
title: Azure portal を使用してフレキシブル スケール セットに仮想マシンを作成する
description: Azure portal のフレキシブル オーケストレーション モードで仮想マシン スケール セットを作成する方法について説明します。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: cef23894759b0b2dca7098ef1c430548d1720406
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868411"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>プレビュー: Azure portal を使用してフレキシブル スケール セットに仮想マシンを作成する

**適用対象:** :heavy_check_mark: フレキシブル スケール セット

この記事では、Azure portal を使用して、フレキシブル オーケストレーション モードで仮想マシン スケール セットを作成する方法について説明します。 フレキシブル スケール セットの詳細については、[仮想マシン スケール セットのフレキシブル オーケストレーション モード](flexible-virtual-machine-scale-sets.md)に関するページを参照してください。 


> [!IMPORTANT]
> フレキシブル オーケストレーション モードの仮想マシン スケール セットは現在、パブリック プレビュー段階です。 後述するパブリック プレビュー機能を使用するためには、オプトイン手順が必要です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


> [!CAUTION]
> オーケストレーション モードは、スケール セットを作成するときに定義され、後で変更または更新することはできません。


## <a name="register-for-flexible-orchestration-mode"></a>フレキシブル オーケストレーション モードの登録

フレキシブル オーケストレーション モードで仮想マシン スケール セットをデプロイするには、最初にプレビュー機能に対してサブスクリプションを登録しておく必要があります。 機能の登録には最大で 15 分かかる場合があります。

スケール セット プレビューのフレキシブル オーケストレーション モードでは、次の手順でリンクされている "*プレビュー*" の Azure portal を使用します。 

1. Azure portal (https://preview.portal.azure.com ) にログインします。
1. **[サブスクリプション]** に移動します。
1. サブスクリプションの名前を選択して、フレキシブル オーケストレーション モードでスケール セットを作成するサブスクリプションの詳細ページに移動します。
1. **[設定]** の下のメニューで、 **[プレビュー機能]** を選択します。
1. 有効にするオーケストレーターの 4 つの機能 (*VMOrchestratorSingleFD*、*VMOrchestratorMultiFD*、*VMScaleSetFlexPreview*、*SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview*) を選択します。
1. **[登録]** を選択します。

サブスクリプションに対して機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。 

1. **[設定]** の下のメニューで、 **[リソース プロバイダー]** を選択します。
1. [`Microsoft.compute`] を選択します。
1. **[再登録]** を選択します。


## <a name="get-started-with-flexible-orchestration-mode"></a>フレキシブル オーケストレーション モードの開始

### <a name="create-a-virtual-machine-scale-set-in-flexible-orchestration-mode-through-the-azure-portal"></a>Azure portal を通じてフレキシブル オーケストレーション モードで仮想マシン スケール セットを作成します。

スケール セット プレビューのフレキシブル オーケストレーション モードでは、次の手順でリンクされている "*プレビュー*" の Azure portal を使用します。 

1. Azure portal (https://preview.portal.azure.com ) にログインします。
1. 検索バーで、 **[仮想マシン スケール セット]** を検索して選択します。
1. **[仮想マシン スケール セット]** ページで **[作成]** を選択します。
1. **[仮想マシン スケール セットの作成]** ページで、 **[オーケストレーション]** セクションを表示します。
1. **[オーケストレーション モード]** で **[Flexible]\(フレキシブル\)** オプションを選択します。
1. **[障害ドメイン数]** を設定します。
1. スケール セットの作成を完了します。 スケール セットを作成する方法の詳細については、[Azure portal でのスケール セットの作成](../virtual-machine-scale-sets/quick-create-portal.md#create-virtual-machine-scale-set)に関するページを参照してください。


### <a name="next-add-a-virtual-machine-to-the-scale-set-in-flexible-orchestration-mode"></a>次に、フレキシブル オーケストレーション モードのスケール セットに仮想マシンを追加します。

1. 検索バーで **[仮想マシン]** を検索して選択します。
1. **[仮想マシン]** ページで **[追加]** を選択します。
1. **[基本]** タブで **[インスタンスの詳細]** セクションを表示します。
1. **[可用性オプション]** でスケール セットを選択して、フレキシブル オーケストレーション モードのスケール セットに VM を追加します。 同じリージョン、ゾーン、リソース グループ内のスケール セットに仮想マシンを追加できます。
1. **[ネットワーク]** タブにアクセスし、送信接続を明示的に定義します。

    > [!IMPORTANT]
    > フレキシブル オーケストレーションを使用した仮想マシン スケール セットには、明示的に定義された送信接続が必要です。 詳細については、[明示的な送信ネットワーク接続](flexible-virtual-machine-scale-sets.md#explicit-network-outbound-connectivity-required)に関するページを参照してください。

1. 仮想マシンの作成を完了します。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure CLI でフレキシブル スケールを作成する方法について説明します。](flexible-virtual-machine-scale-sets-cli.md)