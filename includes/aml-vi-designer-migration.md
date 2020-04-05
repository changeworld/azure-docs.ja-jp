---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "75535715"
---
1. [Azure Machine Learning Studio](https://ml.azure.com) にサインインします。

1. ワークスペースを Enterprise Edition にアップグレードします。

    アップグレード後、すべてのビジュアル インターフェイス実験では、デザイナーのパイプライン ドラフトに変換されます。
    
    > [!NOTE]
    > ビジュアル インターフェイス Web サービスをリアルタイム エンドポイントに変換するために、Enterprise Edition にアップグレードする必要はありません。
    
1. ワークスペースのデザイナー セクションにアクセスして、パイプライン ドラフトの一覧を表示します。 
    
    変換された Web サービスを見つけるには、**エンドポイント** > **リアルタイム エンドポイント**に移動します。

1. パイプライン ドラフトを選択して開きます。

    変換プロセス中にエラーが発生した場合は、エラー メッセージに問題を解決するための手順が表示されます。 

### <a name="known-issues"></a>既知の問題

 手動で対処する必要がある移行に関する既知の問題を以下に示します。

- **データのインポート** モジュールまたは**データのエクスポート** モジュール
        
    実験に**データのインポート** モジュールまたは**データのエクスポート** モジュールがある場合は、データストアを使用するために、データ ソースを更新する必要があります。 データストアを作成する方法については、「[Azure ストレージ サービスのデータにアクセスする](../articles/machine-learning/how-to-access-data.md)」を参照してください。 クラウド ストレージ アカウントの情報は、使いやすいように **データのインポート**または**データのエクスポート**のコメントに追加されています。 
      