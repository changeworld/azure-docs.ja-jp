---
title: "仮想マシンの拡張機能とその機能 | Microsoft Docs"
description: "Azure 仮想マシンに使用できる拡張機能について、提供または改善される内容ごとにまとめて説明します。"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/30/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 7bd48e2ef9177369190494c38bfdcf1bb99e61ea


---
# <a name="about-virtual-machine-extensions-and-features"></a>仮想マシンの拡張機能とその機能について
## <a name="azure-vm-extensions"></a>Azure VM 拡張機能
Azure 仮想マシン拡張機能は、Azure Virtual Machines でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 たとえば、仮想マシンでソフトウェアのインストール、ウイルス対策保護、または Docker 構成が必要な場合、VM 拡張機能を使用して、これらのタスクを実行できます。 Azure VM 拡張機能は、Azure CLI、PowerShell、Resource Manager テンプレート、および Azure Portal を使用して実行できます。 拡張機能は、新しい仮想マシン デプロイにバンドルすることも、既存の任意のシステムに対して実行することもできます。

このドキュメントでは、Azure 仮想マシン拡張機能の前提条件を示し、使用できる VM 拡張機能を検出する方法についてのガイダンスを提供します。 

## <a name="azure-vm-agent"></a>Azure VM エージェント
Azure VM エージェントは、Azure 仮想マシンと Azure ファブリック コントローラーとの相互動作を管理します。 VM エージェントは、Azure Virtual Machines のデプロイと管理の多くの機能面を担当します (VM 拡張機能の実行など)。 Azure VM エージェントは、Azure ギャラリー イメージにプレインストールされており、サポートされているオペレーティング システムにインストールすることができます。 

サポートされているオペレーティング システムおよびインストール手順については、「 [仮想マシンのエージェントおよび拡張機能について](virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

## <a name="discover-vm-extensions"></a>VM 拡張機能の検出
Azure Virtual Machines と共に、多くのさまざまな VM 拡張機能を使用できます。 完全な一覧を表示するには、Azure CLI で次のコマンドを実行します。場所は、実際の場所に置き換えます。

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>一般的な VM 拡張機能
| 拡張機能の名前 | Description | 詳細情報 |
| --- | --- | --- |
| Windows でのカスタムのスクリプト拡張機能 |Azure 仮想マシンに対してスクリプトを実行します |[Windows でのカスタムのスクリプト拡張機能](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Windows での DSC 拡張機能 |PowerShell DSC (必要な状態の構成) 拡張機能。 |[Docker VM 拡張機能](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure 診断拡張機能 |Azure 診断を管理します |[Azure 診断拡張機能](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |




<!--HONumber=Nov16_HO3-->


