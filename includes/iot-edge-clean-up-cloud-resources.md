---
title: インクルード ファイル
description: インクルード ファイル
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 97fb29205136aacaa1722bf2abf441c207b0c4cb
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "40046487"
---
### <a name="delete-azure-resources"></a>Azure リソースを削除する 

Azure のリソースとリソース グループは、削除すると元に戻すことができません。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 IoT Hub を、保持したいリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、IoT Hub リソースだけを削除してください。

リソースを削除するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインし、**[リソース グループ]** を選択します。

2. IoT Edge のテスト リソースを含んだリソース グループの名前を選択します。 

3. リソース グループに含まれるリソースの一覧を確認します。 それらすべてを削除する場合は、**[リソース グループの削除]** を選択します。 一部だけを削除する場合は、削除する各リソースをクリックして個別に削除してください。 
