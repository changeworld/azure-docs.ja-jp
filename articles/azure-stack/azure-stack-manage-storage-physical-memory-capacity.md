---
title: Azure Stack の物理メモリ容量を管理する | Microsoft Docs
description: Azure Stack で使用できる記憶域スペースを管理します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: 0516ee7a8319b85765280b4c84f5febec8343ada
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52965617"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Azure Stack の物理メモリ容量を管理する

*適用対象: Azure Stack 統合システム*

Azure Stack に関して使用可能な総メモリ容量を増やすために、新たにメモリを増設することができます。 Azure Stack では、物理サーバーが "*スケール ユニット ノード*" と呼ばれることもあります。 単一のスケール ユニットに属しているすべてのスケール ユニット ノードには、同量のメモリが確保されている必要があります。

> [!note]  
> 先に進む前に、ご利用のハードウェア製造元のドキュメントを参照し、その製造元が、物理メモリのアップグレードをサポートしているかどうかを確かめてください。 ご利用の OEM ハードウェア ベンダーのサポート契約で、物理サーバー ラックの配置とデバイス ファームウェアの更新がベンダーに義務付けられている場合があります。

次のフロー図は、各スケール ユニット ノードにメモリを増設するための一般的なプロセスを示しています。

![個々のスケール ユニット ノードにメモリを増設](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>既存のノードへのメモリの増設
次の手順は、メモリの増設プロセスの概要を示しています。 

> [!Warning]  
OEM 提供のマニュアルを参照せずにこれらの手順を実行しないでください。

> [!Warning]  
スケール ユニット全体をシャットダウンしてください。メモリのローリング アップグレードはサポートされません。

1. 「[Azure Stack の開始および停止](azure-stack-start-and-stop.md)」の記事に記載の手順に従って Azure Stack を停止します。
2. ハードウェア製造元のドキュメントを参照して、各物理コンピューターのメモリをアップグレードします。
3. 「[Azure Stack の開始および停止](azure-stack-start-and-stop.md)」の記事の手順に従って Azure Stack を開始します。

## <a name="next-steps"></a>次の手順

 - ビジネスのニーズに基づいてストレージ容量を検索、回復、および回収するために、Azure Stack でストレージ アカウントを管理する方法については、「[Azure Stack でのストレージ アカウントの管理](azure-stack-manage-storage-accounts.md)」を参照してください。
 - Azure Stack デプロイの記憶域容量を監視および管理する Azure Stack クラウド オペレーター向けの情報については、「[Azure Stack のストレージ容量を管理する](azure-stack-manage-storage-shares.md)」を参照してください。 
