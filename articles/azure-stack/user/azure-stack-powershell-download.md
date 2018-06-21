---
title: GitHub からの Azure Stack ツールのダウンロード | Microsoft Docs
description: Azure Stack の操作に必要なツールをダウンロードする方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 28F360AD-789A-488D-965F-FC6E6CCF3329
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: f4c4d907fe76c5e6ee5893b5b4dd1b2d766877a7
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287259"
---
# <a name="download-azure-stack-tools-from-github"></a>GitHub からの Azure Stack ツールのダウンロード

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

AzureStack-Tools は、リソースの管理と Azure Stack へのデプロイに使用できる PowerShell モジュールをホストする GitHub レポジトリです。

## <a name="download-targets"></a>ターゲットのダウンロード

これらの PowerShell モジュールは、Azure Stack Development Kit にダウンロードするか、または VPN 接続を使用する Windows ベースの外部クライアントにダウンロードして使用できます。

## <a name="how-to-get-the-tools"></a>ツールの入手方法

これらのツールを入手するには、AzureStack-Tools GitHub リポジトリを複製するか、次のスクリプトを実行して AzureStack-Tools フォルダーをダウンロードします。

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>モジュールで提供される機能

AzureStack-Tools レポジトリには、Azure Stack の次の機能をサポートする PowerShell モジュールが含まれています。

| 機能 | 説明 | このモジュールを使用できるユーザー |
| --- | --- | --- |
| [クラウド機能](azure-stack-validate-templates.md) | このモジュールを使用すると、クラウドのクラウド機能を取得できます。 たとえば、このモジュールを使用して、Azure Stack および Azure クラウドで、API バージョン、Azure Resource Manager リソース、VM 拡張機能などのクラウド機能を取得できます。 | クラウドの管理者とユーザー。 |
| [Azure Stack の Resource Manager ポリシー](azure-stack-policy-module.md) | このモジュールを使用すると、Azure Stack と同じバージョン管理とサービス可用性で、Azure サブスクリプションまたは Azure リソース グループを構成できます。 | クラウドの管理者とユーザー |
| [Azure Stack への接続](azure-stack-connect-azure-stack.md) | このモジュールを使用すると、PowerShell で Azure Stack インスタンスに接続し、Azure Stack への VPN 接続を構成できます。 | クラウドの管理者とユーザー |
| [テンプレートの検証](azure-stack-validate-templates.md) | このモジュールを使用すると、既存のまたは新しいテンプレートを Azure Stack にデプロイできるかどうか確認できます。 | クラウドの管理者とユーザー |

## <a name="next-steps"></a>次の手順

* [Azure Stack ユーザーの PowerShell 環境の構成](azure-stack-powershell-configure-user.md)
* [VPN 経由での Azure Stack Development Kit への接続](azure-stack-connect-azure-stack.md)
