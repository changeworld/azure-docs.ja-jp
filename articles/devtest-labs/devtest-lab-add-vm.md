---
title: 仮想マシンを作成してラボに追加する
description: Azure portal を使用して、Azure DevTest Labs のラボに仮想マシンを追加する方法について説明します。 ベースとしてカスタム イメージまたは数式のいずれかを選択できます。
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 326d8923b27abff2ee480f6b981392a311be4f30
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235290"
---
# <a name="create-and-add-virtual-machines-to-a-lab-in-azure-devtest-labs"></a>仮想マシンを作成して Azure DevTest Labs のラボに追加する

この記事では、Azure portal を使用して、Azure 仮想マシン (VM) を作成し、既存の DevTest Labs のラボに追加する方法の手順を説明します。

## <a name="create-and-add-virtual-machines"></a>仮想マシンを作成して追加する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **DevTest Labs** のラボに移動します。

1. **[概要]** ページで、 **[+ 追加]** を選びます。

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-add-vm.png" alt-text="[追加] ボタンを示すラボの [概要] ページ。":::

1. **[ベースの選択]** ページで、VM のマーケットプレイス イメージを選択します。 このガイドでは **Windows 11 Pro** を使います。 別のイメージを使用すると、一部のオプションが異なる場合があります。

1. **[基本設定]** タブで次の情報を指定します。

    |プロパティ |説明 |
    |---|---|
    |仮想マシン名&nbsp;&nbsp;| テキスト ボックスには自動生成された一意の名前が自動的に入力されます。 この名前は、電子メール アドレス内のユーザー名に対応しており、その後に一意の 3 桁の数字が続きます。 そのままにするか、任意の一意の名前を入力します。|
    |[ユーザー名]| テキスト ボックスには自動生成された一意の名前が自動的に入力されます。 この名前は、電子メール アドレス内のユーザー名に対応しています。 そのままにするか、任意の名前を入力します。 このユーザーには、仮想マシンの **管理者** 権限が付与されます。|
    |保存されているシークレットを使用する| このチュートリアルでは、チェック ボックスをオフのままにします。 最初に Azure Key Vault にシークレットを保存してから、ここで使用できます。 詳細については、「[Azure DevTest Labs でキー コンテナーにシークレットを格納する](devtest-lab-store-secrets-in-key-vault.md)」を参照してください。 保存したシークレットを使用する場合は、ボックスをオンにし、 **[シークレット]** ドロップダウン リストからシークレットを選択します。|
    |Password|8 から 123 文字の長さのパスワードを入力します。|
    |既定のパスワードとして保存する| チェックボックスをオンにして、ラボに関連付けられている Azure Key Vault にパスワードを保存します。|
    |仮想マシンのサイズ| 既定値のままにするか、 **[サイズの変更]** を選択して別の物理コンポーネントを選択します。 このチュートリアルでは **[Standard_B2]** を使用します。|
    |OS ディスクの種類|既定値のままにするか、ドロップダウン リストから別のオプションを選択します。|
    |Artifacts| **[成果物を追加または削除]** を選択します。 基本イメージに追加する成果物を選択して構成します。 各ラボには、パブリック DevTest ラボ成果物リポジトリの成果物と、独自の成果物リポジトリに作成または追加した成果物が含まれます。 拡張手順については、後の「[インストールの間に成果物を追加する](#add-artifacts-during-installation)」をご覧ください。|

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-basic-settings.png" alt-text="仮想マシンの基本設定ページ。":::

1. **[詳細設定]** タブを選択し、次の情報を指定します。

    |プロパティ |説明 |
    |---|---|
    |仮想ネットワーク| そのままにするか、ドロップダウン リストから別のネットワークを選択します。|
    |サブネット&nbsp;セレクター| そのままにするか、ドロップダウン リストから別のサブネットを選択します。|
    |IP アドレス| このチュートリアルでは、既定値の **[共有]** のままにします。|
    |有効期限| 有効期限を設定しないままにしておくか、カレンダー アイコンを選択して有効期限を設定します。|
    |このマシンを要求可能にする| ラボ ユーザーが VM をクレームできるようにするには、 **[はい]** を選択します。 マシンをクレーム可能として設定すると、作成時にその所有権が割り当てられません。 このチュートリアルでは **[はい]** を選びます。|
    |インスタンスの数| このチュートリアルでは、「**2**」と入力します。 作成する仮想マシン インスタンスの数。|
    |Automation | 省略可能。 **[ARM テンプレートの表示]** を選択すると、新しいページにテンプレートが開きます。 テンプレートをコピーして保存すると、後で同じ仮想マシンを作成できます。 保存した Azure Resource Manager テンプレートで、[Azure PowerShell を使用して新しい VM をデプロイ](../azure-resource-manager/templates/overview.md)できます。|

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-advanced-settings.png" alt-text="仮想マシンの [詳細設定] ページ。":::

1. **[基本設定]** タブに戻り、 **[作成]** を選択します。

1. **[DevTest Lab]** ページで、 **[個人用ラボ]** の **[要求可能な仮想マシン]** を選びます。

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-creation-status.png" alt-text="ラボ VM 作成の状態ページ。":::

1. 数分たって仮想マシンが表示されない場合は、 **[最新の情報に更新]** を選択します。 インストールにかかる時間は、選択したハードウェア、基本イメージ、成果物によって異なります。 このチュートリアルで使用した構成のインストールは、約 25 分でした。

## <a name="add-artifacts-during-installation"></a>インストールの間に成果物を追加する

以下の手順は、前のセクションを拡張するものです。 この手順は、 **[基本設定]** タブで **[成果物を追加または削除]** を選んだ後から始まります。成果物について詳しくは、[DevTest Labs で使用するための独自の成果物を作成する方法](devtest-lab-artifact-author.md)に関する記事をご覧ください。

1. **[成果物の追加]** ページで成果物を識別し、 **[>]** ("より大きい" 記号) を選びます。 **[OK]** をクリックします。

   :::image type="content" source="./media/devtest-lab-add-vm/portal-add-artifact-during.png" alt-text="仮想マシンに成果物を追加します。":::

1. 別の成果物を選択します: **PowerShell モジュールをインストールします**。 この成果物には、追加情報 (具体的には PowerShell モジュールの名前) が必要です。 「**Az**」と入力して、 **[OK]** を選びます。

1. VM に必要なアーティファクトを引き続き追加します。

1. 選んだ成果物の 1 つの **[...]** (省略記号) を選び、インストール順序を変更する機能など、さまざまなオプションに注意します。

1. 成果物の追加が済んだら、 **[OK]** を選んで **[基本設定]** タブに戻ります。

## <a name="add-artifacts-after-installation"></a>インストール後に成果物を追加する

VM を作成した後で成果物を追加することもできます。 

1. **[DevTest Lab]** ページで、 **[個人用ラボ]** の **[すべてのリソース]** を選びます。 **[すべてのリソース]** を選ぶと、クレームされた VM とクレームされない VM の両方が一覧表示されます。

    :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-all-resources.png" alt-text="すべてのリソースの状態が示されているラボ。":::

1. **[状態]** に **[使用可能]** と表示されたら、自分の VM を選びます。

1. **仮想マシン** のページで **[開始]** を選んで VM を起動します。

1. ページに **[実行中]** と表示されてしばらくしたら、 **[操作]** の下にある **[成果物]** を選びます。

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-overview.png" alt-text="[開始] ボタンが示されているラボ VM の概要。":::

1. **[成果物の適用]** を選んで、 **[成果物の追加]** ページを開きます。

1. ここからの手順は、上で示した「[インストールの間に成果物を追加する](#add-artifacts-during-installation)」の手順と基本的に同じです。

## <a name="next-steps"></a>次のステップ

* VM が作成されたら、VM のウィンドウで **[接続]** を選択して VM に接続できます。
* [DevTest Labs VM のカスタム アーティファクトの作成](devtest-lab-artifact-author.md)方法を学習します。
* [DevTest Labs Azure Resource Manager のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)を検索します。
