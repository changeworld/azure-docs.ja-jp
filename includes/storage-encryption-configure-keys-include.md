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
ms.openlocfilehash: f846d75833b3a796e24fd23c5f841ea24a8d1876
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665910"
---
Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するために、顧客が管理するキーをストレージ アカウントのレベルで指定できます。

顧客が管理するキーは Azure Key Vault に格納する必要があります。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 ストレージ アカウントとキー コンテナーは同じリージョンに存在していることが必要です。ただし、サブスクリプションは異なっていてもかまいません。 Azure Storage ストレージの暗号化およびキー管理の詳細については、「[保存データに対する Azure Storage 暗号化](../articles/storage/common/storage-service-encryption.md)」を参照してください。 Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../articles/key-vault/key-vault-overview.md)
