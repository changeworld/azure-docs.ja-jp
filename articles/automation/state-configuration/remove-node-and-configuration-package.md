---
title: Automation State Configuration から DSC とノードを削除する
description: この記事では、割り当てられた Azure Automation State Configuration (DSC) 構成ドキュメントを削除して、マネージド ノードを登録解除する方法について説明します。
titleSuffix: Azure Automation
services: automation
ms.subservice: dsc
ms.date: 04/16/2021
ms.topic: how-to
ms.openlocfilehash: 2db345705933f89a2ce119e65182f88a52f13dc8
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109716367"
---
# <a name="how-to-remove-a-configuration-and-node-from-automation-state-configuration"></a>Automation State Configuration から構成とノードを削除する方法

この記事では、Automation State Configuration によって管理されているノードを登録解除し、マネージド ノードから PowerShell Desired State Configuration (DSC) 構成を安全に削除する方法について説明します。 Windows と Linux のどちらのノードの場合でも、[ノードを登録解除](#unregister-a-node)して[構成を削除](#delete-a-configuration-from-the-azure-portal)する必要があります。 Linux ノードの場合のみ、必要に応じてノードから DSC パッケージを削除することもできます。 「[Linux ノードから DSC パッケージを削除する](#remove-the-dsc-package-from-a-linux-node)」を参照してください。

## <a name="unregister-a-node"></a>ノードを登録解除する

State Configuration (DSC) でノードを管理する必要がなくなった場合は、次の手順に従って、Azure portal または Azure PowerShell からノードを登録解除できます。

サービスからノードを登録解除しても、Local Configuration Manager 設定のみが設定されるため、ノードはサービスに接続しなくなります。 これは、ノードに現在適用されている構成には影響しません。また、関連ファイルはノードに配置されたままになります。 必要に応じて、これらのファイルをクリーンアップできます。 「[構成を削除する](#delete-a-configuration)」を参照してください。

### <a name="unregister-in-the-azure-portal"></a>Azure portal で登録解除する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **Automation アカウント** を検索して選択します。
1. **[Automation アカウント]** ページで、一覧からお使いの Automation アカウントを選択します。
1. ご自分の Automation アカウントから、 **[構成管理]** の **[State configuration (DSC)]** を選択します。
1. **[状態の構成 (DSC)]** ページで、 **[ノード]** タブをクリックします。
1. **[ノード]** タブで、登録解除するノードの名前を選択します。
1. そのノードのペインで、 **[登録解除]** をクリックします。

   :::image type="content" source="./media/remove-node-and-configuration-package/unregister-node.png" alt-text="[登録解除] ボタンが強調表示されているノードの詳細ページのスクリーンショット。" lightbox="./media/remove-node-and-configuration-package/unregister-node.png":::

### <a name="unregister-using-powershell"></a>PowerShell を使用して登録解除する

PowerShell コマンドレット [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) を使用して、ノードを登録解除することもできます。

>[!NOTE]
>組織で非推奨の AzureRM モジュールをまだ使用している場合は、[Unregister-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) を使用できます。

## <a name="delete-a-configuration"></a>構成を削除する

1 つ以上のノードに割り当てられている、インポートした DSC 構成ドキュメント (Managed Object Format (MOF) つまり .mof ファイル) を削除する準備ができたら、次の手順に従います。

### <a name="delete-a-configuration-from-the-azure-portal"></a>Azure portal から構成を削除する

Windows と Linux のノードの構成は、どちらも Azure portal から削除できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **Automation アカウント** を検索して選択します。
1. **[Automation アカウント]** ページで、一覧からお使いの Automation アカウントを選択します。
1. ご自分の Automation アカウントから、 **[構成管理]** の **[State configuration (DSC)]** を選択します。
1. **[State configuration (DSC)]** ページで、 **[構成]** タブをクリックし、削除する構成の名前を選択します。

   :::image type="content" source="./media/remove-node-and-configuration-package/configurations-tab.png" alt-text="[構成] タブのスクリーンショット。" lightbox="./media/remove-node-and-configuration-package/configurations-tab.png":::

1. 構成の詳細ページで、 **[削除]** をクリックして構成を削除します。

   :::image type="content" source="./media/remove-node-and-configuration-package/delete-extension.png" alt-text="拡張機能の削除のスクリーンショット。" lightbox="./media/remove-node-and-configuration-package/delete-extension.png":::

## <a name="manually-delete-a-configuration-file-from-a-node"></a>ノードから構成ファイルを手動で削除する

Azure portal を使用しない場合は、次のように .mof 構成ファイルを手動で削除できます。

### <a name="delete-a-windows-configuration-using-powershell"></a>PowerShell を使用して Windows 構成を削除する

インポートされた DSC 構成ドキュメント (.mof) を削除するには、[Remove-DscConfigurationDocument](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) コマンドレットを使用します。

### <a name="delete-a-linux-configuration"></a>Linux 構成を削除する

構成ファイルは /etc/opt/omi/conf/dsc/configuration/ に格納されています。 ノードの構成を削除するには、このディレクトリ内の .mof ファイルを削除します。

## <a name="remove-the-dsc-package-from-a-linux-node"></a>Linux ノードから DSC パッケージを削除する

この手順は省略可能です。 State Configuration (DSC) から Linux ノードを登録解除しても、マシンから DSC と OMI のパッケージは削除されません。 次のコマンドを使用して、パッケージとともにすべてのログと関連データも削除します。

パッケージ名とその他の関連する詳細については、GitHub リポジトリの [Linux 用 PowerShell Desired State Configuration](https://github.com/Microsoft/PowerShell-DSC-for-Linux) を参照してください。

### <a name="rpm-based-systems"></a>RPM ベースのシステム

```bash
RPM -e <package name>
``` 

### <a name="dpkg-based-systems"></a>dpkg ベースのシステム

```bash
dpkg -P <package name>
```

 ## <a name="next-steps"></a>次の手順

- ノードを再登録する場合、または新しいノードを登録する場合は、「[State Configuration によって管理される VM を登録する](../tutorial-configure-servers-desired-state.md#register-a-vm-to-be-managed-by-state-configuration)」を参照してください。

- 構成を戻して再コンパイルする場合は、「[Azure Automation State Configuration で DSC 構成をコンパイルする](../automation-dsc-compile.md)」を参照してください。