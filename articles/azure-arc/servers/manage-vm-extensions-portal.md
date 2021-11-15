---
title: Azure portal から VM 拡張機能を有効にする
description: この記事では、Azure portal からハイブリッド クラウド環境で実行されている Azure Arc 対応サーバーに仮想マシン拡張機能をデプロイする方法について説明します。
ms.date: 10/15/2021
ms.topic: conceptual
ms.openlocfilehash: b3bb6e6b6dcb70dc229dc01a02d04cf195c89700
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423909"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Azure portal から Azure VM 拡張機能を有効にする

この記事では、Azure portal を使用して、Azure Arc 対応サーバーでサポートされている Azure VM 拡張機能を Linux または Windows ハイブリッド マシンにデプロイ、更新、およびアンインストールする方法を示します。

> [!NOTE]
> Key Vault VM 拡張機能では、Azure portal からのデプロイはサポートされておらず、Azure CLI、Azure PowerShell、または Azure Resource Manager テンプレートを使用したデプロイのみがサポートされています。

> [!NOTE]
> Azure Arc 対応サーバーでは、Azure 仮想マシンへの VM 拡張機能のデプロイと管理はサポートされていません。 Azure VM については、次の [VM 拡張機能の概要](../../virtual-machines/extensions/overview.md)に関する記事をご覧ください。

## <a name="enable-extensions"></a>拡張機能を有効にする

VM 拡張機能は、Azure portal を介して、Azure Arc 対応サーバー マネージド マシンに適用できます。

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

2. ポータルで **[サーバー - Azure Arc]** に移動し、一覧からハイブリッド マシンを選択します。

3. **[拡張機能]** を選択し、 **[追加]** を選択します。 利用可能な拡張機能の一覧から目的の拡張機能を選択し、ウィザードの手順に従います。 この例では、Log Analytics VM 拡張機能をデプロイします。

    ![選択したマシンの VM 拡張機能を選択する](./media/manage-vm-extensions/add-vm-extensions.png)

    次の例では、Azure portal からの Log Analytics VM 拡張機能のインストールを示します。

    ![Log Analytics VM 拡張機能をインストールする](./media/manage-vm-extensions/mma-extension-config.png)

    インストールを完了するには、ワークスペース ID と主キーを指定する必要があります。 この情報の検索方法に慣れていない場合は、「[ワークスペース ID とキーを取得する](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)」を参照してください。

4. 表示される必要な情報を確認したら、 **[確認と作成]** を選択します。 デプロイの概要が表示され、デプロイの状態を確認できます。

>[!NOTE]
>複数の拡張機能をまとめてバッチ処理することができますが、順番にインストールされます。 最初の拡張機能のインストールが完了すると、次の拡張機能のインストールが試行されます。

## <a name="list-extensions-installed"></a>インストールされている拡張機能を一覧表示する

Azure Arc 対応サーバー上の VM 拡張機能の一覧を Azure portal から取得できます。 それらを表示するには、次の手順を実行します。

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

2. ポータルで **[サーバー - Azure Arc]** に移動し、一覧からハイブリッド マシンを選択します。

3. **[拡張機能]** を選択すると、インストールされている拡張機能の一覧が返されます。

    :::image type="content" source="media/manage-vm-extensions/list-vm-extensions.png" alt-text="選択されたマシンにデプロイされた VM 拡張機能を一覧表示します。" border="true":::

## <a name="upgrade-extensions"></a>拡張機能のアップグレード

サポートされている拡張機能の新しいバージョンがリリースされると、拡張機能をその最新リリースにアップグレードできます。 Azure Arc 対応サーバーでは、Azure Arc 対応サーバーに移動すると Azure portal にバナーが表示され、マシンにインストールされている 1 つ以上の拡張機能に対して利用可能なアップグレード プログラムが表示されます。 選択した Azure Arc 対応サーバーにインストールされている拡張機能の一覧を表示すると、**[更新プログラムが利用可能です]** というラベルの列が表示されます。 新しいバージョンの拡張機能がリリースされると、その拡張機能の **[更新プログラムが利用可能です]** の値に **[はい]** と表示されます。

>[!NOTE]
>現在、このエクスペリエンスのために Azure portal で「**更新**」という単語が使用されていますが、これは操作の動作を正確に表しているわけではありません。 拡張機能をアップグレードするには、コンピューターまたはサーバーに現在インストールされている拡張機能の新しいバージョンをインストールします。

拡張機能を最新バージョンにアップグレードしても、その拡張機能の構成には影響ありません。 アップグレードする拡張機能の構成情報を再指定する必要はありません。

:::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-status.png" alt-text="VM 拡張機能の更新状態を一覧表示します。" border="true":::

次の手順を実行して、Azure portal からアップグレード対象の拡張機能を 1 つ、または複数選択できます。

> [!NOTE]
> 現時点では、拡張機能は Azure portal からのみアップグレードできます。 現時点では、Azure CLI または Azure Resource Manager テンプレートを使用してこの操作を実行することはサポートされていません。

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

2. ポータルで **[サーバー - Azure Arc]** に移動し、一覧からハイブリッド マシンを選択します。

3. **[拡張機能]** を選択し、 **[更新プログラムが利用可能です]** 列の拡張機能の状態を確認します。

次の 3 つの方法のいずれかを使用して、1 つの拡張機能をアップグレードできます。

* インストールされている拡張機能の一覧から拡張機能を選択し、拡張機能のプロパティで **[更新]** オプションを選択します。

    :::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-from-extension.png" alt-text="選択した拡張機能から拡張機能をアップグレードします。" border="true":::

* インストールされている拡張機能の一覧から拡張機能を選択し、ページの上部から **[更新]** オプションを選択します。

* インストールされている拡張機能の一覧からアップグレードの対象となる 1 つ以上の拡張機能を選択し、**[更新]** オプションを選択します。

    :::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-selected.png" alt-text="選択した拡張機能を更新します。" border="true":::

## <a name="remove-extensions"></a>拡張機能を削除する

Azure portal から、1 つ以上の拡張機能を Azure Arc 対応サーバーから削除できます。 拡張機能を削除するには、次の手順のようにします。

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

2. ポータルで **[サーバー - Azure Arc]** に移動し、一覧からハイブリッド マシンを選択します。

3. **[拡張機能]** を選択し、インストールされている拡張機能の一覧から拡張機能を選択します。

4. **[アンインストール]** を選択し、確認を求めるメッセージが表示されたら、 **[はい]** を選択して続行します。

## <a name="next-steps"></a>次の手順

- [Azure CLI](manage-vm-extensions-cli.md)、[PowerShell](manage-vm-extensions-powershell.md)、または [Azure Resource Manager テンプレート](manage-vm-extensions-template.md)を使用して、VM 拡張機能をデプロイ、管理、および削除できます。

- トラブルシューティングに関する情報は、[VM 拡張機能のトラブルシューティング ガイド](troubleshoot-vm-extensions.md)に記載されています。
