---
title: ストレージ アカウント コンテナーの回復
description: ストレージ アカウント コンテナーの回復
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562277"
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
   |ストレージ コンテナー|いいえ|いいえ|はい|はい| 

   * ストレージ アカウント コンテナーの復元を試みることができますが、保証されません。 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>回復を成功させるために実行してはいけないこと

* 同じ名前のコンテナーを再作成しないでください。  
* コンテナーを既に再作成している場合は、回復のサポート リクエストを提出する前に、コンテナーを削除する必要があります。

## <a name="steps-to-prevent-this-in-the-future"></a>今後これを回避するための手順

1. 今後これを回避するために使用することが最も推奨される機能は、[論理的な削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)です。

2. また、[スナップショット](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)機能もお勧めします。
 
## <a name="next-steps"></a>次のステップ

この機能の 2 つのサンプル コードを次に示します。

  * [.NET での BLOB スナップショットの作成と管理](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [PowerShell での BLOB スナップショットの使用](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

