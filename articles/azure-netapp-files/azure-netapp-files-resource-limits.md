---
title: Azure NetApp Files のリソース制限 | Microsoft Docs
description: Azure NetApp Files リソースの制限と、リソース制限の引き上げを要求する方法について説明します。
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
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: b-juche
ms.openlocfilehash: 0b05b934b4dc57b4db941755cfcfb2f8d21fc2c6
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824976"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files のリソース制限

Azure NetApp Files のリソース制限を理解すると、ボリュームの管理に役立ちます。

## <a name="resource-limits"></a>リソース制限

次の表は、Azure NetApp Files のリソース制限について説明しています。

|  リソース  |  既定の制限  |  サポート要求による調整の可否  |
|----------------|---------------------|--------------------------------------|
|  [リージョンごとのサブスクリプションあたり容量クォータ](#regional-capacity-quota)   |  25 TiB  |  はい  |
|  サブスクリプションあたりの Azure リージョンごとの NetApp アカウント数  |  10    |  はい   |
|  NetApp アカウントあたりの容量プールの数   |    25     |   はい   |
|  サブスクリプションあたりのボリュームの数   |    500     |   はい   |
|  容量プールあたりのボリュームの数     |    500   |    はい     |
|  ボリュームあたりのスナップショット数       |    255     |    いいえ        |
|  Azure Virtual Network あたりの Azure NetApp Files (Microsoft.NetApp/volumes) に委任されたサブネットの数    |   1   |    いいえ    |
|  Azure NetApp Files を使用した VNet (直ちにピアリングされた VNet を含む) で使用されている IP の数   |    1000   |    いいえ   |
|  単一の容量プールの最小サイズ   |  4 TiB     |    いいえ  |
|  単一の容量プールの最大サイズ    |  500 TiB   |   いいえ   |
|  単一のボリュームの最小サイズ    |    100 GiB    |    いいえ    |
|  単一のボリュームの最大サイズ     |    100 TiB    |    いいえ    |
|  1 つのファイルの最大サイズ     |    16 TiB    |    いいえ    |    
|  1 つのディレクトリ内のディレクトリ メタデータの最大サイズ      |    320 MB    |    いいえ    |    
|  1 つのディレクトリに含まれるファイルの最大数  | "*約*" 400 万個。 <br> 「[ディレクトリのサイズが上限に近づいているかどうかの確認](#directory-limit)」を参照してください。  |    いいえ    |   
|  ボリュームあたりのファイルの最大数 ([maxfiles](#maxfiles))     |    1 億    |    はい    |    
|  ボリュームあたりのエクスポート ポリシー ルールの最大数     |    5  |    いいえ    | 
|  手動 QoS ボリュームに割り当てられた最小スループット     |    1 MiB/秒   |    No    |    
|  手動 QoS ボリュームに割り当てられた最大スループット     |    4,500 MiB/秒    |    No    |    
|  リージョン間レプリケーション データ保護ボリュームの数 (宛先ボリューム)     |    5    |    はい    |     

詳細については、「[容量管理に関する FAQ](azure-netapp-files-faqs.md#capacity-management-faqs)」を参照してください。

## <a name="determine-if-a-directory-is-approaching-the-limit-size"></a>ディレクトリのサイズが上限に近づいているかどうかの確認<a name="directory-limit"></a>  

クライアントから `stat` コマンドを使用することで、ディレクトリがディレクトリ メタデータのサイズ上限 (320 MB) に近づいているかどうかを確認できます。   

320 MB のディレクトリの場合、ブロック数は 655,360、各ブロック サイズは 512 バイトです  (つまり、320 x 1,024 x 1,024/512)。320 MB のディレクトリの場合、この数値は最大約 400 万ファイルに換算できます。 ただし、ASCII 以外の文字を含むファイルがディレクトリ内にどの程度存在するかといった要因によっては、実際の最大ファイル数が少なくなる場合があります。 そのため、次のように `stat` コマンドを使用して、ディレクトリが上限に近づいているかどうかを確認する必要があります。  

例 :

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```

## <a name="maxfiles-limits"></a>maxfiles の制限 <a name="maxfiles"></a> 

Azure NetApp Files ボリュームには、*maxfiles* という制限があります。 maxfiles の制限は、ボリュームに格納できるファイルの数です。 Linux ファイル システムは、*inode* で制限を参照します。 Azure NetApp Files ボリュームの maxfiles の制限には、ボリュームのサイズ (クォータ) に基づいてインデックスが作成されます。 ボリュームの maxfiles の制限は、プロビジョニングされたボリュームサイズの TiB ごとに 2000 万ファイルの割合で増減されます。 

サービスでは、プロビジョニングされたサイズに基づいて、ボリュームの maxfiles 制限が動的に調整されます。 たとえば、1 TiB のサイズで初期構成されたボリュームの maxfiles 制限は 2000 万です。 その後ボリュームのサイズを変更すると、以下の規則に基づいて、maxfiles の制限が自動的に再調整されます。 

|    ボリューム サイズ (クォータ)     |  maxfiles 制限の自動再調整    |
|----------------------------|-------------------|
|    1 TiB 以下                |    2,000 万     |
|    1 TiB 超、2 TiB 以下    |    4,000 万     |
|    2 TiB 超、3 TiB 以下    |    6,000 万     |
|    3 TiB 超、4 TiB 以下    |    8,000 万     |
|    4 TiB 超                 |    1 億    |

ボリュームに対して 4 TiB 以上のクォータを既に割り当てている場合は、[サポート リクエスト](#request-limit-increase)を開始して、maxfiles (inodes) の制限を 1 億より大きい値に増やすことができます。 1 億ファイル増加する (またはその一部) ごとに、対応するボリューム クォータを 4 TiB ずつ増やす必要があります。  たとえば、maxfiles の制限を 1 億ファイルから 2 億ファイル (またはその間の任意の数) に増やした場合は、ボリューム クォータを 4 TiB から 8 TiB に増やす必要があります。

ボリューム クォータが 20 TiB 以上の場合は、maxfiles の制限を 5 億に増やすことができます。 <!-- ANF-11854 --> 

## <a name="regional-capacity-quota"></a>リージョンごとの容量クォータ

Azure NetApp Files の [設定] にある **[クォータ]** をクリックすると、リージョンの現在のクォータ サイズと既定のクォータ サイズを表示することができます。 

例: 

![クォータの情報を表示する方法を示すスクリーンショット。](../media/azure-netapp-files/quota-display.png) 

[サポート リクエストを送信](#request-limit-increase)することで、追加コストをかけることなく、リージョンの容量クォータを引き上げることができます。 送信したサポート リクエストは、処理のために Azure 容量管理チームに送信されます。 通常、2 営業日以内に返信が届きます。 大規模なリクエストの場合は、Azure 容量管理チームから連絡が届く場合があります。  

リージョンごとの容量クォータを増やしても、課金は増えません。 課金はこれまで通り、プロビジョニングされた容量プールに基づいて行われます。
たとえば、現在 25 TiB のプロビジョニングされた容量がある場合は、35 TiB へのクォータの引き上げをリクエストできます。  2 営業日以内に、クォータの引き上げが要求されたリージョンに適用されます。 クォータの引き上げが適用された場合でも、現在プロビジョニングされている容量 (25 TiB) に対してのみ料金を支払います。 ただし、実際に追加の 10 TiB をプロビジョニングすると、35 TiB に対して請求されます。

Azure NetApp Files の現在の[リソース制限](#resource-limits)は変わりません。 引き続き 500 TiB の容量プールをプロビジョニングできます。 しかし、その前に、リージョンの容量クォータを 500 TiB に増やす必要があります。

## <a name="request-limit-increase"></a>上限の引き上げを要求する

「[リソース制限](#resource-limits)」の表から調整可能な上限を引き上げるように、Azure サポート リクエストを作成できます。 

1. **[サポートとトラブルシューティング]** にある **[新しいサポート リクエスト]** に移動します。
1. **[問題の説明]** タブで、要求の情報を入力します。
1. **[追加詳細]** タブの要求の詳細フィールドにある **[詳細を入力]** をクリックします。  

    ![[詳細] タブと [詳細の入力] フィールドを示すスクリーンショット。](../media/azure-netapp-files/quota-additional-details.png)

1. 表示される [クォータの詳細] ウィンドウで、以下を行います。  

    1. [クォータの種類] で、増やすリソースの種類を選択します。  
        例:  
        * *サブスクリプションあたりのリージョンの容量クォータ (TiB)*
        * *サブスクリプションあたりの Azure リージョンごとの NetApp アカウント数*
        * *サブスクリプションあたりのボリュームの数*

    1. [要求されたリージョン] で、自分のリージョンを選択します。   
        現在のサイズと既定のサイズが [クォータの状態] に表示されます。
    1. 指定したクォータの種類の引き上げを要求する値を入力します。
    
    ![リージョン クォータの引き上げを表示および要求する方法を示すスクリーンショット。](../media/azure-netapp-files/quota-details-regional-request.png)

1. **[次へ]** 、 **[確認および作成]** の順にクリックして要求を作成します。

## <a name="next-steps"></a>次のステップ  

- [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)
