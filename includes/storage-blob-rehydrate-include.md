---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: fc5f4d2c10cac23600025a72fedf7fe2cec5a34e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684079"
---
アーカイブ ストレージ内のデータを読み取るには、まず、BLOB の層をホットまたはクールに変更する必要があります。 このプロセスはリハイドレートと呼ばれ、完了までに数時間かかることがあります。 リハイドレートのパフォーマンスを最適化するために、サイズの大きい BLOB を使用することをお勧めします。 複数の小さな BLOB を同時にリハイドレートすると、余分に時間がかかる場合があります。 現在、High と Standard という 2 つのリハイドレート優先度があります。これは、[BLOB 層の設定](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)操作または [BLOB のコピー](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作に対するオプションの *x-ms-rehydrate-priority* プロパティを使用して設定できます。

* **Standard の優先度**:このリハイドレート要求は、受信した順序で処理され、最大で 15 時間かかることがあります。
* **優先度が高い**:このリハイドレート要求は、Standard の要求より優先され、1 時間未満で完了することがあります。 BLOB のサイズと現在の需要によっては、High の優先度の処理にかかる時間が 1 時間を超える場合があります。 High の優先度の要求は、Standard の優先度の要求より優先されることが保証されます。

> [!NOTE]
> Standard の優先度は、アーカイブでの既定のリハイドレート オプションです。 High の優先度は、Standard の優先度のリハイドレートよりもコストがかかる高速オプションのため、通常は緊急でデータを復元する状況において使用するために予約されています。

リハイドレート要求が開始された後は、キャンセルできません。 リハイドレート プロセス中、*x-ms-access-tier* BLOB プロパティは、リハイドレートがオンライン層まで完了するまでは、引き続きアーカイブとして表示されます。 リハイドレートのステータスと進捗状況を確認する目的で[、BLOB のプロパティの取得](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)を呼び出し、BLOB プロパティの *x-ms-archive-status* と *x-ms-rehydrate-priority* を確認できます。 アーカイブ ステータスは、リハイドレートの変更先の層に応じて "rehydrate-pending-to-hot" または "rehydrate-pending-to-cool" になります。 リハイドレートの優先度からは "High" または "Standard" のスピードが示されます。 完了時、アーカイブのステータスとリハイドレートの優先度が削除され、選択したホットまたはコールド層を反映するよう、アクセス層 BLOB プロパティが更新されます。
