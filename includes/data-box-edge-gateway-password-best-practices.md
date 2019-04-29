---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: a3b6616edbe1678cb18f78ec9025fc0286bf124f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684477"
---
次のベスト プラクティスに留意してください。

- 管理サービスで既存のパスワードを取得することはできません。このサービスでは、Azure portal を介して既存のパスワードをリセットすることのみを行えます。 パスワードを忘れた場合にリセットしなくても済むように、パスワードはすべて安全な場所に保管しておくことをお勧めします。 パスワードをリセットする場合は、リセットする前にすべてのユーザーに通知してください。
- HTTP 経由でお使いのデバイスの Windows PowerShell インターフェイスにリモートでアクセスすることができます。 セキュリティのベスト プラクティスとして、信頼できるネットワークのみで HTTP を使用する必要があります。
- デバイスのパスワードが強力であること、また適切に保護されていることを確認します。 [パスワードのベスト プラクティス](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management)に従ってください。