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
ms.openlocfilehash: cc5884e1f70bdccee4e7a113e6e3ee2d6604b50a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406083"
---
# <a name="remediate-noncompliant-dsc-servers"></a>非準拠の DSC サーバーを修復する

サーバーが Azure Automation State Configuration に登録されるとき、構成モードが `ApplyOnly`、`ApplyandMonitor`、または `ApplyAndAutoCorrect` に設定されます。 モードが `ApplyAndAutoCorrect` に設定されていない場合、何らかの理由で準拠状態から逸脱したサーバーは、手動で修正されるまで非準拠のままです。

Azure コンピューティングでは、ユーザーが仮想マシン内でスクリプトを実行できるようにする実行コマンドという機能が提供されています。
このドキュメントでは、構成ドリフトを手動で補正する場合のこの機能のサンプル スクリプトを提供します。

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>PowerShell を使用して Windows 仮想マシンのドリフトを補正する

Windows 仮想マシンで実行コマンド機能を使用する詳細な手順については、「[実行コマンドを使用して Windows VM で PowerShell スクリプトを実行する](/azure/virtual-machines/windows/run-command)」のドキュメント ページを参照してください。

Azure Automation State Configuration ノードで強制的に最新の構成をダウンロードして適用するには、[Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) コマンドレットを使用します。

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Linux 仮想マシンのドリフトを補正する

同様の機能は、Linux サーバーでは現在使用できません。
唯一のオプションは、登録プロセスを繰り返すことです。

Azure ノードの場合は、Azure portal から、または Az モジュール コマンドレットを使用して、ドリフトを補正することができます。 このプロセスの詳細は、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md#onboard-vms-by-using-the-azure-portal)」に記載されています。
ハイブリッド ノードの場合は、含まれている Python スクリプトを使用して、ドリフトを補正できます。
[Linux 用の Powershell DSC リポジトリ](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)を参照してください。

## <a name="next-steps"></a>次のステップ

- PowerShell コマンドレットのリファレンスについては、「[Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)」をご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。