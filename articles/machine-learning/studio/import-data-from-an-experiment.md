---
title: 別の実験のデータを Machine Learning Studio にインポートする | Microsoft Docs
description: Azure Machine Learning Studio にトレーニング データを保存して別の実験で保存する方法。
keywords: データのインポート、データ、データ ソース、トレーニング データ
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 7da9dcec-5693-4bb6-8166-15904e7f75c3
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 5007657da11cb45c6511780398c5425d14dca900
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246807"
---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>別の実験のデータを Azure Machine Learning Studio にインポートする
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

ある実験の中間結果を別の実験の一部として使用することが必要になる場合があります。 この場合は、モジュールをデータセットとして保存します。

1. データセットとして保存するモジュールの出力をクリックします。
2. **[データセットとして保存]** をクリックします。
3. メッセージが表示されたら、名前と、データセットを簡単に識別できるような説明を入力します。
4. **[OK]** チェックマークをクリックします。

保存が完了すると、ワークスペースのどの実験でもデータセットを使用できるようになります。 データセットは、モジュール パレットの **[保存されたデータセット]** リストから検索できます。

