---
title: 仮想マシンの拡張機能とその機能 | Microsoft Docs
description: Azure 仮想マシンに使用できる拡張機能について、提供または改善される内容ごとにまとめて説明します。
services: virtual-machines-linux
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: nepeters

---
# <a name="about-virtual-machine-extensions-and-features"></a>仮想マシンの拡張機能とその機能について
## <a name="azure-vm-extensions"></a>Azure VM 拡張機能
Azure 仮想マシン拡張機能は、Azure Virtual Machines でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 たとえば、仮想マシンでソフトウェアのインストール、ウイルス対策保護、または Docker 構成が必要な場合、VM 拡張機能を使用して、これらのタスクを実行できます。 Azure VM 拡張機能は、Azure CLI、PowerShell、Resource Manager テンプレート、および Azure Portal を使用して実行できます。 拡張機能は、新しい仮想マシン デプロイにバンドルすることも、既存の任意のシステムに対して実行することもできます。

このドキュメントでは、Azure 仮想マシン拡張機能の前提条件を示し、使用できる VM 拡張機能を検出する方法についてのガイダンスを提供します。 

## <a name="azure-vm-agent"></a>Azure VM エージェント
Azure VM エージェントは、Azure 仮想マシンと Azure ファブリック コントローラーとの相互動作を管理します。 VM エージェントは、Azure Virtual Machines のデプロイと管理の多くの機能面を担当します (VM 拡張機能の実行など)。 Azure VM エージェントは、Azure ギャラリー イメージにプレインストールされており、サポートされているオペレーティング システムにインストールすることができます。 

サポートされているオペレーティング システムおよびインストール手順については、「 [Azure Linux エージェント ユーザー ガイド](virtual-machines-linux-agent-user-guide.md)」を参照してください。

## <a name="discover-vm-extensions"></a>VM 拡張機能の検出
Azure Virtual Machines と共に、多くのさまざまな VM 拡張機能を使用できます。 完全な一覧を表示するには、Azure CLI で次のコマンドを実行します。場所は、実際の場所に置き換えます。

```none
azure vm extension-image list westus
```

<br />

## <a name="common-vm-extensions"></a>一般的な VM 拡張機能
| 拡張機能の名前 | Description | 詳細情報 |
| --- | --- | --- |
| Linux 用カスタム スクリプト拡張機能 |Azure 仮想マシンに対してスクリプトを実行します |[Linux 用カスタム スクリプト拡張機能](virtual-machines-linux-extensions-customscript.md) |
| Docker 拡張機能 |リモートの Docker コマンドをサポートする Docker デーモンをインストールします。 |[Docker VM 拡張機能](virtual-machines-linux-dockerextension.md) |
| VM アクセス拡張機能 |Azure 仮想マシンへのアクセスを回復します |[VM アクセス拡張機能](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure 診断拡張機能 |Azure 診断を管理します |[Azure 診断拡張機能](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |

<!--HONumber=Oct16_HO2-->


