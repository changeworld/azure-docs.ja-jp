---
title: Azure Cloud Shell (プレビュー) の PowerShell でファイルを永続化する | Microsoft Docs
description: Azure Cloud Shell でファイルを永続化する方法についてのチュートリアルです。
services: azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: b87c4a408393e4ae341898e8cfa23e9acbcb4fc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154400"
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Azure Cloud Shell (プレビュー) の PowerShell の仕組み
Cloud Shell (プレビュー) の PowerShell は、次の方法を使ってファイルを永続化します。 
* ファイル共有を直接操作できるように、指定された Azure ファイル共有を `$Home` ディレクトリに `clouddrive` としてマウントします。

## <a name="list-clouddrive-azure-file-shares"></a>`clouddrive` Azure ファイル共有の一覧表示
`Get-CloudDrive` コマンドは、Cloud Shell の `clouddrive` によって現在マウントされている Azure ファイル共有の情報を取得します。 <br>
![Get-CloudDrive の実行](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-clouddrive"></a>`clouddrive` のマウントを解除する
Cloud Shell にマウントされた Azure ファイル共有は、いつでもマウントを解除することができます。 Azure ファイル共有が削除された場合は、次回のセッション時に新しい Azure ファイル共有を作成してマウントするように求められます。

`Dismount-CloudDrive` コマンドは、現在のストレージ アカウントから Azure ファイル共有をマウント解除します。 `clouddrive` をマウント解除すると、現在のセッションが終了します。 次のセッションでは、新しい Azure ファイル共有を作成してマウントするように求められます。
![Dismount-CloudDrive の実行](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>次の手順
[PowerShell のクイックスタート](quickstart-powershell.md) <br>
[Azure Files について](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[ストレージのタグについて](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>