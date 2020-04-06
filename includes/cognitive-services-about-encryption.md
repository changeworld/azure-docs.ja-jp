---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372016"
---
## <a name="about-cognitive-services-encryption"></a>Cognitive Services の暗号化について

データは、[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) に準拠する [256 ビット AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 暗号化を使用して暗号化および暗号化解除されます。 暗号化と暗号化解除は透過的であり、暗号化とアクセスはユーザーによって管理されます。 データは既定でセキュリティ保護され、暗号化を利用するためにコードやアプリケーションを変更する必要はありません。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

サブスクリプションでは、Microsoft のマネージド暗号化キーが既定で使用されます。 カスタマー マネージド キーをサポートする価格レベルを使用している場合は、次の図に示すように、[Azure portal](https://portal.azure.com) の **[暗号化]** セクションでリソースの暗号化設定を確認できます。

![暗号化の設定を表示する](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Microsoft マネージド暗号化キーのみをサポートするサブスクリプションの場合、 **[暗号化]** セクションはありません。

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault でのカスタマー マネージド キー

ユーザー独自のキーを使用してサブスクリプションを管理するためのオプションもあります。 カスタマー マネージド キー (CMK、Bring Your Own Key (BYOK) とも呼ばれます) を使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。
