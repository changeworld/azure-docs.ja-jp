---
author: baanders
description: 基本的な Azure Digital Twins インスタンスとアプリの登録をクリーンアップするためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: e8f167555434338416381aaf8978a3d551686fb2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "90606627"
---
このチュートリアルで作成したリソースが不要であれば、次の手順に従って削除してください。

[Azure Cloud Shell](https://shell.azure.com) から [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) コマンドを使用すると、リソース グループ内の Azure リソースをすべて削除できます。 リソース グループと Azure Digital Twins インスタンスが削除されます。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 

Azure Cloud Shell を開き、次のコマンドを実行すると、リソース グループとそこに含まれる内容がすべて削除されます。

```azurecli
az group delete --name <your-resource-group>
```

次に、クライアント アプリ用に作成した Azure Active Directory アプリの登録を次のコマンドで削除します。

```azurecli
az ad app delete --id <your-application-ID>
```