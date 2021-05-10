---
title: Azure NetApp Files のボリューム レプリケーションを作成する | Microsoft Docs
description: Azure NetApp Files のボリューム レプリケーション ピアリングを作成して、リージョン間レプリケーションを設定する方法について説明します。
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
ms.topic: how-to
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 2a3c788ce50ccc1d537fd2903fe05acffd079b0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591012"
---
# <a name="create-volume-replication-for-azure-netapp-files"></a>Azure NetApp Files のボリューム レプリケーションを作成する

> [!IMPORTANT]
> リージョン間レプリケーションの機能は、現在パブリック プレビュー段階です。 [Azure NetApp Files のリージョン間レプリケーションの順番待ち送信ページ](https://aka.ms/anfcrrpreviewsignup)から、機能にアクセスするための順番待ちリクエストを送信する必要があります。 Azure NetApp Files チームからの正式な確認メールを待ってからリージョン間レプリケーション機能を使用してください。

この記事では、レプリケーション ピアリングを作成してリージョン間レプリケーションを設定する方法について説明します。 

レプリケーション ピアリングを設定すると、ある Azure NetApp Files ボリューム (ソース) から別の別の Azure NetApp Files ボリューム (宛先) にデータを非同期的にレプリケートできます。 ソース ボリュームと宛先ボリュームは別々のリージョンにデプロイする必要があります。 ターゲットの容量プールのサービス レベルは、ソースの容量プールのものと一致させることも、別のサービス レベルを選択することもできます。   

現在、Azure NetApp Files レプリケーションでは複数のサブスクリプションがサポートされていません。レプリケーションはすべて、1 つのサブスクリプションで実行する必要があります。

開始する前に、[リージョン間レプリケーションを使用するための要件と考慮事項](cross-region-replication-requirements-considerations.md)を必ず確認してください。  

## <a name="locate-the-source-volume-resource-id"></a>ソース ボリュームのリソース ID を見つける  

レプリケートするソース ボリュームのリソース ID を取得する必要があります。 

1. ソース ボリュームに移動し、[設定] の下の **[プロパティ]** を選択して、ソース ボリュームのリソース ID を表示します。   
    ![ソース ボリュームのリソース ID を見つける](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. リソース ID をクリップボードにコピーします。  この情報は後で必要になります。

## <a name="create-the-data-replication-volume-the-destination-volume"></a>データ レプリケーション ボリューム (宛先ボリューム) を作成する

ソース ボリュームのデータのレプリケート先である宛先ボリュームを作成する必要があります。  宛先ボリュームを作成するには、宛先のリージョンに NetApp アカウントと容量プールを持っている必要があります。 

1. 宛先アカウントは、ソース ボリューム リージョンとは異なるリージョンにある必要があります。 必要に応じて、「[NetApp アカウントを作成する](azure-netapp-files-create-netapp-account.md)」の手順に従って、レプリケーションに使用する Azure リージョンに NetApp アカウントを作成します。   
別のリージョン内の既存の NetApp アカウントを選択することもできます。  

2. 必要に応じて、「[容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)」の手順に従って、新しく作成した NetApp アカウントに容量プールを作成します。   

    また、既存の容量プールを選択して、宛先ボリュームをホストすることもできます。  

    ターゲットの容量プールのサービス レベルは、ソースの容量プールのものと一致させることも、別のサービス レベルを選択することもできます。

3. 「[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)」の手順に従って、レプリケーションに使用するリージョン内でサブネットを委任します。

4. 宛先の NetApp アカウントの [ストレージ サービス] の下の **[ボリューム]** を選択して、データ レプリケーション ボリュームを作成します。 次に、 **[+ Add data replication]\(+データ レプリケーションの追加\)** ボタンをクリックします。  

    ![データ レプリケーションの追加](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. 表示された [ボリュームの作成] ページで、 **[基本]** タブの下にある次のフィールドを入力します。
    * ボリューム名
    * 容量プール
    * Volume quota (ボリューム クォータ)
        > [!NOTE] 
        > 宛先ボリュームのボリューム クォータ (サイズ) は、ソース ボリュームのものをミラーリングする必要があります。 ソース ボリュームよりも小さいサイズを指定すると、宛先ボリュームのサイズが自動的にソース ボリュームのサイズに変更されます。 
    * 仮想ネットワーク 
    * Subnet

    フィールドの詳細については、「[NFS ボリュームを作成する](azure-netapp-files-create-volumes.md#create-an-nfs-volume)」を参照してください。 

6. **[プロトコル]** タブで、ソース ボリュームと同じプロトコルを選択します。  
NFS プロトコルの場合、エクスポート ポリシー ルールが、エクスポートにアクセスするリモート ネットワーク内のすべてのホストの要件を満たしていることを確認します。  

7. **[タグ]** タブで、必要に応じてキーと値のペアを作成します。  

8. **[レプリケーション]** タブで、「[ソース ボリュームのリソース ID を見つける](#locate-the-source-volume-resource-id)」で取得したソース ボリュームのリソース ID を貼り付け、目的のレプリケーション スケジュールを選択します。 レプリケーション スケジュールのオプションには、10 分ごと、毎時間、毎日、毎週、毎月があります。  

    ![ボリューム レプリケーションを作成する](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. **[確認および作成]** をクリックし、 **[作成]** をクリックしてデータ レプリケーション ボリュームを作成します。   

    ![レプリケーションの確認と作成](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>ソース ボリュームからレプリケーションを承認する  

レプリケーションを承認するには、レプリケーションの宛先ボリュームのリソース ID を取得し、レプリケーションのソース ボリュームの [承認] フィールドに貼り付ける必要があります。 

1. Azure portal で、Azure NetApp Files に移動します。

2. レプリケーションの宛先ボリュームが配置されている宛先の NetApp アカウントと宛先の容量プールに移動します。

3. レプリケーションの宛先ボリュームを選択し、[設定] の下の **[プロパティ]** に移動して、宛先ボリュームの **[リソース ID]** を見つけます。 宛先ボリュームのリソース ID をクリップボードにコピーします。

    ![[プロパティ] のリソース ID](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. Azure NetApp Files で、レプリケーションのソース アカウントとソース容量プールに移動します。 

5. レプリケーションのソース ボリュームを見つけて選択します。 [ストレージ サービス] の下の **[レプリケーション]** に移動して、 **[承認]** をクリックします。

    ![レプリケーションの承認](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. [承認] フィールドに、手順 3. で取得した宛先レプリケーション ボリュームのリソース ID を貼り付け、 **[OK]** をクリックします。

## <a name="next-steps"></a>次のステップ  

* [リージョン間レプリケーション](cross-region-replication-introduction.md)
* [リージョン間レプリケーションを使用するための要件と考慮事項](cross-region-replication-requirements-considerations.md)
* [レプリケーション関係の正常性状態を表示する](cross-region-replication-display-health-status.md)
* [ボリューム レプリケーション メトリック](azure-netapp-files-metrics.md#replication)
* [ディザスター リカバリーの管理](cross-region-replication-manage-disaster-recovery.md)
* [ボリューム レプリケーションまたはボリュームを削除する](cross-region-replication-delete.md)
* [リージョン間レプリケーションのトラブルシューティング](troubleshoot-cross-region-replication.md)

