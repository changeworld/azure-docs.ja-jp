---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/07/2021
ms.author: alkohli
ms.openlocfilehash: c8a749a1b0e14859b0eb2c81b4bfeafd1dba0913
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868157"
---
再試行できないエラーを確認して、注文を続行するには、次の手順を実行します。

1. Azure portal で注文を開きます。  

   再試行できないエラーが発生してファイルをアップロードできない場合は、次の通知が表示されます。 現在の注文の状態は、 **[データのコピーが完了しましたが、エラーが発生しました。デバイスの保留中のデータの消去]** となります。

   ![Azure portal でのアップロード中にコピー エラーが発生した場合の通知](media/data-box-review-nonretryable-errors/copy-errors-in-upload-01.png)

   **[データ コピーの詳細]** の **[ログ パスのコピー]** をメモしてください。 データ コピー ログでエラーを確認します。

   > [!NOTE]
   > [!INCLUDE [data-box-copy-logs-behind-firewall](data-box-copy-logs-behind-firewall.md)]

2. **[Confirm device erasure]\(デバイス消去の確認\)** を選択して、確認パネルを開きます。

   ![Azure portal でのアップロード エラーについて確認し、続行するためのパネル](media/data-box-review-nonretryable-errors/copy-errors-in-upload-02.png)

3. 前にメモしたコピー ログ パスを使用して、データ コピー ログでエラーを確認します。 必要に応じて、 **[閉じる]** を選択してパスを再度表示することもできます。 

   ネットワーク転送または新しいインポート注文を使用して別のアップロードを実行する前に、構成の問題を修正する必要があります。 <!--For guidance, see [Review copy errors in uploads from Azure Data Box and Azure Data Box Heavy devices](../articles/databox/data-box-troubleshoot-data-upload.md). - To make the Include, I needed to move this reference out of the main procedure.-->

4. エラーを確認したら、チェック ボックスをオンにし、データ消去を続行する準備ができていることを確認します。 次に、 **[続行]** を選択します。

   ![データ消去を続行する準備ができていることを確認する](media/data-box-review-nonretryable-errors/copy-errors-in-upload-03.png)

   データがデバイスから安全に消去されたら、注文ステータスは **[コピーは完了しましたが、エラーが発生しました]** に更新されます。

   ![エラーが発生して完了した Data Box のインポート注文のステータス表示](media/data-box-review-nonretryable-errors/copy-errors-in-upload-04.png)

   何も操作を行わない場合、注文は 14 日後に自動的に完了します。

