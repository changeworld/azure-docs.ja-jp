---
title: Azure のための HTTPS 経由での Windows リモート管理 | Azure Marketplace
description: PowerShell を使用してリモートで管理できるように、Azure でホストされる Windows ベースの VM を構成する方法について説明します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: 7c799c4a56ee5fc2074e4d644bdbcbc6d2b1ca5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288752"
---
# <a name="windows-remote-management-over-https"></a>HTTPS 経由での Windows リモート管理

このセクションでは、PowerShell を使用してリモートで管理およびデプロイできるように、Azure でホストされる Windows ベースの VM を構成する方法について説明します。  PowerShell のリモート操作を有効にするには、ターゲット VM が Windows リモート管理 (WinRM) HTTPS エンドポイントを公開する必要があります。  PowerShell のリモート操作について詳しくは、「[Running Remote Commands](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands)」(リモート コマンドの実行) をご覧ください。  WinRM について詳しくは、「[Windows Remote Management](https://docs.microsoft.com/windows/desktop/WinRM/portal)」(Windows リモート管理) をご覧ください。

Azure の "クラシック" アプローチ (Azure Service Manager Portal または非推奨の [Azure Service Management API](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))) のいずれかを使用して VM を作成した場合は、WinRM エンドポイントが自動的に構成されます。  一方、次の "最新の" Azure アプローチのいずれかを使用して VM を作成した場合は、VM は HTTPS 経由の WinRM 用に構成 "*されません*"。

- [Azure portal](https://portal.azure.com/) を使用。通常は「[Azure と互換性のある VHD の作成](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)」セクションで説明されているように承認済みのベースから
- [Azure Resource Manager テンプレートを使用](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Azure PowerShell または Azure CLI コマンド シェルを使用。  たとえば、「[クイック スタート:PowerShell を使用して Azure に Windows 仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)」および「[クイック スタート:Azure CLI を使用して Linux 仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)」をご覧ください。

この WinRM エンドポイントは、「[VM イメージを認定する](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm)」で説明されているように、VM のオンボード用に認定資格ツール キットを実行するためにも必要です。

これに対し、Linux VM は通常、SSH コンソールから [Azure CLI](https://docs.microsoft.com/cli/azure) または Linux コマンドを使用してリモート管理されます。  Azure では、[Linux VM でスクリプトを実行](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm)するための複数の代替手段も提供されています。  さらに複雑なシナリオでは、いくつかの自動化および統合ソリューションを Windows または Linux ベースの VM に対して使用できます。


## <a name="configure-and-deploy-with-winrm"></a>WinRM で構成およびデプロイする

Windows ベースの VM 用の WinRM エンドポイントは、開発の 2 つの異なるステージの間に構成できます。

- 作成の間 - 既存の VHD に VM をデプロイするとき。  これは、新しいオファーに対して推奨されるアプローチです。  このアプローチでは、提供された Azure Resource Manager テンプレートを使用し、カスタマイズした PowerShell スクリプトを実行して、Azure 証明書を作成する必要があります。
- デプロイの後 - Azure でホストされている既存の VM で。  既に Azure に VM ソリューションをデプロイしてあり、それに対して Windows リモート管理を有効にする必要がある場合は、このアプローチを使用します。  このアプローチでは、Azure portal で手動による変更を行い、ターゲット VM でスクリプトを実行する必要があります。


## <a name="next-steps"></a>次のステップ
新しい VM を作成する場合は、[VHD からの VM のデプロイ](./cpp-deploy-vm-vhd.md)の間に WinRM を有効にすることができます。  それ以外の場合は、既存の VM で WinRM を有効にできます
