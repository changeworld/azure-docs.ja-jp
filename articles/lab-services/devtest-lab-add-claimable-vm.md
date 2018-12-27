---
title: Azure DevTest Labs のラボでのクレーム可能 VM の作成と管理 | Microsoft Docs
description: Azure DevTest Labs でラボに要求可能な仮想マシンを追加する方法を説明します
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2018
ms.author: spelluru
ms.openlocfilehash: 3bfb674fa66f0701a099d237f4e760453c7b6a6e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232129"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Azure DevTest Labs でのクレーム可能 VM の作成と管理
要求可能な VM をラボに追加する方法は、"*ベース*" ([カスタム イメージ](devtest-lab-create-template.md)、[数式](devtest-lab-manage-formulas.md)、[Marketplace イメージ](devtest-lab-configure-marketplace-images.md)のいずれか) から[標準の VM を追加](devtest-lab-add-vm.md)する方法と似ています。 このチュートリアルでは、Azure Portal を使用して DevTest Labs でクレーム可能 VM をラボに追加する方法を説明し、ユーザーが VM を要求および解放する際に従うプロセスを示します。

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs で要求可能 VM をラボに追加する手順
1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で、要求可能 VM を作成するラボを選択します。  
1. ラボの **[概要]** ウィンドウで、**[+ 追加]** を選択します。  

    ![VM ボタンを追加する](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. **[ベースの選択]** 領域で、VM のベースを選択します。
1. **[仮想マシン]** ウィンドウで、**[仮想マシン名]** ボックスに新しい仮想マシンの名前を入力します。

    ![ラボの VM ウィンドウ](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. **[ユーザー名]** に、仮想マシンの管理者権限を付与するユーザー名を入力します。  
1. [シークレット ストア](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)に格納されているパスワードを使用する場合、**[Use a saved secret (保存されたシークレットを使用する)]** を選択し、シークレット (パスワード) に対応するキー値を指定します。 または、**[Type a value (値を入力してください)]** とラベル付けされているテキスト フィールドにパスワードを入力します。
1. **仮想マシンのディスクの種類**によって、ラボの仮想マシンで許可されるストレージ ディスクの種類が決まります。
1. **[仮想マシン サイズ]** を選択し、定義済みの項目の中から、作成する VM のプロセッサ コア、RAM サイズ、ハード ドライブ サイズを指定するものを選びます。
1. **[アーティファクト]** を選択し、アーティファクトの一覧から基本イメージに追加するアーティファクトを選択して構成します。 DevTest Labs やアーティファクトの構成に関する経験がない場合は、「[既存のアーティファクトの VM への追加](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)」セクションを参照し、終了してからここに戻ってください。
1. **[詳細設定]** を選択して VM のネットワーク オプションと有効期限のオプションを構成します。 **[Claim options (要求のオプション)]** で **[はい]** を選択して、コンピューターを要求可能にします。

  ![VM を要求可能にする。](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Azure Resource Manager テンプレートを表示またはコピーする場合は、「[Azure Resource Manager テンプレートの保存](devtest-lab-add-vm.md#save-azure-resource-manager-template)」セクションを参照し、終了してからここに戻ってください。
1. **[作成]** を選択して、指定した VM をラボに追加します。

   VM の作成状況は、最初に "**作成中**" と表示され、VM が起動した後は "**実行中**" と表示されます。

> [!NOTE]
>  [Azure Resource Manager テンプレート](devtest-lab-create-environment-from-arm.md)を使用してラボ VM をデプロイする場合、要求可能な VM を作成するには、プロパティ セクションで **allowClaim** プロパティを true に設定します。
>
>

## <a name="using-a-claimable-vm"></a>要求可能 VM の使用

ユーザーが "要求可能な仮想マシン" の一覧から任意の VM を要求するには、次のいずれかの手順を実行します。

* ラボの [概要] ウィンドウの下部にある [要求可能な仮想マシン] の一覧で、一覧にある VM のいずれかを右クリックし、**[マシンの要求]** を選択します。

 ![特定の要求可能 VM を要求する。](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* [概要] ウィンドウの上部で、**[要求]** を選択します。 要求可能 VM の一覧から仮想マシンがランダムに割り当てられます。

 ![任意の claimable VM を要求する。](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


ユーザーが VM を要求すると、その VM は、"マイ仮想マシン" の一覧に挿入され、他のユーザーが要求できなくなります。

## <a name="unclaim-a-vm"></a>VM の解放

ユーザーは、要求した VM の使用を終了し、それを他のユーザーが利用できるようにする場合、次のいずれかの手順を実行して、要求した VM を要求可能な仮想マシンの一覧に戻すことができます。

- [自分の仮想マシン] の一覧から、いずれかの VM を右クリックするかその省略記号 [...] を選択し、**[解放]** を選択します。

  ![VM の一覧で VM を解放する。](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- [自分の仮想マシン] の一覧で、VM を選択してその管理ウィンドウを開き、上部のメニュー バーにある **[解放]** を選択します。

  ![VM の管理ウィンドウで VM を解放する。](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

ユーザーは、VM を解放すると、その特定のラボ VM に対するアクセス許可がなくなります。

### <a name="transferring-the-data-disk"></a>データ ディスクの譲渡
クレーム可能 VM にデータ ディスクが接続されていて、ユーザーがその VM を解放した場合、そのデータ ディスクは VM で保持されたままになります。 その後、別のユーザーがその VM を要求するときに、その新しいユーザーは VM に加えてデータ ディスクも要求します。

これは "データ ディスクの譲渡" と呼ばれます。 このデータ ディスクは、新しいユーザーが管理するためにそのユーザーの **[個人用データ ディスク]** の一覧に表示されるようになります。

![データ ディスクを解放する。](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>次の手順
* 作成されたら、管理ウィンドウの **[接続]** を選択して VM に接続できます。
* [DevTest Labs Azure Resource Manager のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/Samples)を検索します。
