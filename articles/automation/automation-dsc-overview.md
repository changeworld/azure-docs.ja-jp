---
title: Azure Automation State Configuration の概要
description: Azure Automation State Configuration (DSC) の概要、その利用規約、および既知の問題
keywords: powershell dsc, 必要な状態の構成, powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2d46803f81b369f8f24a6f0e3c7f32955503e4a
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006063"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration の概要

Azure Automation State Configuration は、PowerShell の Desired State Configuration (DSC) [構成](/powershell/dsc/configurations)の書き込み、管理、コンパイル、[DSC リソース](/powershell/dsc/resources)のインポート、および構成のターゲット ノードへの割り当てを、すべてクラウドで実行できる Azure サービスです。

## <a name="why-use-azure-automation-state-configuration"></a>Azure Automation State Configuration を使用する理由

Azure Automation State Configuration には、Azure の外部で DSC を使用する際にいくつかの利点があります。

### <a name="built-in-pull-server"></a>組み込みのプル サーバー

Azure Automation State Configuration には、ターゲット ノードが自動的に構成を受信して、目的の状態に適合し、その準拠の状態に関するレポートを返すことができる [Windows Feature DSC-Service](/powershell/dsc/pullserver) と似た DSC プル サーバーが用意されています。 Azure Automation の組み込みのプル サーバーでは、独自のプル サーバーを設定して管理する必要はありません。 Azure Automation は、クラウドまたはオンプレミスの仮想または物理的な Windows コンピューターや Linux コンピューターを対象とすることができます。

### <a name="management-of-all-your-dsc-artifacts"></a>すべての DSC アーティファクトの管理

Azure Automation State Configuration は、Azure Automation が PowerShell スクリプト用に提供するものと同じ管理レイヤーを [PowerShell Desired State Configuration](/powershell/dsc/overview) に提供します。

Azure Portal から、または PowerShell から、すべての DSC 構成、リソース、およびターゲット ノードを管理できます。

![Azure Automation ブレードのスクリーン ショット](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Log Analytics へのレポート データのインポート

Azure Automation State Configuration で管理されているノードは、組み込みのプル サーバーに詳細なレポート ステータス データを送信します。 Azure Automation State Configuration を構成して、このデータを Log Analytics ワークスペースに送信できます。 Log Analytics ワークスペースに State Configuration ステータス データを送信する方法については、「[Log Analytics への Azure Automation State Configuration レポート データの転送](automation-dsc-diagnostics.md)」をご覧ください。

## <a name="introduction-video"></a>紹介ビデオ

読むより見る方がよければ、 Azure Automation State Configuration が初めて発表された 2015 年 5 月に公開された次のビデオをご覧ください。

> [!NOTE]
> このビデオで解説されている概念とライフ サイクルは正しいものですが、このビデオが作成されてから Azure Automation State Configuration はかなり進歩しています。 Azure Automation DSC は一般公開され、Azure ポータルでさまざまな UI を使用できるようになり、多数の機能がサポートされています。

[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>次の手順

- 使用を開始するには、「[Azure Automation State Configuration の使用](automation-dsc-getting-started.md)」をご覧ください。
- ノードをオンボードにする方法は、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/azurerm.automation/#automation)に関するページをご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。