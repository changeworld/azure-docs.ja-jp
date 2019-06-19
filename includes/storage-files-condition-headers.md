---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181243"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>ブラウザーから Azure Files を使用する Web アプリケーションの ConditionHeadersNotSupported エラー

条件ヘッダーを使用するアプリケーション (Web ブラウザーなど) を通して Azure Files でホストされているコンテンツにアクセスすると、アクセスが失敗し、ConditionHeadersNotSupported エラーと表示されます。

![ConditionHeaderNotSupported エラー](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>原因

条件ヘッダーはまだサポートされていません。 それらを実装しているアプリケーションでは、ファイルにアクセスするたびに、ファイル全体を要求する必要があります。

### <a name="workaround"></a>対処法

新しいファイルがアップロードされるとき、cache-control プロパティは既定で "no-cache" になっています。 アプリケーションで毎回ファイルを要求するようにするには、ファイルの cache-control プロパティを "no-cache" から "no-cache, no-store, must-revalidate" に更新する必要があります。 これは、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して実現できます。

![ストレージ エクスプローラーのコンテンツ キャッシュの変更](media/storage-files-condition-headers/storage-explorer-cache.png)