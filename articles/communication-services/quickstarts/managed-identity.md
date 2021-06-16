---
title: Communication Services でマネージド ID を使用する
titleSuffix: An Azure Communication Services quickstart
description: マネージド ID を使用すると、Azure VM、関数アプリ、その他のリソースで実行されているアプリケーションからの Azure Communication Services へのアクセスを承認できます。
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 05/27/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 3433aceb4bc9bda10c11602a7a45f28a5882f479
ms.sourcegitcommit: e832f58baf0b3a69c2e2781bd8e32d4f1ae932c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110585747"
---
# <a name="use-managed-identities"></a>マネージド ID の使用
マネージド ID を使用して、Azure Communication Services を開始します。 Communication Services の ID および SMS SDK では、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用した Azure Active Directory (Azure AD) 認証がサポートされています。

このクイックスタートでは、マネージド ID をサポートする Azure 環境から、ID および SMS SDK へのアクセスを承認する方法について説明します。 また、開発環境でコードをテストする方法についても説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成する](https://azure.microsoft.com/free)
- アクティブな Azure Communication Services リソース。ない場合は、「[Communication Services リソースを作成する](./create-communication-resource.md)」を参照してください。
- SMS を送信するには、[電話番号](./telephony-sms/get-phone-number.md)が必要です。
- 開発環境用のセットアップ マネージド ID については、「[マネージド ID を使用したアクセスを承認する](./managed-identity-from-cli.md)」を参照してください。

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ

- [Azure のロールベースのアクセス制御の詳細について学習する](../../../articles/role-based-access-control/index.yml)
- [.NET 用 Azure ID ライブラリの詳細について学習する](/dotnet/api/overview/azure/identity-readme)
- [ユーザー アクセス トークンを作成する](../quickstarts/access-tokens.md)
- [SMS メッセージの送信](../quickstarts/telephony-sms/send.md)
- [SMS に関する詳細](../concepts/telephony-sms/concepts.md)
