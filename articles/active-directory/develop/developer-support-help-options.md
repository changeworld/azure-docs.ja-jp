---
title: Microsoft ID プラットフォーム開発者向けのサポートとヘルプのオプション | Azure
description: Azure Active Directory (Azure AD) および Microsoft ID プラットフォームのその他のコンポーネントと統合される ID およびアクセス管理 (IAM) ソリューションを構築する際に役立つ情報を入手し、質問の答えを見つける方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/19/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: has-adal-ref
ms.openlocfilehash: 931d7385b9c7afe48d7c163aad3570c8485ae8d1
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122767889"
---
# <a name="support-and-help-options-for-developers"></a>開発者向けのサポート オプションとヘルプ オプション

ドキュメントに記載されていない問題を解決するために質問する必要がある場合は、専門家にご連絡ください。 ここでは、Microsoft ID プラットフォームと統合するアプリケーションを開発する際に、問題を解決するための推奨事項をいくつか紹介します。

## <a name="create-an-azure-support-request"></a>Azure サポート要求を作成する

<div class='icon is-large'>
    <img alt='Azure support' src='https://docs.microsoft.com/media/logos/logo_azure.svg'>
</div>

クラウドへの移行を始めたばかりの開発者の方も、ビジネスクリティカルで戦略的なアプリケーションの展開を行う大企業のお客様も、さまざな [Azure サポート オプションをご確認し、最適なプランをお選びください](https://azure.microsoft.com/support/plans)。 Azure ユーザーは、Azure portal でサポート要求を作成し、管理できます。

- Azure サポート プランをお持ちの場合は、[こちらからサポート要求をオープン](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)します。

- Azure のお客様でない場合は、 [ビジネス向け Microsoft サポート](https://support.serviceshub.microsoft.com/supportforbusiness)を使用してサポート リクエストを開くことができます。

## <a name="post-a-question-to-microsoft-qa"></a>Microsoft Q&A に質問を投稿する

<div class='icon is-large'>
    <img alt='Microsoft Q&A' src='./media/common/question-mark-icon.png'>
</div>             

ID アプリの開発に関する質問については、Microsoft のエンジニア、Azure Most Valuable Professionals (MVP)、およびエキスパー トコミュニティのメンバーから直接回答が得られます。

[Microsoft Q&A](/answers/products/) は、Azure のコミュニティ サポートの推奨される情報源です。

Microsoft Q&A を検索しても問題に対する回答が見つからない場合は、新しい質問を送信します。 [質の高い質問](/answers/articles/24951/how-to-write-a-quality-question.html)をするときは、次のタグのいずれかを使います。

| コンポーネント/領域| Tags  |
|------------|---------------------------|
| Active Directory Authentication Library (ADAL)                              | [[adal]](/answers/topics/azure-ad-adal-deprecation.html)                |
| Microsoft Authentication Library (MSAL)                                     | [[msal]](/answers/topics/azure-ad-msal.html)                            |
| Open Web Interface for .NET (OWIN) ミドルウェア                               | [[azure-active-directory]](/answers/topics/azure-active-directory.html) |
| [Azure AD B2B / 外部 ID](../external-identities/what-is-b2b.md) | [[azure-ad-b2b]](/answers/topics/azure-ad-b2b.html)                     |
| [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](/answers/topics/azure-ad-b2c.html)                     |
| [Microsoft Graph API](https://developer.microsoft.com/graph/)               | [[azure-ad-graph]](/answers/topics/azure-ad-graph.html)                 |
| 他のすべての認証と承認の分野                            | [[azure-active-directory]](/answers/topics/azure-active-directory.html) |

## <a name="create-a-github-issue"></a>GitHub の issue を作成する

<div class='icon is-large'>
    <img alt='GitHub-image' src='./media/common/github.svg'>
</div>

Microsoft 認証ライブラリ (MSAL) のいずれかに関するヘルプが必要な場合は、GitHub のリポジトリで問題を開きます。

| MSAL ライブラリ | GitHub の問題の URL|
| --- | --- |
| MSAL for Android | https://github.com/AzureAD/microsoft-authentication-library-for-android/issues |
| MSAL Angular | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |
| iOS および macOS 用の MSAL| https://github.com/AzureAD/microsoft-authentication-library-for-objc/issues |
| MSAL Java | https://github.com/AzureAD/microsoft-authentication-library-for-java/issues |
| MSAL.js | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |
| MSAL.NET| https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues |
| MSAL Node | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |
| MSAL Python | https://github.com/AzureAD/microsoft-authentication-library-for-python/issues |
| MSAL React | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |

## <a name="stay-informed-of-updates-and-new-releases"></a>更新プログラムと新しいリリースに関する最新情報を入手する

<div class='icon is-large'>
    <img alt='Stay informed' src='https://docs.microsoft.com/media/common/i_blog.svg'>
</div>

- [Azure の更新情報](https://azure.microsoft.com/updates/?category=identity): 重要な製品の更新プログラム、ロードマップ、および発表について確認できます。

- [ドキュメントの最新情報](./whats-new-docs.md): Microsoft ID プラットフォームのドキュメントの最新情報を確認できます。

- [Azure Active Directory ID ブログ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/bg-p/Identity): Azure AD に関するニュースと情報を入手できます。

- [技術コミュニティ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/bg-p/Identity/): 経験を共有し、エキスパートとの交流を通じて学ぶことができます。