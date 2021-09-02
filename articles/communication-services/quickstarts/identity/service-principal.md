---
title: Communication Services で Azure Active Directory を使用する
titleSuffix: An Azure Communication Services quickstart
description: Azure Active Directory を使用すると、Azure VM、関数アプリ、その他のリソースで実行されているアプリケーションからの Azure Communication Services へのアクセスを承認できます。
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: quickstart
ms.date: 06/30/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 3559dc9f7adc10f11e13e18468bbbd964bf5db04
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123253535"
---
# <a name="quickstart-authenticate-using-azure-active-directory"></a>クイック スタート: Azure Active Directory を使用して認証する

Azure Active Directory を使用して、Azure Communication Services を開始します。 Communication Services の ID および SMS SDK では、Azure Active Directory (Azure AD) 認証がサポートされています。

このクイックスタートでは、Active Directory をサポートする Azure 環境から、ID および SMS SDK へのアクセスを承認する方法について説明します。 作業用にサービス プリンシパルを作成し、開発環境でコードスをテストする方法についても説明しています。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成する](https://azure.microsoft.com/free)
- アクティブな Azure Communication Services リソース。ない場合は、「[Communication Services リソースを作成する](../create-communication-resource.md)」を参照してください。
- SMS を送信するには、[電話番号](../telephony-sms/get-phone-number.md)が必要です。
- 開発環境用のセットアップ サービス プリンシパルについては、[マネージド ID を使用したアクセスを承認する](./service-principal-from-cli.md)方法に関するページを参照してください。

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/active-directory/service-principal-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/active-directory/service-principal-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/active-directory/service-principal-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/active-directory/service-principal-python.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ

- [Azure のロールベースのアクセス制御の詳細について学習する](../../../../articles/role-based-access-control/index.yml)
- [.NET 用 Azure ID ライブラリの詳細について学習する](/dotnet/api/overview/azure/identity-readme)
- [ユーザー アクセス トークンを作成する](../../quickstarts/access-tokens.md)
- [SMS メッセージの送信](../../quickstarts/telephony-sms/send.md)
- [SMS に関する詳細](../../concepts/telephony-sms/concepts.md)
- [テスト用 ID の簡単作成](./quick-create-identity.md).

