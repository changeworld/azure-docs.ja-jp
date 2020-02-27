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
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588214"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Microsoft Defender Advanced Threat Protection からアラートを接続する 


> [!IMPORTANT]
> Microsoft Defender Advanced Threat Protection のログのインジェストは、現在パブリック プレビュー段階です。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
 

シングル クリックで [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) から Azure Sentinel にアラートをストリーム配信できます。 この接続を使用すると、Microsoft Defender Advanced Threat Protection から Azure Sentinel にアラートをストリーム配信できます。 

## <a name="prerequisites"></a>前提条件

- [ライセンスのプロビジョニングの検証と Microsoft Defender Advanced Threat Protection のセットアップの完了](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)に関する記事の説明に従って有効化された Microsoft Defender Advanced Threat Protection の正当なライセンス。 
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
