---
title: "Azure ML Workbench リリース ノート: スプリント 3 - 2018 年 1 月"
description: "このドキュメントでは、Azure ML のスプリント 3 リリースの更新について詳細に説明します"
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/22/2018
ms.openlocfilehash: 266a56d5e247fd4926031e563c8be302599838eb
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2018
---
# <a name="sprint-3---january-2018"></a>スプリント 3 - 2018 年 1 月 

#### <a name="version-number-01171218263"></a>バージョン番号: 0.1.1712.18263

>バージョン番号を調べる方法については、[こちら](known-issues-and-troubleshooting-guide.md)をご覧ください。

Azure Machine Learning Workbench の 4 回目の更新へようこそ。 このスプリントでの更新と機能強化は次のとおりです。 これらの更新の多くは、ユーザー フィードバックの直接の結果として行われました。 

## <a name="notable-new-features-and-changes"></a>注目すべき新機能と変更
- 認証スタックへの更新がスタートアップ時のログインとアカウント選択に強制されます。

## <a name="detailed-updates"></a>詳細な更新
以下は、このスプリントでの Azure Machine Learning の各コンポーネント領域内の詳細な更新の一覧です。

### <a name="workbench"></a>ワークベンチ
- [プログラムの追加と削除] からアプリをインストール/アンインストールできるようになりました。
- 認証スタックへの更新がスタートアップ時のログインとアカウント選択に強制されます。
- Windows のシングル サインオン (SSO) エクスペリエンスが改善されました。
- 異なる資格情報の複数のテナントに属しているユーザーが Workbench にサインインできるようになります。

#### <a name="ui"></a>UI
- 全般的な機能強化とバグの修正

### <a name="notebooks"></a>Notebook
- 全般的な機能強化とバグの修正

### <a name="data-preparation"></a>データの準備 
- [例による] 変換実行中の自動提案が機能強化されました。
- パターン頻度インスペクターのアルゴリズムが強化されました。
- [例による] 変換実行中にサンプル データとフィードバックを送信できるようになりました。![派生列変換のフィードバック送信リンクのイメージ](media/release-notes-sprint-3/SendFeedbackFromDeriveColumn.png)
- Spark ランタイムが機能強化されました。
- Pyspark が Scala に置き換えられました。
- 時系列インスペクターの適用外データを終了できない問題が修正されました。 
- HDI のデータ準備を実行したときに反応しなくなる問題を修正されました。

### <a name="model-management-cli-updates"></a>モデル管理 CLI の更新 
  - リソースのプロビジョニングでサブスクリプションの所有権が不要になりました。 デプロイ環境の設定は、リソース グループへの共同作成者のアクセスで十分になります。
  - Free サブスクリプションでローカル環境のセットアップが有効になりました。 
