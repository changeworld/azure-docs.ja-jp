---
title: Azure Chaos Studio でのカオス実験の実行と管理
description: Azure Chaos Studio でカオス実験の開始、停止、詳細の表示、履歴の表示を行う方法について説明します
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 06abbe99467af52cb7867d31f31d4aa2d727d0ae
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089957"
---
# <a name="run-and-manage-an-experiment-in-azure-chaos-studio"></a>Azure Chaos Studio でのカオス実験の実行と管理

カオス実験では、制御された環境で障害を発生させることで、アプリケーションが障害に強いことを検証できます。 このアーティクルでは、過去に作成したカオス実験を使用する方法の概要を説明します。

## <a name="start-an-experiment"></a>実験を開始する

1. [Azure Portal](https://portal.azure.com)を開きます。

2. 検索バーで **Chaos Studio (プレビュー)** を検索します。

3. **[実験]** をクリックします。 実験リスト ビューを使用すると、実験を一括で開始、停止、または削除したり、新しい実験を作成できます。

    ![ポータルの実験リスト](images/run-experiment-list.png)

4. 実験をクリックします。 実験の概要ページでは、実験の開始、停止、編集、リソースに関する重要な詳細の表示と、履歴の表示ができます。 **[Start (スタート)]** ボタンをクリックし、 **[OK]** をクリックして実験を開始します。

    ![実験の開始](images/run-experiment-start.png)

5. 実験の状態には、*PreProcessingQueued*、*WaitingToStart* と、最後の *実行* が表示されます。

## <a name="view-experiment-history-and-details"></a>実験の履歴と詳細を表示する

1. 実験が実行されたら、 **[履歴]** で現在実行されている **[Details (詳細)]** をクリックして、詳細な状態とエラーを確認します。

    ![実行履歴](images/run-experiment-history.png)

2. 実験の詳細ビューには、各ステップ、分岐、およびエラーの実行状態が表示されます。 エラーをクリックします。

    ![実験の詳細の表示](images/run-experiment-details.png)

3. エラーの詳細には、失敗または成功したターゲットとその原因など、エラーの実行に関する追加情報が表示されます。 実験の実行中にエラーが発生した場合は、ここにデバッグ情報が表示されます。

    ![エラーの詳細](images/run-experiment-fault.png)

## <a name="edit-experiment"></a>実験の編集

1. 実験の概要に戻り、 **[編集]** ボタンをクリックします。

    ![実験の編集](images/run-edit.png)

2. 実験作成時に使用したものと同じ実験デザイナーです。 ステップ、分岐、エラーを追加または削除したり、エラー パラメーターとターゲットを編集できます。 エラーを編集するには、エラーの横にある **...** をクリックします。

    ![エラーの編集](images/run-edit-ellipses.png)

3. 編集が終了したら、 **[Save (保存)]** をクリックします。 保存せずに変更を破棄する場合は、右上にある **[閉じる (X)]** ボタンをクリックします。
  ![実験の保存](images/run-edit-save.png)

> [!WARNING]
> 実験にターゲットを追加した場合は、ターゲット リソースに実験 ID 用のロール割り当てを追加することを忘れないでください。

## <a name="delete-experiment"></a>実験の削除
1. 実験リストに戻り、削除する実験を確認します。 実験リストの上にあるツールバーの **[削除する]** をクリックします。 画面の解像度によっては、省略記号 (...) をクリックしないと削除オプションが表示されない場合があります。

    ![実験の削除](images/run-delete.png)

2. **[Yes (はい)]** をクリックしてリソースの削除を確認します。

3. また、実験を開いて、ツールバーの **[削除する]** ボタンをクリックすることもできます。
