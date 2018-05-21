---
title: Azure Automation DSC Overview
description: Azure Automation Desired State Configuration (DSC) の概要、その利用規約、および既知の問題
keywords: powershell dsc, 必要な状態の構成, powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 83d5b89422a0181c06dbfe3b2bd8975ef7214b9d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
---
# <a name="azure-automation-dsc-overview"></a>Azure Automation DSC Overview

Azure Automation DSC は、PowerShell の Desired State Configuration (DSC) [構成](https://msdn.microsoft.com/powershell/dsc/configurations)の書き込み、管理、コンパイル、[DSC リソース](https://msdn.microsoft.com/powershell/dsc/resources)のインポート、および構成のターゲット ノードへの割り当てを、すべてクラウドで実行できる Azure サービスです。

## <a name="why-use-azure-automation-dsc"></a>Azure Automation DSC を使用する理由

Azure Automation DSC には、Azure の外部で DSC を使用する際にいくつかの利点があります。

### <a name="built-in-pull-server"></a>組み込みのプル サーバー

Azure Automation には、ターゲット ノードが自動的に構成を受信して、目的の状態に適合し、その準拠の状態に関するレポートを返すことができる [DSC プル サーバー](https://msdn.microsoft.com/powershell/dsc/pullserver)が用意されています。
Azure Automation の組み込みのプル サーバーでは、独自のプル サーバーを設定して管理する必要はありません。
Azure Automation は、クラウドまたはオンプレミスの仮想または物理的な Windows コンピューターや Linux コンピューターを対象とすることができます。

### <a name="management-of-all-your-dsc-artifacts"></a>すべての DSC アーティファクトの管理

Azure Automation DSC は、Azure Automation が PowerShell スクリプト用に提供するものと同じ管理レイヤーを [PowerShell Desired State 構成](https://msdn.microsoft.com/powershell/dsc/overview) に提供します。

Azure Portal から、または PowerShell から、すべての DSC 構成、リソース、およびターゲット ノードを管理できます。

![Azure Automation ブレードのスクリーン ショット](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Log Analytics へのレポート データのインポート

Azure Automation DSC で管理されているノードは、組み込みのプル サーバーに詳細なレポート ステータス データを送信します。
Azure Automation DSC を構成して、このデータを Log Analytics ワークスペースに送信できます。
Log Analytics ワークスペースに DSC ステータス データを送信する方法については、「[Log Analytics への Azure Automation DSC レポート データの転送](automation-dsc-diagnostics.md)」を参照してください。

## <a name="introduction-video"></a>紹介ビデオ

読むより見る方がよければ、 Azure Automation DSC が初めて発表された 2015年 5 月に公開された次のビデオをご覧ください。

>[!NOTE]
>このビデオで解説されている概念とライフ サイクルは正しいものですが、このビデオが作成されてから Azure Automation DSC はかなり進歩しています。
>Azure Automation DSC は一般公開され、Azure ポータルでさまざまな UI を使用できるようになり、多数の機能がサポートされています。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>次の手順

* Azure Automation DSC で管理対象ノードをオンボードにする方法は、「[Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」を参照してください。
* Azure Automation DSC の使用の概要は、「[Azure Automation DSC の使用](automation-dsc-getting-started.md)」をご覧ください。
* DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation DSC での構成のコンパイル](automation-dsc-compile.md)」を参照してください。
* Azure Automation DSC の PowerShell コマンドレットのリファレンスについては、「[Azure Automation DSC cmdlets (Azure Automation DSC コマンドレット)](/powershell/module/azurerm.automation/#automation)」を参照してください。
* 料金情報については、[Azure Automation DSC の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページを参照してください。
* 継続的なデプロイ パイプラインで Azure Automation DSC を使う例については、「[Azure Automation DSC と Chocolatey を使用した IaaS VM への継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
