---
title: Azure Stack Edge デバイスのリセットと再アクティブ化
description: ご利用の Azure Stack Edge デバイスのデータをワイプし、その後で再アクティブ化する方法について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/20/2021
ms.author: alkohli
ms.openlocfilehash: 4f37d453072277da4cb4ecade03cf3f58f28460e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005672"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-device"></a>Azure Stack Edge デバイスをリセットして再アクティブ化する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、デバイスで問題が発生した場合や、何らかの理由で最初からやり直す必要がある場合に、Azure Stack Edge デバイスをリセット、再構成、および再アクティブ化する方法について説明します。

デバイスをリセットしてデータを削除した後、デバイスを新しいリソースとして再アクティブ化する必要があります。 デバイスをリセットするとデバイスの構成が削除されるため、ローカル Web UI を使用してデバイスを再構成する必要があります。

たとえば、既存の Azure Stack Edge リソースを新しいサブスクリプションに移動する必要が生じる場合があります。 これを行うには、次のようにします。

1. 「[デバイスのリセット](#reset-device)」の手順に従って、デバイス上のデータをリセットします。
2. 既存のデバイスで新しいサブスクリプションを使用する新しいリソースを作成し、デバイスをアクティブ化します。 「[デバイスを再アクティブ化する](#reactivate-device)」の手順に従います。

## <a name="reset-device"></a>デバイスのリセット

デバイス上のデータ ディスクのデータを削除するには、ご利用のデバイスをリセットする必要があります。

リセットする前に、必要に応じて、デバイス上のローカル コピーを作成します。 デバイスから Azure Storage コンテナーにデータをコピーできます。

>[!IMPORTANT]
> デバイスをリセットすると、すべてのローカル データとワークロードがデバイスから消去されます。これは、元に戻すことができません。 デバイスを最初からやり直す場合にのみデバイスをリセットしてください。

ローカル Web UI または PowerShell でデバイスをリセットできます。 PowerShell の手順については、「[デバイスをリセットする](./azure-stack-edge-connect-powershell-interface.md#reset-your-device)」を参照してください。

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

## <a name="reactivate-device"></a>デバイスを再アクティブ化する

デバイスをリセットした後、デバイスを新しいリソースとして再アクティブ化する必要があります。 新しい注文を作成した後、新しいリソースを再構成して再アクティブ化する必要があります。

既存のデバイスを再アクティブ化するには、次の手順を実行します。

1. 「[新しいリソースを作成する](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)」の手順に従って、既存のデバイスについて新しい注文を作成します。 **[配送先住所]** タブで、 **[I already have a device]\(既にデバイスを持っています\)** を選択します。

   ![[配送先住所] に新しいデバイスを指定しない](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [アクティブ化キーを取得します](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#get-the-activation-key)。

1. [デバイスに接続します](azure-stack-edge-gpu-deploy-connect.md)。

1. [デバイスのネットワークを構成します](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。

1. [デバイス設定を構成します](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)。

1. [証明書を構成します](azure-stack-edge-gpu-deploy-configure-certificates.md)。

1. [デバイスをアクティブ化します](azure-stack-edge-gpu-deploy-activate.md)。

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge デバイスに接続する](azure-stack-edge-gpu-deploy-connect.md)方法を確認します。
