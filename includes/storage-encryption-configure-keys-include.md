---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895278"
---
Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するために、BLOB とファイル データの暗号化に使用する目的で、顧客が管理するキーを提供できます。

顧客が管理するキーは Azure Key Vault に格納する必要があります。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 ストレージ アカウントとキー コンテナーは同じリージョンに存在していることが必要です。ただし、サブスクリプションは異なっていてもかまいません。 Azure Storage ストレージの暗号化およびキー管理の詳細については、「[保存データに対する Azure Storage 暗号化](../articles/storage/common/storage-service-encryption.md)」を参照してください。 Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../articles/key-vault/key-vault-overview.md)
