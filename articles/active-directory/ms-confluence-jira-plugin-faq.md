---
title: Azure Active Directory SSO プラグインに関する FAQ | Microsoft Docs
description: Azure Active Directory と Jira/Confluence の間のシングル サインオンの構成に関するよく寄せられる質問への回答を得ます。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: jeedes
ms.openlocfilehash: eb7576c48d3836eec8051d849cefe4c5ec6658c9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699208"
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>Azure Active Directory SSO プラグインに関する FAQ

このプラグインについて質問がある場合は、次の FAQ を参照してください。

## <a name="what-does-the-plug-in-do"></a>このプラグインは何を行いますか。

このプラグインは、Atlassian Jira (Jira Core、Jira Software、Jira Service Desk を含む) および Confluence オンプレミス ソフトウェアのシングル サインオン (SSO) 機能を提供します。 このプラグインは Azure Active Directory (Azure AD) で ID プロバイダー (IdP) として動作します。

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>このプラグインはどの Atlassian 製品で動作しますか。

このプラグインはオンプレミス バージョンの Jira と Confluence で動作します。

## <a name="does-the-plug-in-work-on-cloud-versions"></a>このプラグインはクラウド バージョンで動作しますか。

いいえ。 このプラグインはオンプレミス バージョンの Jira と Confluence のみサポートしています。

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>このプラグインはどのバージョンの Jira と Confluence をサポートしていますか。

このプラグインは次のバージョンをサポートしています。

* Jira Core と Jira Software: 6.0 から 7.8
* Jira Service Desk: 3.0 から 3.2
* Confluence: 5.0 ～ 5.10

## <a name="is-the-plug-in-free-or-paid"></a>このプラグインは無料ですか、有料ですか。

このアドオンは無料です。

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>プラグインを展開した後に Jira または Confluence を再起動する必要はありますか。

再起動は必要ありません。 すぐにプラグインの使用を開始できます。

## <a name="how-do-i-get-support-for-the-plug-in"></a>プラグインのサポートを受けるにはどうすればよいですか。

このプラグインに関するサポートが必要な場合は、[Azure AD SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)までお問い合わせください。 チームが 24 から 48 営業時間以内に応答します。

Azure Portal チャネルを通じて Microsoft にサポート チケットを提出することもできます。

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>このプラグインは、Mac または Ubuntu にインストールされている Jira と Confluence で動作しますか。

このプラグインは、64 ビット版 Windows Server にインストールされた Jira および Confluence でのみテストされています。

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>このプラグインは Azure AD 以外の IdP で動作しますか。

いいえ。 Azure AD でのみ動作します。

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>このプラグインはどのバージョンの SAML で動作しますか。

SAML 2.0 で動作します。

## <a name="does-the-plug-in-do-user-provisioning"></a>このプラグインはユーザー プロビジョニングを行いますか。

いいえ。 このプラグインは SAML 2.0 ベースの SSO のみを提供します。 SSO サインイン前にアプリケーションでユーザーをプロビジョニングする必要があります。

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>このプラグインはクラスター バージョンの Jira と Confluence をサポートしていますか。

いいえ。 このプラグインはオンプレミス バージョンの Jira と Confluence で動作します。

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>このプラグインは HTTP バージョンの Jira と Confluence で動作しますか。

いいえ。 このプラグインは、HTTPS 対応のインストールでのみ動作します。