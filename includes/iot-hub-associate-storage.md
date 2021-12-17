---
title: インクルード ファイル
description: インクルード ファイル
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ecd7e070c353d706865b245427758c11a7bbfa5d
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111896470"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>IoT Hub への Azure Storage アカウントの関連付け

IoT ハブに関連付けられた Azure Storage アカウントを持っている必要があります。 作成方法については、「[ストレージ アカウントを作成する](../articles/storage/common/storage-account-create.md)」を参照してください。 Azure Storage アカウントを IoT ハブに関連付けると、IoT ハブによって SAS URI が生成されます。 デバイスは、この SAS URI を使って安全にファイルを BLOB コンテナーにアップロードすることができます。 SAS URI を生成し、デバイスでファイルのアップロードに使用できるようにするプロセスは、IoT Hub サービスとデバイス SDK によって調整されます。

## <a name="create-a-container"></a>コンテナーを作成する

次の手順に従って、ストレージ アカウント用の BLOB コンテナーを作成します。

1. ストレージ アカウントの左ペインで、 **[データ ストレージ]** の下の **[コンテナー]** を選択します。
1. [コンテナー] ブレードで、 **[+ コンテナー]** を選択します。
1. 開いた **[新しいコンテナー]** ペインで、コンテナーに名前を付け、 **[作成]** を選択します。

コンテナーを作成したら、「[Azure portal を使用してファイルのアップロードを構成する](../articles/iot-hub/iot-hub-configure-file-upload.md)」の手順に従います。 ご利用の IoT ハブに BLOB コンテナーが関連付けられていること、またファイル通知が有効になっていることを確認します

![ポータルでファイル通知を有効にする](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
