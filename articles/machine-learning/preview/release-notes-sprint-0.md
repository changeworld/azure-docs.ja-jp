---
title: 'Azure ML Workbench リリース ノート: スプリント 0 - 2017 年 10 月'
description: このドキュメントでは、Azure ML のスプリント 0 リリースに対する更新について詳しく説明します
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2018
---
# <a name="sprint-0---october-2017"></a>スプリント 0 - 2017 年 10 月 

**バージョン番号: 0.1.1710.04013**

Microsoft Ignite 2017 カンファレンスでの最初のパブリック プレビューに続いて、Azure Machine Learning Workbench の初回の更新が実施されています。 今回のリリースでの主要な更新プログラムは、信頼性と安定化の修正です。  対処した重大な問題には、以下が含まれます。

## <a name="new-features"></a>新機能
- macOS High Sierra がサポートされるようになりました。

## <a name="bug-fixes"></a>バグの修正
### <a name="workbench-experience"></a>Workbench のエクスペリエンス
- Workbench にファイルをドラッグ アンド ドロップすると、Workbench がクラッシュします。
- Workbench の IDE として構成された VS Code 内のターミナル ウィンドウでは、_az ml_ コマンドは認識されません。

### <a name="workbench-authentication"></a>Workbench の認証
報告されたさまざまなログインと認証に関する問題を改善するために、多数の更新を実施しました。
- 認証ウィンドウはポップ アップ状態を保ちます (特にインターネット接続が安定しない場合)。
- 認証トークンの有効期限に関する信頼性の問題を改善しました。
- 場合によっては、認証ウィンドウが 2 回表示されます。
- 認証プロセスが実際は終了しており、ポップアップ ダイアログ ボックスが既に閉じている場合でも、Workbench のメイン ウィンドウに [認証中] メッセージが引き続き表示されます。
- インターネット接続がない場合、認証ダイアログは空白の画面でポップアップ表示されます。

### <a name="data-preparation"></a>データの準備 
- 特定の値をフィルター処理すると、エラーと欠落値も除外されます。
- サンプリング方法を変更すると、後続の既存の結合操作が削除されます。
- 欠落値を置き換える変換では、NaN は考慮されません。
- 日付型の推定では、null 値が検出されたときに例外をスローします。

### <a name="job-execution"></a>ジョブの実行
- ジョブの実行でプロジェクト フォルダーがサイズの制限を超えたためにアップロードできない場合、明確なエラー メッセージはありません。
- ユーザーの Python スクリプトで作業ディレクトリが変更された場合、出力フォルダーに書き込まれるファイルは追跡されません。 
- アクティブな Azure サブスクリプションが現在のプロジェクトが属しているものと異なる場合、ジョブの送信によって 403 エラーが発生します。
- Docker が存在しないときに実行対象として Docker を使用しようとした場合、明確なエラー メッセージは返されません。
- _実行_ボタンをクリックしても、.runconfig ファイルは自動的に保存されません。

### <a name="jupyter-notebook"></a>Jupyter Notebook
- ユーザーが特定のログインの種類を使用した場合、Notebook サーバーを開始できません。
- Notebook サーバーのエラー メッセージは、ユーザーが表示できるログには記録されません。

### <a name="azure-portal"></a>Azure ポータル
- Azure ポータルのダーク テーマを選択すると、[モデル管理] ブレードがブラック ボックスで表示されます。

### <a name="operationalization"></a>運用化
- Web サービスを更新するためにマニフェストを再利用すると、新しい Docker イメージがランダムな名前で作成されます。
- Kubernetes クラスターから Web サービスのログを取得することはできません。
- モデル管理アカウントまたは ML コンピューティング アカウントを作成しようとすると、誤解を招くエラー メッセージが出力され、アクセス許可問題が発生します。
