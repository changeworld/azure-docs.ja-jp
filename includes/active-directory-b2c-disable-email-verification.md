---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126745"
---
## <a name="disable-email-verification"></a>メールの確認を無効にする

既定で、Azure Active Directory B2C (Azure AD B2C) では、顧客のローカル アカウント (メール アドレスまたはユーザー名を使用してサインアップしたユーザーのアカウント) のメール アドレスが検証されます。 Azure AD B2C では、メール アドレスが有効であることを確認するために、サインアップ時に顧客が検証することを必須にしています。 また、悪意のあるアクターが自動化されたプロセスを使用してアプリケーションに不正なアカウントを生成することを防ぐことができます。

アプリケーション開発者によっては、サインアップ プロセス時にはメールの検証をスキップし、代わりに後で顧客にメール アドレスを検証させることを好みます。 それをサポートするために、電子メールの検証を無効にするように Azure AD B2C を構成することができます。 そうすることで、サインアップ プロセスがよりスムーズになり、開発者はメール アドレスを検証した顧客と検証していない顧客を柔軟に区別することができます。

> [!WARNING]
> サインアップ プロセスでの電子メールの検証を無効にすると、スパムにつながる場合があります。 既定の Azure AD B2C で提供されるメールの検証を無効にする場合は、代替の検証システムを実装することをお勧めします。
