---
title: Azure Active Directory B2C でリソースやデータに対する脅威を管理する | Microsoft Docs
description: Azure Active Directory B2C でのサービス拒否攻撃やパスワード攻撃を検出して軽減する手法について説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ee99e7346d438e81a0cd25f8c522838524732568
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912851"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でリソースやデータに対する脅威を管理する

Azure Active Directory (Azure AD) B2C には、リソースやデータに対する脅威から保護するうえで役立つ組み込み機能があります。 これらの脅威には、サービス拒否攻撃やパスワード攻撃が含まれます。 サービス拒否攻撃により、目的のユーザーがリソースを使用できなくなることがあります。 パスワード攻撃は、リソースへの不正アクセスにつながります。 

## <a name="denial-of-service-attacks"></a>サービス拒否攻撃

Azure AD B2C では、SYN Cookie を使用して SYN フラッド攻撃を防ぎます。 また、Azure AD B2C では、速度と接続の制限を使用して、サービス拒否攻撃から保護します。

## <a name="password-attacks"></a>パスワード攻撃

ユーザーが設定したパスワードには、合理的な複雑さが必要です。 Azure AD B2C は、パスワード攻撃に対する軽減策を備えています。 軽減策は、ブルート フォース パスワード攻撃とディクショナリ パスワード攻撃の両方に対処します。 Azure AD B2C では、さまざまな信号を使用して、要求の整合性を分析します。 Azure AD B2C は、ハッカーやボットネットから目的のユーザーをインテリジェントに区別するように設計されています。 

Azure AD B2C では高度な戦略を使用して、アカウントをロックします。 アカウントは、要求の IP と、入力したパスワードに基づいてロックされます。 また、ロックアウト期間は、攻撃されている可能性に応じて長くなります。 パスワードの試行に 10 回失敗した後、1 分間ロックアウトされます。 アカウントのロックが解除された後、次のログインに失敗すると、さらに 1 分間ロックアウトされ、ログインに失敗するごとにこのような状態が続きます。 同じパスワードを繰り返し入力した場合は、複数回失敗したログインとしてカウントされません。 

最初の 10 回のロックアウト期間は、1 分となります。 次の 10 回のロックアウト期間は若干長くなり、その後、10 回のロックアウト期間ごとに長くなります。 ロックアウト カウンターは、アカウントがロックされていない状態でログインに成功した後、0 にリセットされます。 ロックアウト期間は、最長で 5 時間続く場合があります。 

現時点では、以下のことを行うことはできません。

- 10 回未満のログインの失敗でロックアウトをトリガーする
- ロックアウトされたアカウントのリストを取得する
- ロックアウト ポリシーを構成する

詳細については、[Microsoft セキュリティ センター](https://www.microsoft.com/trustcenter/default.aspx)を参照してください。
