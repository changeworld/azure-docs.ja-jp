---
title: "Azure Cloud Shell (プレビュー) の PowerShell でファイルを永続化する | Microsoft Docs"
description: "Azure Cloud Shell でファイルを永続化する方法についてのチュートリアルです。"
services: azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: 74488b85ec524e4ad4c06a639a16ddbfd54b3154
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Azure Cloud Shell (プレビュー) の PowerShell の仕組み
Cloud Shell (プレビュー) の PowerShell は、次の方法を使ってファイルを永続化します。 
* ファイル共有を直接操作できるように、指定された Azure ファイル共有を `$Home` ディレクトリに `clouddrive` としてマウントします。

## <a name="list-cloud-drive-azure-file-shares"></a>クラウド ドライブ ファイル共有の一覧の取得
`Get-CloudDrive` コマンドは、Cloud Shell のクラウド ドライブによって現在マウントされている Azure ファイル共有の情報を取得します。 <br>
![Get-CloudDrive の実行](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>クラウド ドライブのマウント解除
Cloud Shell にマウントされた Azure ファイル共有は、いつでもマウントを解除することができます。 Azure ファイル共有が削除された場合は、次回のセッション時に新しい Azure ファイル共有を作成してマウントするように求められます。

`Dismount-CloudDrive` コマンドは、現在のストレージ アカウントから Azure ファイル共有をマウント解除します。 クラウド ドライブをマウント解除すると、現在のセッションが終了します。 次のセッションでは、新しい Azure ファイル共有を作成してマウントするように求められます。
![Dismount-CloudDrive の実行](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>次の手順
[PowerShell のクイックスタート](quickstart-powershell.md) <br>
[Azure Files について](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[ストレージのタグについて](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>