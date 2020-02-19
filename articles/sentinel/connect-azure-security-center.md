---
title: Azure Security Center データを Azure Sentinel に接続する
description: Azure Sentinel に Azure Security Center データを接続する方法について説明します。
author: rkarlin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 4c75d1870ca3b966acdd658c91c0af8fd7465bfb
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190873"
---
# <a name="connect-data-from-azure-security-center"></a>Azure Security Center からデータを接続する





Azure Sentinel では、[Azure Security Center](../security-center/security-center-intro.md) からアラートを接続して、それらを Azure Sentinel にストリーミングできます。 

## <a name="prerequisites"></a>前提条件

- Azure Security Center からアラートをエクスポートするには、ストリーミングするログのサブスクリプションで Security Reader ロールが必要になります。

- サブスクリプションで実行されている[Azure Security Center Standard レベル](../security-center/security-center-pricing.md)を所有している必要があります。 ない場合は、[サブスクリプションを Standard にアップグレード](https://azure.microsoft.com/pricing/details/security-center/)してください。



## <a name="connect-to-azure-security-center"></a>Azure Security Center に接続する

1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Azure Security Center]** タイルをクリックします。

1. 右側で、そのアラームを Azure Sentinel にストリーミングするサブスクリプションの横にある **[接続]**  をクリックします。 アラートを Azure Sentinel にストリーミングするには、各サブスクリプションが Azure Security Center Standard レベルにアップグレードされていることを確認します。

1. Azure Security Center のアラートによって Azure Sentinel で自動的にインシデントが生成されるようにするかどうかを選択できます。 **[Create incidents]\(インシデントの作成\)** で **[有効化]** を選択して、接続されたセキュリティ サービスで生成されたアラートからインシデントを自動的に作成する既定の分析ルールを有効にします。 次に、 **[分析]** でこのルールを編集してから、 **[Active rules]\(アクティブなルール\)** を選択します。

3. **[接続]** をクリックします。

4. Azure Security Center のアラートで Log Analytics の関連スキーマを使用するには、**SecurityAlert** を検索します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Security Center を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
