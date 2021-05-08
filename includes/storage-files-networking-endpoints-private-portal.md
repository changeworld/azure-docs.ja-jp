---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 5/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09723030f0da0252120f66f36347cad2e3a3546a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93375875"
---
プライベート エンドポイントを作成するストレージ アカウントに移動します。 ストレージ アカウントの目次で **[プライベート エンドポイント接続]** を選択した後、 **[+ プライベート エンドポイント]** を選択して、新しいプライベート エンドポイントを作成します。 

[![ストレージ アカウントの目次にある [プライベート エンドポイント接続] 項目のスクリーンショット](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

表示されるウィザードには、設定するページが複数あります。

**[基本]** ブレードで、プライベート エンドポイントの目的のリソース グループ、名前、リージョンを選択します。 これらには任意の内容を指定でき、ストレージ アカウントと一致している必要はありませんが、プライベート エンドポイントは、そのプライベート エンドポイントを作成しようとしている仮想ネットワークと同じリージョン内に作成する必要があります。

![[プライベート エンドポイントの作成] セクションの [基本] セクションのスクリーンショット](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

**[リソース]** ブレードで、 **[マイ ディレクトリ内の Azure リソースに接続します]** のラジオ ボタンを選択します。 **[リソースの種類]** で、リソースの種類として **[Microsoft.Storage/storageAccounts]** を選択します。 **[リソース]** フィールドは、接続先の Azure ファイル共有を含むストレージ アカウントです。 これは Azure Files 向けのため、ターゲット サブリソースは **[ファイル]** です。

**[構成]** ブレードでは、プライベート エンドポイントを追加する特定の仮想ネットワークとサブネットを選択できます。 上でサービス エンドポイントを追加したサブネットから個別のサブネットを選択する必要があります。 [構成] ブレードには、プライベート DNS ゾーンを作成または更新するための情報も含まれています。 既定の `privatelink.file.core.windows.net` ゾーンを使用することをお勧めします。

![[構成] セクションのスクリーンショット](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

**[確認および作成]** をクリックしてプライベート エンドポイントを作成します。 
