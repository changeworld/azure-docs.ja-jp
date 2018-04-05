---
title: storage-files-create-storage-account-portal
description: Azure Files 用のストレージ アカウントの作成方法。
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 82e141eb4941dbd58be95e19dae186acd18caf94
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2018
---
ストレージ アカウントは、Azure ファイル共有またはその他のストレージ リソース (BLOB やキューなど) をデプロイできるストレージの共有プールです。 ストレージ アカウントに含まれる共有の数と、共有に格納できるファイル数には制限がなく、ストレージ アカウントの容量の上限まで増やすことができます。

ストレージ アカウントを作成するには、以下の手順を実行します。

1. 左側のメニューで、**+** をクリックしてリソースを作成します。
2. 検索ボックス (1) に「**ストレージ アカウント**」と入力し、**[ストレージ アカウント - Blob、File、Table、Queue]** (2) を選択してから、**[作成]** をクリックします。
    ![ストレージ アカウントの文字がリソース検索ダイアログに入力されたところを示すスクリーンショット](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. **[名前]** に「*mystorageacct*」と入力し、それに続けて、一意の名前であることを示す緑色のチェック マークが表示されるまでランダムな数字をいくつか入力します。 ストレージ アカウント名はすべて小文字で、グローバルに一意である必要があります。 ストレージ アカウント名は後で使用するため、メモします。 
4. **[デプロイ モデル]** で、既定値の **[Resource Manager]** をそのまま選択します。 Azure Resource Manager とクラシック デプロイメントの違いについて詳しくは、[デプロイ モデルとリソースの状態](../articles/azure-resource-manager/resource-manager-deployment-model.md)に関するページを参照してください。
5. **[アカウントの種類]** で **[StorageV2]** を選択します。 ストレージ アカウントの種類について詳しくは、[Azure ストレージ アカウント](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関するページを参照してください。
6. **[パフォーマンス]** で、既定値の **[Standard Storage]** をそのまま選択します。 現時点で Azure Files でサポートされているのは Standard Storage のみです。Premium Storage を選んでも、ファイル共有は Standard Storage に格納されます。
7. **[レプリケーション]** で *[ローカル冗長ストレージ (LRS)]* を選択します。 
8. **[安全な転送が必須]** では常に **[有効]** を選択することをお勧めします。 このオプションの詳細については、[転送中の暗号化](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関するページを参照してください。
9. **[サブスクリプション]** で、ストレージ アカウントを作成するサブスクリプションを選択します。 ご利用のサブスクリプションが 1 つだけの場合、それが既定値となります。
10. **[リソース グループ]** で **[新規作成]** を選択し、名前として「*myResourceGroup*」と入力します。
11. **[場所]**で **[米国東部]** を選択します。
12. **[仮想ネットワーク]** で既定のオプションである *[無効]* のままにします。 
13. **[ダッシュボードにピン留めする]** を選択して、ストレージ アカウントを見つけやすくします。
14. 終わったら、**[作成]** をクリックしてデプロイを開始します。