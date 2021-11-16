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
ms.date: 11/09/2021
ms.author: b-juche
ms.openlocfilehash: 1565ab6ecd0fe1c2f79237115c08f8d14091c158
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159010"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files のリソース制限

Azure NetApp Files のリソース制限を理解すると、ボリュームの管理に役立ちます。

## <a name="resource-limits"></a>リソース制限

次の表は、Azure NetApp Files のリソース制限について説明しています。

|  リソース  |  既定の制限  |  サポート要求による調整の可否  |
|----------------|---------------------|--------------------------------------|
|  [リージョンごとのサブスクリプションあたり容量クォータ](regional-capacity-quota.md)   |  25 TiB  |  はい  |
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
|  リージョン間レプリケーション データ保護ボリュームの数 (宛先ボリューム)     |    10    |    はい    |     
|  ボリュームあたりのポリシーベース (スケジュール) バックアップの最大数  | <ul><li> 日単位のリテンション期間数: 1 (最小) から 1019 (最大) </li> <li> 週単位のリテンション期間数: 1 (最小) から 1019 (最大) </li> <li> 月単位のリテンション期間数: 1 (最小) から 1019 (最大) </ol></li> <br> 日、週、月単位の最大バックアップ リテンション期間数の *合計* は 1019 です。  |  N  |
|  保護されたボリュームの最大サイズ  |  100 TiB  |  ×  |
|  サブスクリプションあたりのバックアップできるボリュームの最大数   |  5  |  Y  |
|  1 日あたりのボリュームあたりの手動バックアップの最大数 |  5  |  Y  |

詳細については、「[容量管理に関する FAQ](faq-capacity-management.md)」を参照してください。

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

## <a name="request-limit-increase"></a>上限の引き上げを要求する

「[リソース制限](#resource-limits)」の表から調整可能な上限を引き上げるように、Azure サポート リクエストを作成できます。 

1. **[サポートとトラブルシューティング]** にある **[新しいサポート リクエスト]** に移動します。   

2. **[問題の説明]** タブで、必要な情報を入力します。
    1. **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
    2. **[サブスクリプション]** で、ご使用のサブスクリプションを選択します。 
    3. **[クォータの種類]** で、 **[ストレージ: Azure NetApp Files の制限]** を選択します。  

    ![[問題の説明] タブを示すスクリーンショット。](../media/azure-netapp-files/support-problem-descriptions.png)

3. **[追加詳細]** タブの要求の詳細フィールドにある **[詳細を入力]** をクリックします。  

    ![[詳細] タブと [詳細の入力] フィールドを示すスクリーンショット。](../media/azure-netapp-files/quota-additional-details.png)

4. 制限の引き上げを要求するには、表示された [クォータの詳細] ウィンドウで次の情報を指定します。
    1. **[クォータの種類]** で、増やすリソースの種類を選択します。  
        例:   
        * *サブスクリプションあたりのリージョンの容量クォータ (TiB)*
        * *サブスクリプションあたりの Azure リージョンごとの NetApp アカウント数*
        * *サブスクリプションあたりのボリュームの数*

    2. **[要求されたリージョン]** で、自分のリージョンを選択します。   
        現在のサイズと既定のサイズが [クォータの状態] に表示されます。
    
    3. 指定したクォータの種類の引き上げを要求する値を入力します。
    
    ![リージョン クォータの引き上げを表示および要求する方法を示すスクリーンショット。](../media/azure-netapp-files/quota-details-regional-request.png)

5. **保存して続行** をクリックします。 **[確認と作成]** をクリックして要求を作成します。

## <a name="next-steps"></a>次のステップ  

- [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)
- [Azure NetApp Files のリージョンの容量クォータ](regional-capacity-quota.md)
- [Azure NetApp Files のリージョン アクセスを要求する](request-region-access.md)
