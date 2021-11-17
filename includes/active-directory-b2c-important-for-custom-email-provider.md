---
author: kengaderdus
ms.service: active-directory-b2c
ms.topic: include
ms.date: 10/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 18ce90a5c108dc6ff1b6a903e6fe113d33ad4797
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179512"
---
> [!IMPORTANT]
> 次の手順では、カスタム ポリシー XML ファイルをビルドする方法を示します。 GitHub で入手できる **[サンプルのカスタム メール確認](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol/policy)** カスタム ポリシーを使用することをお勧めします。 `DisplayControl_TrustFrameworkExtensions.xml` では基本ファイルとして `TrustFrameworkExtensions.xml` を使用するため、**SocialAndLocalAccounts** [スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)の `TrustFrameworkBase.xml`、`TrustFrameworkLocalization.xml`、`TrustFrameworkExtensions.xml` ファイルを必ずポリシーに含めてください。