---
title: Azure Key Vault セキュリティ ワールド | Microsoft Docs
description: Azure Key Vault はマルチテナント サービスです。 各 Azure リージョンで HSM のプールを使用します。 地理的リージョン内のすべてのリージョンで、暗号化境界が共有されます。
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 0753108df52f0dcaea1d6c79eab266eeb000889a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747223"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault セキュリティ ワールドと地理的境界

Azure 製品は多くの [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)で利用でき、各 Azure 地域には 1 つ以上のリージョンが含まれています。 たとえば、ヨーロッパ地域には、北ヨーロッパと西ヨーロッパの 2 つのリージョンが含まれていますが、ブラジル地域の唯一のリージョンはブラジル南部です。

Azure Key Vault は、ハードウェア セキュリティ モジュール (HSM) のプールを使用するマルチ テナント サービスです。 地域内のすべての HSM は、"セキュリティ ワールド" と呼ばれる同じ暗号化境界を共有します。 すべての地域が 1 つのセキュリティ ワールドに対応し、その逆も同様です。

米国東部と米国西部は、地域 (米国) に属しているため、同じセキュリティ ワールドを共有します。 同様に、日本のすべての Azure リージョンは、同じセキュリティ ワールドを共有しています。オーストラリアのすべての Azure リージョンも同様です。

>[!NOTE]
> 例外として、米国防総省東部と米国防総省中部には、独自のセキュリティ ワールドがあります。

## <a name="backup-and-restore-behavior"></a>バックアップと復元動作

1 つの Azure リージョンのキー コンテナーから取得したキーのバックアップは、次の両方の条件を満たしていれば、別の Azure リージョンのキー コンテナーに復元できます。

- 両方の Azure リージョンが同じ地域に属している。
- 両方のキー コンテナーが同じ Azure サブスクリプションに属している。

たとえば、インド西部のキー コンテナー内のキーのバックアップを、インド地域 (インド西部、インド中部、インド南部の各リージョン) の同じサブスクリプション内の別のキー ボールトに復元できます。

## <a name="next-steps"></a>次のステップ

- [リージョン別の Microsoft 製品](https://azure.microsoft.com/regions/services/)を参照する
