---
title: Azure Automation State Configuration で複合リソースを使用して DSC 構成を作成する
description: Azure Automation State Configuration で複合リソースを使用して構成を作成する方法について学習します。
keywords: powershell dsc, Desired State Configuration, powershell dsc azure, 複合リソース
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 1840f4049f8450295e179a89b472d7710c5f61a0
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993774"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>Azure Automation State Configuration で複合リソースを使用して DSC 構成を作成する

複数の Desired State Configuration (DSC) でリソースを管理する必要がある場合に最適な方法は、[複合リソース](/powershell/scripting/dsc/resources/authoringresourcecomposite)を使用することです。 複合リソースは、別の構成内の DSC リソースとして使用されている、入れ子になったパラメーター化された構成です。 複合リソースを使用すると、基になる複合リソースを個別に管理および構築しながら、複雑な構成を作成することができます。

Azure Automation により、[複合リソースのインポートとコンパイル](automation-dsc-compile.md)が可能になります。 Automation アカウントに複合リソースをインポートしたら、Azure portal の **State Configuration (DSC)** 機能から Azure Automation の State Configuration を使用できます。

## <a name="composing-a-configuration-from-composite-resources"></a>複合リソースから構成を作成する

Azure portal で複合リソースから作成された構成を割り当てるには、まず構成を作成する必要があります。 構成には、[State Configuration (DSC)] ページの **[構成]** または **[コンパイル済み構成]** のいずれかのタブで **[構成の作成]** を使用します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. Automation アカウント ページで、 **[構成管理]** の **[状態の構成 (DSC)]** を選択します。
1. [State Configuration (DSC)] ページで、 **[構成]** または **[コンパイル済み構成]** のいずれかのタブをクリックしてから、ページ上部のメニューで **[構成の作成]** をクリックします。
1. **[基本]** のステップでは、新しい構成名 (必須) を指定して、新しい構成に含める各複合リソースの行の任意の場所をクリックし、 **[次へ]** をクリックするか、 **[ソース コード]** のステップをクリックします。 次の手順では、複合リソースの `PSExecutionPolicy` と `RenameAndDomainJoin` を選択しました。
   ![[構成の作成] ページの [基本] ステップのスクリーンショット](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. **[ソース コード]** ステップでは、選択した複合リソースの作成された構成がどのようになっているかが示されます。 すべてのパラメーターのマージと、それらが複合リソースにどのように渡されるかを確認することができます。 新しいソース コードの確認が終ったら、 **[次へ]** をクリックするか、 **[パラメーター]** ステップをクリックします。
   ![[構成の作成] ページの [ソース コード] ステップのスクリーンショット](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. **[パラメーター]** ステップでは、値を指定できるように、各複合リソースのパラメーターが公開されます。 パラメーターに説明がある場合は、パラメーター フィールドの横に表示されます。 パラメーターが `PSCredential` の種類の場合、ドロップダウンには現在の Automation アカウントの **Credential** オブジェクトの一覧が表示されます。 **[+ 資格情報の追加]** オプションも使用できます。 すべての必須パラメーターを指定したら、 **[Save and compile]\(保存してコンパイル\)** をクリックします。
   ![[構成の作成] ページの [パラメーター] ステップのスクリーンショット](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

新しい構成が保存されると、その構成がコンパイルのために送信されます。 コンパイル ジョブの状態は、他のインポートされた構成と同じように表示できます。 詳細については、「[コンパイル ジョブを表示する](automation-dsc-getting-started.md#view-a-compilation-job)」を参照してください。

コンパイルが正常に完了すると、新しい構成が **[コンパイル済み構成]** タブに表示されます。その後、「[別のノード構成にノードを再割り当てする](automation-dsc-getting-started.md#reassign-a-node-to-a-different-node-configuration)」の手順を使って、その構成をマネージド ノードに割り当てることができます。

## <a name="next-steps"></a>次のステップ

- 使用を開始するには、「[Azure Automation State Configuration の使用開始](automation-dsc-getting-started.md)」をご覧ください。
- ノードをオンボードにする方法は、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/azurerm.automation/#automation)に関するページをご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
