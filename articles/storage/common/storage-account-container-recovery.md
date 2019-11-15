---
title: ストレージ アカウント コンテナーの回復
description: ストレージ アカウント コンテナーの回復
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
ms.openlocfilehash: f095bdfe7bbb5777a2ad2aabb3bda92d0974457d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693800"
---
# <a name="storage-account-container-recovery"></a>ストレージ アカウント コンテナーの回復

Azure Storage は、自動レプリカによるデータ回復性を備えています。 ただし、アプリケーション コードやユーザーが、誤ってまたは悪意を持ってデータを破損させるのを防ぐことはできません。 アプリケーション エラーやユーザー エラーが発生した場合にデータの忠実性を維持するには、監査ログと共にセカンダリ ストレージの場所にデータをコピーするなどの高度な手法が必要です。

## <a name="checking-azure-storage-account-type"></a>Azure Storage のアカウントの種類の確認

1. [Azure Portal](https://portal.azure.com/) に移動します。

2. 自分のストレージ アカウントを探します。

3. **[概要]** セクションで、 **[レプリケーション]** を確認します。

   ![Image](media/storage-account-container-recovery/1.png)

4. レプリケーションの種類が **GRS/RA-GRS** の場合、アカウント コンテナーの回復は保証なしで実行できます。 他のすべての種類のレプリケーションでは実行できません。

5. 次の情報を収集し、Microsoft サポートでサポート リクエストを提出します。

   * ストレージ アカウント名:
   * コンテナー名:
   * 削除の時刻:

   次の表は、レプリケーション戦略に応じて、ストレージ アカウント コンテナーの回復のスコープの概要を示しています。

   |コンテンツの種類|LRS|ZRS|GRS|RA - GRS| 
   |---|---|---|---|---|
   |ストレージ コンテナー|いいえ|いいえ|可能|はい| 

   * ストレージ アカウント コンテナーの復元を試みることができますが、保証されません。 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>回復を成功させるために実行してはいけないこと

* 同じ名前のコンテナーを再作成しないでください。  
* コンテナーを既に再作成している場合は、回復のサポート リクエストを提出する前に、コンテナーを削除する必要があります。

## <a name="steps-to-prevent-this-in-the-future"></a>今後これを回避するための手順

1. 今後これを回避するために使用することが最も推奨される機能は、[論理的な削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)です。

2. また、[スナップショット](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)機能もお勧めします。
 
## <a name="next-steps"></a>次の手順

この機能の 2 つのサンプル コードを次に示します。

  * [.NET での BLOB スナップショットの作成と管理](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [PowerShell での BLOB スナップショットの使用](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

