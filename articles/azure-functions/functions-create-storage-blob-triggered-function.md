---
title: "Blob Storage でトリガーされる Azure 関数の作成 | Microsoft Docs"
description: "Azure Functions を使用して、Azure Blob Storage に追加された項目によって呼び出されるサーバーレスの関数を作成します。"
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b00f9e7491048a5dd60e0decab1727a1ff01448e
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Azure Blob Storage によってトリガーされる関数の作成

Azure Blob Storage でファイルをアップロードしたり、更新したりするときにトリガーされる関数の作成方法について説明します。  

![ログ内のメッセージを表示します。](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

このトピックの手順をすべて完了するまでにかかる時間は、5 分未満です。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

[Microsoft Azure Storage エクスプローラー](http://storageexplorer.com/)をダウンロードしてインストールする必要もあります。 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Blob Storage でトリガーされる関数の作成

Function App で、**[関数]** の横にある **[+]** ボタンをクリックし、使用する言語の **BlobTrigger** テンプレートをクリックします。 テーブルに指定されている設定を使用して、**[作成]** をクリックします。

![Blob Storage でトリガーされる関数を作成します。](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)
    
| Setting      |  推奨値   | Description                                        |
| ------------ |  ----------------- | -------------------------------------------------- |
| **パス**   | mycontainer/{name}    | 監視されている Blob Storage ストレージ内の位置。 Blob のファイル名は、_name_ パラメーターとしてバインディングで渡されます。  |
| **ストレージ アカウント接続** | AzureWebJobStorage | Function App によって既に使用されているストレージ アカウント接続を使用するか、新しく作成できます。  |
| **関数名の指定** | Function App 内で一意 | このキューによってトリガーされる関数の名前。 | 

次に、Azure Storage アカウントに接続し、**mycontainer** コンテナーを作成します。

## <a name="create-the-container"></a>コンテナーの作成

1. 関数で、**[統合]** をクリックし、**[ドキュメント]** を展開して、**[アカウント名]** と **[アカウント キー]** の両方をコピーします。 これらの資格情報を使用して、ストレージ アカウントに接続します。 ストレージ アカウントに既に接続している場合は、手順 4 に進みます。
 
    ![ストレージ アカウント接続の資格情報を取得します。](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

2. [Microsoft Azure Storage Explorer](http://storageexplorer.com/) ツールを実行し、左側の接続アイコンをクリックして、**[Use a storage account name and key] (ストレージ アカウント名とキーを使用)** を選択し、**[次へ]** をクリックします。

    ![ストレージ アカウント エクスプローラー ツールを実行します。](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)
    
3. 手順 1 の **[アカウント名]** と **[アカウント キー]** を入力し、**[次へ]** をクリックし、**[接続]** をクリックします。 
  
    ![ストレージ資格情報を入力し、接続します。](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

4. 接続されたストレージ アカウントを展開し、**[Blob containers] (Blob コンテナー)** を右クリックして、**[Create blob container] (Blob コンテナーの作成)** をクリックし、「`mycontainer`」と入力して、Enter キーを押します。
 
    ![ストレージ キューを作成します。](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Blob コンテナーが用意されているので、このコンテナーにファイルをアップロードすることによって、関数をテストできます。  

## <a name="test-the-function"></a>関数をテストする

1. Azure Portal に戻り、関数を参照して、ページ下部の **[ログ]** を展開して、ログ ストリーミングが一時停止していないことを確認します。

2. ストレージ エクスプローラーで、ストレージ アカウント、**[Blob containers] (Blob コンテナー)**、および **[mycontainer]** の順に展開します。 **[アップロード]**、**[Upload files...] (ファイルをアップロードしています...)** の順にクリックします。

    ![Blob コンテナーにファイルをアップロードします。](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

2. **[Upload files] (ファイルのアップロード)** ダイアログ ボックスで、**[ファイル]** フィールドをクリックします。 画像ファイルなど、ローカル コンピューター上のファイルを参照して選択し、**[開く]**、**[アップロード]** の順にクリックします。   
 
3. 関数ログに戻り、Blob が読み取られたことを確認します。 

   ![ログ内のメッセージを表示します。](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > 既定の従量課金プランで Function App を実行する場合、Blob が追加されたり更新されてから関数がトリガーされるまで、最高数分間の遅延が生じることがあります。 Blob でトリガーされた関数で待機時間を短くする必要がある場合は、App Service プランで Function App を実行することを検討してください。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

Blob Storage で Blob が追加または更新されたときに実行する関数を作成しました。 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Blob Storage トリガーの詳細については、「[Azure Functions における Blob Storage バインディング](functions-bindings-storage-blob.md)」を参照してください。 




