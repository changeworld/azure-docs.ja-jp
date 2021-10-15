---
title: Azure NetApp Files のボリュームに関するエラーをトラブルシューティングする | Microsoft Docs
description: Azure NetApp Files ボリュームに関するトラブルシューティングに役立つエラー メッセージと解決策について説明します。
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
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: a9df7577e1426fb355995cd4486338003a753c86
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129539016"
---
# <a name="troubleshoot-volume-errors-for-azure-netapp-files"></a>Azure NetApp Files のボリュームに関するエラーをトラブルシューティングする

この記事では、Azure NetApp Files ボリュームに関するトラブルシューティングに役立つエラー メッセージと解決策について説明します。 

## <a name="errors-for-volume-allocation"></a>ボリューム割り当てのエラー 

Azure NetApp Files で新しいボリュームを作成するか、既存のボリュームのサイズを変更すると、Microsoft Azure により、ストレージとネットワーク リソースがサブスクリプションに割り当てられます。 特定のリージョンでは、Azure サービスの需要がかつてないほど増加しているために、リソース割り当てのエラーが発生する場合があります。

このセクションでは、いくつかの一般的な割り当てエラーの原因を説明し、考えられる対処方法を提案します。

|     エラー条件    |     解決策    |
|-|-|
|新しいボリュームの作成時、または既存のボリュームのサイズ変更時にエラーが発生しました。 <br> エラー メッセージ: `There was a problem locating [or extending] storage  for the volume. Please retry the operation. If the problem persists, contact Support.` | このエラーは、この要求にサービスがリソースを割り当てようとしているときにエラーが発生したことを示します。 <br> しばらくしてから操作を再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。|
|通常のボリュームのリージョンのストレージまたはネットワークの容量の不足。 <br> エラー メッセージ: `There are currently insufficient resources available to create [or extend] a volume in this region. Please retry the operation. If the problem persists, contact Support.` | このエラーは、リージョンで使用可能なリソースが不足しているために、ボリュームの作成またはサイズ変更を行うことができないことを示します。 <br> 次のいずれかの回避策を使用します。 <ul><li>新しい VNet の下にボリュームを作成します。 こうすると、ネットワーク関連のリソース制限の超過が回避されます。</li> <li>しばらくしてからやり直してください。 その間にクラスター、リージョン、またはゾーンでリソースが解放されている可能性があります。</li></ul> |
|ネットワーク機能を `Standard` に設定してボリュームを作成しているときのストレージ容量の不足。 <br> エラー メッセージ: `No storage available with Standard network features, for the provided VNet.` | このエラーは、リージョンで使用可能なリソースが不足しているために、`Standard` ネットワーク機能を使用してボリュームを作成できないことを示します。 <br> 次のいずれかの回避策を使用します。 <ul><li>`Standard` ネットワーク機能が必要でない場合は、`Basic` ネットワーク機能を使用してボリュームを作成します。</li> <li>新しい VNet でボリュームを作成してみてください。 こうすると、ネットワーク関連のリソース制限の超過が回避されます。</li><li>しばらくしてからやり直してください。  その間にクラスター、リージョン、またはゾーンでリソースが解放されている可能性があります。</li></ul> |

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files の SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md) 
* [Azure NetApp Files のデュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md) 
* [ボリュームのネットワーク機能を構成する](configure-network-features.md)
