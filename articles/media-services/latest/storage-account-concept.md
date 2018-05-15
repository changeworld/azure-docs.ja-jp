---
title: Azure Media Services のストレージ アカウント | Microsoft Docs
description: この記事では、Media Services がストレージ アカウントを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6d4c21867b0b46508f348300ae2b9553a75d23b2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="storage-accounts"></a>ストレージ アカウント

Media Services アカウントの作成では、Azure Storage アカウント リソースの名前を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。 

1 つの**プライマリ** ストレージ アカウントを持つ必要があります。Media Services アカウントに関連付けられた任意の数の **セカンダリ** ストレージ アカウントを持つことができます。 Media Services は、**汎用 v2** (GPv2) アカウントまたは**汎用 v1** (GPv1) アカウントをサポートします。 

>[!NOTE]
> BLOB のみのアカウントを**プライマリ**として使用することはできません。 

ホット ストレージ層とクール ストレージ層の選択を活用できるように、GPv2 を使用することをお勧めします。 ストレージ アカウントの詳細については、「[Azure Storage アカウントの種類](../../storage/common/storage-account-options.md)」を参照してください。 

## <a name="assets-in-a-storage-account"></a>ストレージ アカウント内の資産

Media Services v3 では、Storage API を使用してファイルをアップロードします。 Storage API と Media Services を使用して入力ファイルをアップロードする方法を確認するには、「[ローカル ファイルからジョブの入力を作成する](job-input-from-local-file-how-to.md)」を参照してください。 

> [!Note]
> Media Service API を使用せずに Media Services SDK によって生成された BLOB コンテナーの内容は、変更しないでください。

## <a name="next-steps"></a>次の手順

ストレージ アカウントを Media Services アカウントに関連付ける方法については、[アカウントの作成](create-account-cli-quickstart.md)に関する記事を参照してください。
