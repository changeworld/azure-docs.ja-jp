---
title: 証明書の使用 - Azure Batch
description: 証明書を使用してアプリケーションの認証を有効にする
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 4da5fad63b148fa054eefb7f13424b46dc43bf29
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83764321"
---
# <a name="using-certificates-with-batch"></a>Batch での証明書の使用

現在、Batch で証明書を使用する主な理由は、エンドポイントで認証する必要があるアプリケーションをプールで実行している場合です。 

まだ証明書がない場合は、`makecert` コマンドライン ツールを使用して自己署名証明書を作成できます。

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Azure portal から証明書を手動でアップロードする

1. 証明書をアップロードする先の Batch アカウントから、 **[証明書]** を選択してから **[追加]** を選択します。 

2. 拡張子が .pfx または .cer の証明書をアップロードします。 

アップロードが完了すると、証明書が証明書一覧に追加され、拇印を確認できます。

これで、Batch プールを作成する際に、プール内で [証明書] に移動し、アップロードした証明書をそのプールに割り当てることができます。

## <a name="next-steps"></a>次のステップ

Batch には証明書 API の [AZ batch certificate create](https://docs.microsoft.com/cli/azure/batch/certificate?view=azure-cli-latest#az-batch-certificate-create) があります。

Key Vault の使用方法の詳細については、「[Batch で Key Vault に安全にアクセスする](credential-access-key-vault.md)」を参照してください。
