---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 86b18c2a6fc3fbf342f5b7a4b6b563432c605f73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "68781468"
---
次のベスト プラクティスに注意してください。

- 忘れた場合にパスワードをリセットしなくても済むように、すべてのパスワードをセキュリティで保護された場所に格納することをお勧めします。 管理サービスは既存のパスワードを取得できません。 可能なのは、そのパスワードを Azure Portal 経由でリセットすることだけです。 パスワードをリセットする場合は、リセットする前に、必ずすべてのユーザーに通知してください。
- HTTP 経由でお使いのデバイスの Windows PowerShell インターフェイスにリモートでアクセスすることができます。 セキュリティのベスト プラクティスとして、信頼できるネットワークのみで HTTP を使用する必要があります。
- デバイスのパスワードが強力であり、適切に保護されていることを確認してください。 [パスワードのベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices#enable-password-management)に従ってください。