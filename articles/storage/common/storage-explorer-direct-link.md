---
title: Azure Storage Explorer の直接リンク | Microsoft Docs
description: Azure Storage Explorer の直接リンクのドキュメント
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 121df1e1c3ab6d0741d3e4da1144a86938da70ed
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582040"
---
# <a name="azure-storage-explorer-direct-link"></a>Azure Storage Explorer の直接リンク

Windows および macOS 上の Storage Explorer は `storageexplorer://` プロトコルを使用する URI をサポートしています。 これらの URI は直接リンクと呼ばれています。 直接リンクは、Storage Explorer の Azure Storage リソースを指します。 直接リンクをたどると、Storage Explorer が開き、指しているリソースに移動します。 この記事では、直接リンクのしくみと使用方法について説明します。

## <a name="how-direct-links-work"></a>直接リンクのしくみ

Storage Explorer には、Azure のリソースを視覚化するためにツリー ビューが使用されます。 直接リンクには、ツリー内のリンク先のリソース ノードの階層情報が含まれています。 直接リンクをたどると、Storage Explorer が開き、直接リンクのパラメーターが受信されます。 次に、Storage Explorer によってこれらのパラメーターが使用され、ツリー ビューでリンク先のリソースに移動します。

> [!IMPORTANT]
> 直接リンクが機能するには、サインインしていて、リンク先のリソースにアクセスするために必要なアクセス許可を持っている必要があります。

## <a name="parameters"></a>パラメーター

Storage Explorer の直接リンクは、常にプロトコル `storageexplorer://` で始まります。 次の表では、直接リンクで使用できる各パラメーターを説明しています。

パラメーター | 説明
:---------| :---------
`v`         | 直接リンク プロトコルのバージョン。
`accountid` | リンク先のリソースのストレージ アカウントの Azure Resource Manager リソース ID。 リンク先のリソースがストレージ アカウントの場合、この ID はそのストレージ アカウントの Azure Resource Manager リソース ID になります。 それ以外の場合、この ID は、リンク先のリソースが属するストレージ アカウントの Azure Resource Manager リソース ID になります。
`resourcetype` | 省略可能。 リンク先のリソースが BLOB コンテナー、ファイル共有、キュー、またはテーブルである場合にのみ使用されます。 "Azure.BlobContainer"、"Azure.FileShare"、"Azure.Queue"、"Azure.FileShare" のいずれかにする必要があります。
`resourcename` | 省略可能。 リンク先のリソースが BLOB コンテナー、ファイル共有、キュー、またはテーブルである場合にのみ使用されます。 リンク先のリソースの名前。

これは BLOB コンテナーへの直接リンクの例です。 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>Storage Explorer から直接リンクを取得する

Storage Explorer を使用して、リソースへの直接リンクを取得できます。 ツリー ビューで、リソースのノードのコンテキスト メニューを開きます。 次に、"直接リンクをコピー" アクションを使用して、直接リンクをクリップボードにコピーします。

## <a name="direct-link-limitations"></a>直接リンクの制限事項

直接リンクは、サブスクリプション ノード以下のリソースでのみサポートされます。 さらに、次のリソースの種類のみがサポートされます。

- ストレージ アカウント
- Blob コンテナー
- キュー
- ファイル共有
- テーブル
