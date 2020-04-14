---
title: 非準拠の Azure Automation State Configuration サーバーを修復する
description: 構成状態がドリフトになっているサーバーにオンデマンドで構成を再適用する方法
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: f4ca76f4be9d00e185f8774fc33296d1af1aeece
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585499"
---
# <a name="remediate-non-compliant-dsc-servers"></a>準拠していない DSC サーバーを修復する

サーバーが Azure Automation State Configuration に登録されると、'構成モード' が ApplyOnly、ApplyandMonitor、または ApplyAndAutoCorrect に設定されます。
モードが AutoCorrect に設定されていない場合、何らかの理由で準拠状態から逸脱したサーバーは、手動で修正されるまで非準拠のままになります。

Azure コンピューティングでは、ユーザーが仮想マシン内でスクリプトを実行できるようにする実行コマンドという機能が提供されています。
このドキュメントでは、構成ドリフトを手動で補正する場合のこの機能のサンプル スクリプトを提供します。

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>PowerShell を使用して Windows 仮想マシンのドリフトを補正する

Windows 仮想マシンで実行コマンド機能を使用する詳細な手順については、「[実行コマンドを使用して Windows VM で PowerShell スクリプトを実行する](/azure/virtual-machines/windows/run-command)」のドキュメント ページを参照してください。

Azure Automation State Configuration ノードで強制的に最新の構成をダウンロードして適用するには、`Update-DscConfiguration` コマンドレットを使用します。
詳細については、[Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) コマンドレットのドキュメントを参照してください。

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Linux 仮想マシンのドリフトを補正する

同様の機能は、Linux サーバーでは現在使用できません。
唯一のオプションは、登録プロセスを繰り返すことです。
Azure ノードの場合、ドリフト補正は、ポータルから、または Az Automation コマンドレットを使用して行うことができます。
このプロセスの詳細は、「[Azure Automation State Configuration による管理のためのマシンのオンボード](/azure/automation/automation-dsc-onboarding#onboard-a-vm-using-azure-portal)」のページに記載されています。
ハイブリッド ノードの場合は、含まれている Python スクリプトを使用して、ドリフト補正を行うことができます。
[Linux リポジトリ用の PowerShell DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer) のドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/azurerm.automation/#automation)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
