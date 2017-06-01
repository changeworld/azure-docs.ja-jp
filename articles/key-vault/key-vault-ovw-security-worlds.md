---
ms.assetid: 
title: "Azure Key Vault セキュリティ ワールド | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6477e8d91b71361ef91763418b22596deb216f02
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault セキュリティ ワールドと地理的境界

Azure Key Vault はマルチ テナント サービスであり、Azure の場所それぞれでハードウェア セキュリティ モジュール (HSM) のプールが使用されています。 

地理的に同じリージョンに属する Azure の場所にある HSM はすべて、同じ暗号化境界 (Thales セキュリティ ワールド) を共有しています。 たとえば、米国 geo ロケーションに属する米国東部と米国西部は、同じセキュリティ ワールドを使用します。 また、日本でもすべての Azure の場所が同じセキュリティ ワールドを共有しています。オーストラリア、インドなどの Azure の場所も同様です。 

1 つの Azure の場所のキー コンテナーから取得したキーのバックアップは、次の両方の条件を満たしていれば、別の Azure の場所のキー コンテナーに復元できます。

- Azure の場所の両方が同じ地理的な場所に属している
- キー コンテナーの両方が同じ Azure サブスクリプションに属している

たとえば、インド西部にあるキー コンテナーで、指定したキーのサブスクリプションによって取得されたバックアップは、同じサブスクリプションおよび geo ロケーション、つまりインド西部、インド中部、またはインド南部にある別のキー コンテナーにのみ復元できます。 



