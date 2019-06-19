---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/16/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 3076065d23d4701711c0ecb60349e77ee5c9b7c8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181390"
---
Azure Storage では、Microsoft マネージド キーまたはカスタマー マネージド キーのいずれかを使用する保存時の暗号化をサポートしています。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、および取り消しを行うことができます。

キーの管理およびキーの使用状況の監査には、Azure Key Vault を使います。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 ストレージ アカウントとキー コンテナーは同じリージョンに存在していることが必要です。ただし、サブスクリプションは異なっていてもかまいません。 Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../articles/key-vault/key-vault-overview.md)
