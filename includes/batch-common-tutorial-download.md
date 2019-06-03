---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127355"
---
### <a name="retrieve-output-files"></a>出力ファイルを取得する

Azure Portal を使用して、ffmpeg タスクによって生成された出力 MP3 ファイルをダウンロードすることができます。 

1. **[すべてのサービス]** > **[ストレージ アカウント]** の順にクリックし、ストレージ アカウントの名前をクリックします。
2. **[BLOB]** > *[出力]* の順にクリックします。
3. 出力 MP3 ファイルの 1 つを右クリックして、**[ダウンロード]** をクリックします。 ブラウザーのメッセージに従って、ファイルを開くか保存します。

![出力ファイルをダウンロードする](./media/batch-common-tutorial-download/download.png)

このサンプルには示されていませんが、コンピューティング ノードまたはストレージ コンテナーからプログラムでファイルをダウンロードすることもできます。
