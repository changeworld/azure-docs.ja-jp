---
title: GitHub からの Azure Stack ツールのダウンロード | Microsoft Docs
description: Azure Stack の操作に必要なツールをダウンロードする方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 05dd3b292f90964c6af21890aaeafab9849a09ed
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242979"
---
# <a name="download-azure-stack-tools-from-github"></a>GitHub からの Azure Stack ツールのダウンロード

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

**AzureStack-Tools** は PowerShell モジュールをホストする [GitHub リポジトリ](https://github.com/Azure/AzureStack-Tools)で、リソースの管理と Azure Stack へのデプロイに使用できます。 VPN 接続を確立しようとしている場合は、これらの PowerShell モジュールを Azure Stack Development Kit にダウンロードするか、Windows ベースの外部クライアントにダウンロードできます。 これらのツールを入手するには、GitHub リポジトリを複製するか、次のスクリプトを実行して **AzureStack-Tools** フォルダーをダウンロードします。

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>モジュールで提供される機能

**AzureStack-Tools** リポジトリには、Azure Stack の次の機能をサポートする PowerShell モジュールが含まれています。  

| 機能 | 説明 | このモジュールを使用できるユーザー |
| --- | --- | --- |
| [クラウド機能](user/azure-stack-validate-templates.md) | このモジュールを使用すると、クラウドのクラウド機能を取得できます。 たとえば、このモジュールを使用して、API バージョンや Azure Resource Manager リソースなどのクラウド機能を取得できます。 このモジュールを使用して Azure Stack と Azure クラウドの VM 拡張機能を入手することもできます。 | クラウド オペレーターとユーザー |
| [Azure Stack の Resource Manager ポリシー](user/azure-stack-policy-module.md) | このモジュールを使用すると、Azure Stack と同じバージョン管理とサービス可用性で、Azure サブスクリプションまたは Azure リソース グループを構成できます。 | クラウド オペレーターとユーザー |
| [Azure への登録](azure-stack-register.md) | このモジュールを使用すると、開発キットのインスタンスを Azure に登録できます。 登録後、Azure からマーケットプレースの項目をダウンロードして Azure Stack で使用できます。 | クラウド オペレーター |
| [Azure Stack のデプロイ](azure-stack-run-powershell-script.md) | このモジュールを使用すると、Azure Stack の仮想ハード ディスク (VHD) イメージを使用して、Azure Stack のホスト コンピューターでデプロイおよび再デプロイを行う準備ができます。 | クラウド オペレーター|
| [Azure Stack への接続](azure-stack-connect-powershell.md) | Azure Stack への VPN 接続を構成するには、このモジュールを使用します。 | クラウド オペレーターとユーザー |
| [テンプレートの検証](user/azure-stack-validate-templates.md) | このモジュールを使用すると、既存のまたは新しいテンプレートを Azure Stack にデプロイできるかどうか確認できます。 | クラウド オペレーターとユーザー|


## <a name="next-steps"></a>次の手順
* [Azure Stack ユーザーの PowerShell 環境の構成](user/azure-stack-powershell-configure-user.md)   
* [VPN 経由での Azure Stack Development Kit への接続](azure-stack-connect-azure-stack.md)  
