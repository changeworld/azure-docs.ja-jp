---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: b5f642eafa7792872bdd6f49141d2b6b1412cb78
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581324"
---
次のベスト プラクティスに注意してください。

- 忘れた場合にパスワードをリセットしなくても済むように、すべてのパスワードをセキュリティで保護された場所に格納することをお勧めします。 管理サービスは既存のパスワードを取得できません。 可能なのは、そのパスワードを Azure Portal 経由でリセットすることだけです。 パスワードをリセットする場合は、リセットする前に、必ずすべてのユーザーに通知してください。
- HTTP 経由でお使いのデバイスの Windows PowerShell インターフェイスにリモートでアクセスすることができます。 セキュリティのベスト プラクティスとして、信頼できるネットワークのみで HTTP を使用する必要があります。
- デバイスのパスワードが強力であり、適切に保護されていることを確認してください。 [パスワードのベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices#enable-password-management)に従ってください。