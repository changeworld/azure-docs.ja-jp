---
title: Azure NetApp Files の LDAP ボリュームに関する問題をトラブルシューティングする | Microsoft Docs
description: Azure NetApp Files 用の LDAP ボリュームの構成時に発生する可能性のあるエラー状態の解決策について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: eea3f691bc6d91948dc73b4a02c89abfac12d384
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498883"
---
# <a name="troubleshoot-ldap-volume-issues"></a>LDAP ボリュームに関する問題のトラブルシューティング

この記事では、LDAP ボリュームの構成時に発生する可能性のあるエラー状態の解決方法について説明します。

## <a name="errors-and-resolutions-for-ldap-volumes"></a>LDAP ボリュームのエラーと解決策

|     エラー条件    |     解決策    |
|-|-|
| ldapEnabled を true に設定して SMB ボリュームを作成する際にエラーが発生しました: <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | LDAP を有効にして SMB ボリュームを作成することはできません。 <br> LDAP を無効にして SMB ボリュームを作成します。 |
| 既存のボリュームの ldapEnabled パラメーター値を更新中にエラーが発生しました: <br> `Error Message: ldapEnabled parameter is not allowed to update` |  ボリュームの作成後に、LDAP オプションの設定を変更することはできません。 <br> 作成されたボリュームでは、LDAP オプションの設定を更新しないでください。 詳細については、「[NFS ボリューム アクセスに拡張グループで ADDS LDAP を構成する](configure-ldap-extended-groups.md)」を参照してください。 |
| LDAP 対応の NFS ボリュームの作成中にエラーが発生しました: <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  このエラーは、DNS にアクセスできないために発生します。 <br> <ul><li> Azure NetApp Files 用に正しいサイト (サイトのスコープ) が構成されているかどうかを確認します。 </li><li> DNS にアクセスできない理由は、DNS IP アドレスが正しくないか、ネットワークの問題である可能性があります。 AD 接続で入力された DNS IP アドレスを調べて、それが正しいことを確認します。 </li><li> AD とボリュームが同じリージョンにあり、さらに同じ VNet 内にあることを確認します。 それらが異なる VNet 内にある場合は、その 2 つの VNet 間に VNet ピアリングが確立されていることを確認します。</li></ul> |
| スナップショットからのボリュームの作成時にエラーが発生しました: <br> `Aggregate does not exist` | Azure NetApp Files では、LDAP が無効になっているボリュームに属するスナップショットから、LDAP が有効になっている新しいボリュームのプロビジョニングがサポートされていません。 <br> 指定されたスナップショットから、LDAP が無効になっている新しいボリュームを作成してみてください。 |

## <a name="next-steps"></a>次の手順  

* [NFS ボリューム アクセスに拡張グループで ADDS LDAP を構成する](configure-ldap-extended-groups.md)
* [Azure NetApp Files の NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files のデュアルプロトコル (NFSv3 と SMB) ボリュームを作成する](create-volumes-dual-protocol.md)