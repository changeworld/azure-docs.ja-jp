---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d804cb310a8638713cabf76c2f4192a0e4d0f43d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134400"
---
## <a name="create-a-project-zip-file"></a>プロジェクトの ZIP ファイルを作成する

まだサンプル プロジェクトのルート ディレクトリにいることを確認します。 プロジェクト内のすべての ZIP アーカイブを作成します。 次のコマンドには、お使いの端末の既定のツールを使用します。

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

その後、この ZIP ファイルを Azure にアップロードし、App Service にデプロイします。