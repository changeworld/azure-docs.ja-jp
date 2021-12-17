---
title: Azure Stack Edge Pro GPU ストレージ アカウント管理 | Microsoft Docs
description: Azure portal を使用して Azure Stack Edge Pro GPU のストレージ アカウントを管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/13/2021
ms.author: alkohli
ms.openlocfilehash: 3c5cb61bf7450ea4668c6368d75615cdf43c2c40
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195874"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge-pro-gpu"></a>Azure portal を使用して Azure Stack Edge Pro GPU の Edge ストレージ アカウントを管理する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro の Edge ストレージ アカウントを管理する方法について説明します。 Azure Stack Edge Pro GPU の管理は、Azure portal またはローカル Web UI を通じて行うことができます。 Azure portal を使用して、デバイスの Edge ストレージ アカウントを追加または削除します。

## <a name="about-edge-storage-accounts"></a>Edge ストレージ アカウントについて

SMB、NFS、または REST プロトコルを使用して、Azure Stack Edge Pro GPU デバイスからデータを転送できます。 REST API を使用して BLOB ストレージにデータを転送するには、Edge ストレージ アカウントをデバイスに作成する必要があります。 

Azure Stack Edge Pro GPU デバイスに追加した Edge ストレージ アカウントは、Azure ストレージ アカウントにマップされます。 Edge ストレージ アカウントに書き込まれたデータは、自動的にクラウドにプッシュされます。

2 種類のアカウントと、これらの各アカウントから Azure へのデータの流れについて説明する図を次に示します。

![BLOB ストレージ アカウントの図](media/azure-stack-edge-gpu-manage-storage-accounts/ase-blob-storage.svg)

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Edge ストレージ アカウントを追加する
> * Edge ストレージ アカウントを削除する


## <a name="add-an-edge-storage-account"></a>Edge ストレージ アカウントを追加する

Edge ストレージ アカウントを作成するには、次の手順のようにします。

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Edge ストレージ アカウントを削除する

Edge ストレージ アカウントを削除するには、次の手順のようにします。

1. リソース内で **[構成] > [ストレージ アカウント]** にアクセスします。 ストレージ アカウントの一覧から、削除するストレージ アカウントを選択します。 上部のコマンド バーから **[ストレージ アカウントの削除]** を選択します。

    ![ストレージ アカウントの一覧に移動](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-1.png)

2. **[ストレージ アカウントの削除]** ブレードで、削除するストレージ アカウントを確認して **[削除]** を選択します。

    ![ストレージ アカウントの確認と削除](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-2.png)

ストレージ アカウントの一覧が更新され、削除が反映されます。


## <a name="add-delete-a-container"></a>コンテナーの追加と削除

これらのストレージ アカウントのコンテナーを追加または削除することもできます。

コンテナーを追加するには、次の手順を実行します。

1. 管理するストレージ アカウントを選択します。 上部のコマンド バーから **[+ コンテナーの追加]** を選択します。

    ![コンテナーを追加するストレージ アカウントの選択](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-1.png)

2. コンテナーの名前を指定します。 このコンテナーは、このアカウントにマップされている Azure ストレージ アカウントだけでなく、Edge ストレージ アカウントにも作成されます。 

    ![Edge コンテナーの追加](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-2.png)

コンテナーの一覧が更新され、新しく追加されたコンテナーが反映されます。

![更新されたコンテナーの一覧](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-4.png)

この一覧からコンテナーを選択して、上部のコマンド バーから **[+ コンテナーの削除]** を選択できるようになりました。 

![コンテナーを削除する](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>ストレージ キーの同期

各 Azure Storage アカウントには、2 つの 512 ビット ストレージ アクセス キーがあります。これらは、ストレージ アカウントにアクセスするときに認証の目的で使用されます。 Azure Stack Edge デバイスがクラウド ストレージ サービス プロバイダー (この場合は Azure) にアクセスするときは、これら 2 つのキーのどちらかを指定する必要があります。

Azure 管理者は、ストレージ アカウントに (Azure Storage サービスを介して) 直接アクセスすることによって、アクセス キーを再生成または変更できます。 Azure Stack Edge サービスとデバイスでは、この変更が自動的に表示されません。
 
Azure Stack Edge に変更を通知するには、Azure Stack Edge サービスにアクセスし、ストレージ アカウントにアクセスして、アクセス キーを同期する必要があります。 その後、サービスは最新のキーを取得し、キーを暗号化して、デバイスに送信します。 デバイスが新しいキーを取得すると、引き続き Azure Storage アカウントにデータを転送できます。 
 
デバイスに新しいキーを提供するには、Azure portal にアクセスし、ストレージ アクセス キーを同期します。 次の手順を実行します。 

1. リソース内で、管理するストレージ アカウントを選択します。 上部のコマンド バーから **[ストレージ キーの同期]** を選択します。

    ![ストレージ キーの同期を選択](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-1.png)

2. 確認を求められたら、 **[はい]** を選択します。

    ![ストレージ キーの同期を選択 2](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用してユーザーを管理する](azure-stack-edge-gpu-manage-users.md)方法について学習します。
