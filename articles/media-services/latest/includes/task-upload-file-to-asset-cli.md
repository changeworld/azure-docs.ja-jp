---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88657084"
---
<!-- ### Upload files with the CLI -->

次のコマンドは、1 つのファイルをアップロードします。  

次の値を変更してください。

1. `/path/to/file` をファイルのローカル パスに変更します。  
1. `MyContainer` を、(名前ではなく) アセット ID を使用して以前作成したアセットに変更します。
1. `MyBlob` を、アップロードしたファイルに使用する名前に変更します。
1. `MyStorageAccountName` を、使用するストレージ アカウントの名前に変更します。
1. `MyStorageAccountKey` をストレージ アカウントのアクセス キーに変更します。

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
