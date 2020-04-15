---
title: Microsoft Defender ATP データを Azure Sentinel に接続する | Microsoft Docs
description: Microsoft Defender Advanced Threat Protection データを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756344"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Microsoft Defender Advanced Threat Protection からアラートを接続する 


> [!IMPORTANT]
> Microsoft Defender Advanced Threat Protection アラートのインジェストは、現在パブリック プレビュー段階です。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
 

[Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) コネクタを使用すると、Microsoft Defender Advanced Threat Protection から Azure Sentinel にアラートをストリーミングできます。 これにより、組織全体のセキュリティ イベントをより包括的に分析し、効果的かつ迅速に対応するためのプレイブックを構築することができます。

## <a name="prerequisites"></a>前提条件

- [Microsoft Defender ATP の展開の設定](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)に関する説明に従い、Microsoft Defender Advanced Threat Protection の有効なライセンスを持っている必要があります。 
- Azure Sentinel テナントの管理者またはセキュリティ管理者である必要があります。


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Microsoft Defender Advanced Threat Protection に接続する

Microsoft Defender Advanced Threat Protection がデプロイされ、データのインジェストを行っている場合は、アラートを簡単に Azure Sentinel にストリーム配信することができます。


1. Azure Sentinel で、 **[Data connectors]\(データ コネクタ\)** を選択し、 **[Microsoft Defender Advanced Threat Protection]** タイルをクリックし、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。
1. **[Connect]** をクリックします。 
1. Defender ATP のアラートで Log Analytics の関連スキーマを使用するには、**SecurityAlert** を検索します。**プロバイダ名**は **MDATP** です。




## <a name="next-steps"></a>次のステップ
このドキュメントでは、Microsoft Defender ATP を Azure Sentinel に接続する方法を学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
