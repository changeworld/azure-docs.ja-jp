---
title: 'Azure Active Directory B2C: 脅威の管理 | Microsoft Docs'
description: Azure Active Directory B2C でのサービス拒否攻撃やパスワード攻撃を検出して軽減する手法について説明します。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/27/2016
ms.author: davidmu
ms.openlocfilehash: 5ab699b0dccd772ec905699d94dedaca0eefcdad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C: 脅威の管理

脅威の管理には、システムおよびネットワークを攻撃から保護する計画が含まれています。 サービス拒否攻撃により、目的のユーザーがリソースを使用できなくなることがあります。 パスワード攻撃は、リソースへの不正アクセスにつながります。 Azure Active Directory B2C (Azure AD B2C) には、これらの脅威から複数の方法でデータを保護するうえで役立つ組み込み機能があります。

## <a name="denial-of-service-attacks"></a>サービス拒否攻撃

Azure AD B2C は、SYN cookie や速度と接続の制限などの検出と軽減策の手法を使用して、サービス拒否攻撃から基になるリソースを保護します。

## <a name="password-attacks"></a>パスワード攻撃

Azure AD B2C は、パスワード攻撃に対する軽減策も備えています。 軽減策は、ブルート フォース パスワード攻撃とディクショナリ パスワード攻撃の両方に対処します。 ユーザーが設定したパスワードには、合理的な複雑さが必要です。 Azure AD B2C では、さまざまな信号を使用して、要求の整合性を分析します。 Azure AD B2C は、ハッカーやボットネットから目的のユーザーをインテリジェントに区別するように設計されています。 Azure AD B2C は、攻撃の公算に応じて、入力したパスワードに基づいてアカウントをロックする高度な戦略を提供します。

詳細については、[Microsoft セキュリティ センター](https://www.microsoft.com/trustcenter/security/threatmanagement)を参照してください。
