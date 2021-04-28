---
title: Azure Functions の Event Grid のローカル デバッグ
description: Event Grid イベントによってトリガーされる Azure Functions をローカルでデバッグする方法について説明します
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 7483a097b188b9f96221a13964992c7b02332258
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892033"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Azure Functions の Event Grid トリガーのローカル デバッグ

この記事では、ストレージ アカウントによって発生した Azure Event Grid イベントを処理するローカル関数をデバッグする方法を示します。 

## <a name="prerequisites"></a>前提条件

- 既存の関数アプリを作成または使用する
- 既存のストレージ アカウントを作成または使用する
- [ngrok](https://ngrok.com/) をダウンロードして、Azure でローカル関数を呼び出せるようにする

## <a name="create-a-new-function"></a>新しい関数を作成する

Visual Studio で関数アプリを開き、ソリューション エクスプローラーでプロジェクト名を右クリックし、 **[追加] > [新しい Azure 関数]** の順にクリックします。

*[新しい Azure 関数]* ウィンドウで、 **[Event Grid トリガー]** 選択し、 **[OK]** をクリックします。

![新しい関数の作成](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

関数が作成されたら、コード ファイルを開いて、ファイルの上部にコメント アウトされた URL をコピーします。 この場所は、Event Grid トリガーを構成するときに使用されます。

![コピーの場所](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

次に、`log.LogInformation` で始まる行にブレークポイントを設定します。

![ブレークポイントの設定](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


次に、**F5 キーを押して** デバッグ セッションを開始します。

[!INCLUDE [functions-event-grid-local-dev](../../includes/functions-event-grid-local-dev.md)]

## <a name="debug-the-function"></a>関数をデバッグする

新しいファイルがストレージ コンテナーにアップロードされることを Event Grid が認識すると、ローカル関数でブレークポイントがヒットします。

![ngrok を起動する](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースをクリーンアップするには、ストレージ アカウント内の **test** コンテナーを削除します。

## <a name="next-steps"></a>次のステップ

- [Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Azure Functions の Event Grid トリガー](./functions-bindings-event-grid.md)
