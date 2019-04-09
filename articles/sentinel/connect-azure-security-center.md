---
title: Azure Sentinel プレビューで Azure Security Center のデータを収集する | Microsoft Docs
description: Azure Sentinel で Azure Security Center のデータを収集する方法を説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f59c5f6a9f497a6420172996f9f327f16ffd26f9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242275"
---
# <a name="collect-data-from-azure-security-center"></a>Azure Security Center からデータを収集する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。



Azure Sentinel では、[Azure Security Center](../security-center/security-center-intro.md) からアラートを収集して、それらを Azure Sentinel にストリーミングできます。 

## <a name="prerequisites"></a>前提条件

- Azure Security Center からアラートをエクスポートする場合は、そのログをストリーミングするサブスクリプションの共同作成者である必要があります。

- サブスクリプションで実行されている[Azure Security Center Standard レベル](../security-center/security-center-pricing.md)を所有している必要があります。 ない場合は、[サブスクリプションを Standard にアップグレード](https://azure.microsoft.com/pricing/details/security-center/)してください。

- 接続する各サブスクリプションのグローバル管理者またはセキュリティ管理者のアクセス許可を持つユーザーでログインする必要があります。


## <a name="connect-to-azure-security-center"></a>Azure Security Center に接続する

1. Azure Sentinel で **[データ収集]** を選択し、**[Azure Security Center]** タイルをクリックします。
1. 右側で、そのアラームを Azure Sentinel にストリーミングするサブスクリプションの横にある **[接続]**  をクリックします。 アラートを Azure Sentinel にストリーミングするには、各サブスクリプションが Azure Security Center Standard レベルにアップグレードされていることを確認します。

3. **[接続]** をクリックします。

4. Azure Security Center のアラートで Log Analytics の関連スキーマを使用するには、**SecurityEvent** を検索します。

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure Security Center を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法を確認します。
- [Azure Sentinel を使用した驚異の検出](tutorial-detect-threats.md)を開始します。
