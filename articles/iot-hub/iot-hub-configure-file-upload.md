---
title: Azure Portal を使用してファイルのアップロードを構成する | Microsoft Docs
description: Azure Portal を使用して IoT Hub を構成し、接続されているデバイスからファイルのアップロードを有効にする方法。 対象の Azure ストレージ アカウントの構成に関する情報が含まれています。
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: lizross
ms.openlocfilehash: 5871ffdc16fe7c3e66c6915c72198250823d817e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555283"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Azure Portal を使用して IoT Hub ファイルのアップロードを構成する

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

この記事では、Azure portal を使用して IoT ハブ上でファイルのアップロードを構成する方法を示します。 

[IoT Hub でファイルのアップロード機能](iot-hub-devguide-file-upload.md)を使用するには、最初に Azure ストレージ アカウントと BLOB コンテナーを IoT ハブに関連付ける必要があります。 IoT Hub により、ファイルをアップロードするときにデバイスで使用する、この BLOB コンテナーへの書き込みアクセス許可を含む SAS URI が自動的に生成します。 ストレージ アカウントと BLOB コンテナーに加えて、SAS URI の有効期限と、Azure Storage で IoT Hub が使用する認証の種類を設定できます。 IoT Hub でバックエンド サービスに配信できる、オプションのファイル アップロード通知の設定を構成することもできます。

## <a name="prerequisites"></a>前提条件

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* Azure IoT Hub。 IoT ハブがない場合は、[ポータルを使用して IoT ハブを作成する](iot-hub-create-through-portal.md)に関するページを参照してください。

## <a name="configure-your-iot-hub"></a>IoT Hub を構成する

1. [Azure portal](https://portal.azure.com) で、目的の IoT ハブに移動し、 **[ファイルのアップロード]** を選択して、ファイルのアップロード プロパティを表示します。 次に、 **[ストレージ コンテナー設定]** で **[Azure Storage コンテナー]** を選択します。

    :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-settings.png" alt-text="ポータルでファイルのアップロード設定を構成する方法を示すスクリーンショット。":::

1. 現在のサブスクリプションで Azure Storage アカウントと BLOB コンテナーを選択して、IoT ハブに関連付けます。 必要に応じて、 **[ストレージ アカウント]** ペインで Azure Storage アカウントを作成し、 **[コンテナー]** ペインで BLOB コンテナーを作成できます。

   :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-container-selection.png" alt-text="ファイルのアップロード用のストレージ コンテナーを表示する方法を示すスクリーンショット。":::

1. Azure Storage アカウントと BLOB コンテナーを選択したら、残りのファイルのアップロード プロパティを構成します。

    * **Receive notifications for uploaded files (アップロードされたファイルに関する通知を受け取る)** : トグル ボタンを使用して、ファイルのアップロードに関する通知を有効または無効にします。

    * **SAS TTL**: IoT Hub によりデバイスに返される SAS URI の有効期間を設定します。 既定で 1 時間に設定されますが、スライダーを使用して、他の値にカスタマイズできます。

    * **[ファイル通知の設定] の [既定の TTL]** : 有効期限が切れるまでのファイルのアップロード通知の有効期間。 既定で 1 時間に設定されますが、スライダーを使用して、他の値にカスタマイズできます。

    * **File notification maximum delivery count (ファイルの通知設定 最大配信回数)** : IoT Hub がファイルのアップロード通知の配信を試行する回数です。 既定で 10 に設定されますが、スライダーを使用して、他の値にカスタマイズできます。

    * **認証の種類**: 既定では、Azure IoT Hub はキーベースの認証を使用して Azure Storage に接続し、認証します。 ユーザー割り当てまたはシステム割り当てのマネージド ID を構成して、Azure Storage で Azure IoT Hub を認証することもできます。 マネージド ID は、Azure AD で自動的に管理される ID を Azure サービスに安全な方法で提供します。 マネージド ID の構成方法については、「[IoT Hub でのマネージド ID のサポート](./iot-hub-managed-identity.md)」を参照してください。 Azure Storage アカウントと IoT ハブで 1 つ以上のマネージド ID を構成したら、そのうちの 1 つを、 **[システム割り当て]** または **[ユーザー割り当て]** ボタンを使用して Azure Storage での認証用に選択できます。

        > [!NOTE]
        > 認証の種類の設定は、IoT ハブが Azure Storage アカウントで認証する方法を構成します。 デバイスは常に、IoT ハブから取得した SAS URI を使用して、Azure Storage で認証されます。 

1. **[Save]\(保存\)** を選択して設定を保存します。 正常に完了したことを必ず確認してください。 一部の選択 (**認証の種類** など) は、設定を保存した後にのみ検証されます。 

## <a name="next-steps"></a>次のステップ

* [デバイス概要からファイルをアップロードする](iot-hub-devguide-file-upload.md)
* [IoT Hub でのマネージド ID のサポート](./iot-hub-managed-identity.md)
* [ファイル アップロードのハウツー ガイド](./iot-hub-csharp-csharp-file-upload.md)
