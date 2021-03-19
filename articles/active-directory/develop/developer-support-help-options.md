---
title: Azure AD アプリ開発者向けのサポート オプションとヘルプ オプション
description: Microsoft ID (Azure Active Directory および Microsoft アカウント) と連携するアプリケーションを作成するときの開発関連の質問や問題について、ヘルプとサポートを入手する方法について確認します
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 43990952f6cbe90c729ac2df421c682fe8d42b1b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517943"
---
# <a name="support-and-help-options-for-developers"></a>開発者向けのサポート オプションとヘルプ オプション

Azure Active Directory (Azure AD)、Microsoft ID、または Microsoft Graph API との連携を開始する場合や、アプリケーションに新しい機能を実装するときに、コミュニティからヘルプを入手し、開発者として使用できるサポートのオプションを把握しておく必要があります。 ここでは、Microsoft ID プラットフォーム ソリューションを開発する際に助けを求める方法を示します。

## <a name="create-an-azure-support-request"></a>Azure サポート要求を作成する

<div class='icon is-large'>
    <img alt='Azure support' src='https://docs.microsoft.com/media/logos/logo_azure.svg'>
</div>

クラウドへの移行を始めたばかりの開発者の方も、ビジネスクリティカルで戦略的なアプリケーションの展開を行う大企業のお客様も、さまざな [Azure サポート オプションをご確認し、最適なプランをお選びください](https://azure.microsoft.com/support/plans)。 Azure ユーザーは、Azure portal でサポート要求を作成し、管理できます。

- Azure サポート プランをお持ちの場合は、[こちらからサポート要求をオープン](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)します。

- Azure をご利用でない場合、マイクロソフトの[商用サポート](https://support.serviceshub.microsoft.com/supportforbusiness)からサポート要求をオープンすることもできます。

## <a name="post-a-question-to-microsoft-qa"></a>Microsoft Q&A に質問を投稿する
<div class='icon is-large'>
    <img alt='Microsoft Q&A' src='./media/common/question-mark-icon.png'>
</div>             

ID アプリの開発に関する質問については、Microsoft のエンジニア、Azure Most Valuable Professionals (MVP)、およびエキスパー トコミュニティのメンバーから直接回答が得られます。

[Microsoft Q&A](/answers/products/) は、Azure のコミュニティ サポートの推奨される情報源です。

Microsoft Q&A を検索しても問題に対する回答が見つからない場合は、新しい質問を送信します。 [質の高い質問](https://docs.microsoft.com/answers/articles/24951/how-to-write-a-quality-question.html)をするときは、次のタグのいずれかを使います。

| コンポーネント/領域| Tags  |
|------------|---------------------------|
| Active Directory Authentication Library (ADAL)                              | [[adal]](https://docs.microsoft.com/answers/topics/azure-ad-adal-deprecation.html)                |
| Microsoft Authentication Library (MSAL)                                     | [[msal]](https://docs.microsoft.com/answers/topics/azure-ad-msal.html)                            |
| Open Web Interface for .NET (OWIN) ミドルウェア                               | [[azure-active-directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |
| [Azure AD B2B / 外部 ID](../external-identities/what-is-b2b.md) | [[azure-ad-b2b]](https://docs.microsoft.com/answers/topics/azure-ad-b2b.html)                     |
| [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://docs.microsoft.com/answers/topics/azure-ad-b2c.html)                     |
| [Microsoft Graph API](https://developer.microsoft.com/graph/)               | [[azure-ad-graph]](https://docs.microsoft.com/answers/topics/azure-ad-graph.html)                 |
| 他のすべての認証と承認の分野                            | [[azure-active-directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |

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
|MSAL.NET| https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues |
| MSAL Node | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |
| MSAL Python | https://github.com/AzureAD/microsoft-authentication-library-for-python/issues |
| MSAL React | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |

## <a name="submit-feedback-on-azure-feedback"></a>Azure フィードバックでフィードバックを送信する

<div class='icon is-large'>
    <img alt='UserVoice' src='https://docs.microsoft.com/media/logos/logo-uservoice.svg'>
</div>

新機能を要求するには、Azure フィードバックに投稿します。 Microsoft ID プラットフォームを、あなたが開発するアプリケーションによりいっそう役立てるためのアイデアを共有します。

| サービス                       | Azure フィードバックの URL |
|-------------------------------|---------------|
| Azure Active Directory | https://feedback.azure.com/forums/169401-azure-active-directory |
| Azure Active Directory - 開発者エクスペリエンス             | https://feedback.azure.com/forums/169401-azure-active-directory?category_id=164757 |
| Azure Active Directory - 認証             | https://feedback.azure.com/forums/169401-azure-active-directory?category_id=167256 |

## <a name="stay-informed-of-updates-and-new-releases"></a>更新プログラムと新しいリリースに関する最新情報を入手する

<div class='icon is-large'>
    <img alt='Stay informed' src='https://docs.microsoft.com/media/common/i_blog.svg'>
</div>

- [Azure の更新情報](https://azure.microsoft.com/updates/?category=identity): 重要な製品の更新プログラム、ロードマップ、および発表について確認できます。

- [ドキュメントの最新情報](https://docs.microsoft.com/azure/active-directory/develop/whats-new-docs): Microsoft ID プラットフォームのドキュメントの最新情報を確認できます。

- [Azure Active Directory ID ブログ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/bg-p/Identity): Azure AD に関するニュースと情報を入手できます。

- [技術コミュニティ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/bg-p/Identity/): 経験を共有し、エキスパートとの交流を通じて学ぶことができます。


