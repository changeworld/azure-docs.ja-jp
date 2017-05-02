---
title: "Azure B2C の脅威の管理 |Microsoft ドキュメント"
description: "DOS 攻撃とパスワード攻撃を検出し軽減する Azure B2C の手法です。"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: Ajith Alexander
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5cca5fcd445e70b4eab9197258bb783e20016f76
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad-b2c-threat-management"></a>Azure AD B2C: 脅威の管理
脅威の管理には、システムおよびネットワークの攻撃からの保護が含まれています。 サービス拒否 (DOS) を受けると、システムの可用性に影響が生じて目的のユーザーがリソースを使用できなくなります。 パスワード攻撃は、リソースへの不正アクセスにつながります。 Microsoft Azure Active Directory B2C には、これらの脅威から複数の方法でデータを保護する組み込み機能があります。 

## <a name="denial-of-service-attack"></a>サービス拒否攻撃

Azure AD B2C は、SYN cookie や速度と接続の制限などの検出と軽減策の手法を使用して、これらの攻撃から基になるリソースを保護します。  

## <a name="password-attacks"></a>パスワード攻撃

Azure AD B2C は、パスワード攻撃に対する軽減策も備えています。  この方法は、ブルート フォース パスワード攻撃とディクショナリ パスワード攻撃の両方に対処します。  ユーザーが設定したパスワードには、合理的な複雑さが必要です。  Azure AD B2C では、想定されるユーザーと、ハッカーやボットネットとの違いをインテリジェントに区別するために、要求の整合性をさまざまな信号を使用して分析します。 B2C は、攻撃の公算に応じて、入力したパスワードに基づいてアカウントをロックする高度な戦略を提供します。

[マイクロソフトの脅威の管理に関する詳細情報](https://www.microsoft.com/trustcenter/security/threatmanagement)
