---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: JimacoMS3
ms.service: iot-hub
ms.topic: include
ms.date: 01/22/2019
ms.author: v-jbrannian
ms.custom: include file
ms.openlocfilehash: b4f390fe573e732bb01dde6ef2a63a6164d7f6e5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287623"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>IoT Hub への Azure Storage アカウントの関連付け

デバイスからファイルをアップロードするには、IoT ハブに関連付けられている Azure Storage アカウントと Azure Blob Storage コンテナーが必要です。 ストレージ アカウントとコンテナーを IoT ハブに関連付けると、デバイスから要求されたときに、その IoT ハブで SAS URI の要素を提供できます。 デバイスではその後、これらの要素を使用して SAS URI を構築でき、この URI を使用して Azure Storage との間で認証を行い、BLOB コンテナーにファイルをアップロードします。

Azure Storage アカウントを IoT ハブに関連付けるには:

1. IoT ハブの左側のペインにある **[Hub settings]\(ハブの設定\)** で、 **[ファイルのアップロード]** を選びます。

    :::image type="content" source="./media/iot-hub-include-associate-storage/select-storage.png" alt-text="ポータルから [ファイルのアップロード] 設定の選択を示すスクリーン キャプチャ。" border="true":::

1. **[ファイルのアップロード]** ペインで、 **[Azure Storage コンテナー]** を選択します。 この記事の場合は、ストレージ アカウントと IoT Hub を同じリージョンに配置することをお勧めします。
    * 使おうとしているストレージ アカウントが既にある場合は、一覧からそれを選択します。

    * 新しいストレージ アカウントを作成するには、 **[+ストレージ アカウント]** を選択します。 ストレージ アカウントの名前を指定し、 **[場所]** が、お使いの IoT ハブと同じリージョンに設定されていることを確認し、 **[OK]** を選択します。 IoT ハブと同じリソース グループに新しいアカウントが作成されます。 デプロイが完了したら、一覧からそのストレージ アカウントを選択します。

    ストレージ アカウントを選択すると、 **[コンテナー]** ペインが開きます。

1. **[コンテナー]** ペインで、BLOB コンテナーを選択します。
    * 使おうとしている BLOB コンテナーが既にある場合は、一覧からそれを選択し、 **[選択]** をクリックします。

    * 新しい BLOB コンテナーを作成するには、 **[+ コンテナー]** を選択します。 新しいコンテナーの名前を指定します。 この記事の目的上、他のフィールドはすべて既定のままにすることができます。 **［作成］** を選択します デプロイが完了したら、一覧からコンテナーを選択し、 **[選択]** をクリックします。

1. **[ファイルのアップロード]** ペインに戻り、ファイル通知が **[オン]** に設定されていることを確認します。 他の設定はすべて既定値のままにすることができます。 **[保存]** を選択し、設定が完了するのを待ってから次のセクションに進みます。

    :::image type="content" source="./media/iot-hub-include-associate-storage/file-upload-settings-small.png" alt-text="ポータルの [ファイルのアップロード] 設定の確認を示すスクリーン キャプチャ。" border="true":::

Azure Storage アカウントを作成する方法の詳細な手順については、「[ストレージ アカウントを作成する](../articles/storage/common/storage-account-create.md)」を参照してください。 ストレージ アカウントと BLOB コンテナーを IoT ハブに関連付ける方法の詳細な手順については、[Azure portal を使用してファイルのアップロードを構成する](../articles/iot-hub/iot-hub-configure-file-upload.md)ことに関するページを参照してください。
