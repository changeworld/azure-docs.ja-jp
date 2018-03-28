---
title: Microsoft Azure Active Directory シングル サインオン プラグインについてよく寄せられる質問 | Microsoft Docs
description: Azure Active Directory と Microsoft Azure Active Directory single sign-on for JIRA の間でシングル サインオンを構成する方法について説明します。
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
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: 571fbd5078f66375f6e81cba2a790121366f9d60
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-faq"></a>Microsoft Azure Active Directory シングル サインオン プラグインについてよく寄せられる質問 

## <a name="1-whats-the-microsoft-sso-add-on"></a>1.Microsoft SSO アドオンとは何ですか。

このアドオンは、Atlassian の JIRA (JIRA Core、JIRA Software、JIRA Service Desk を含む) および Confluence On-premise ソフトウェアのシングル サインオン機能を提供します。 このアドオンは、IdP として Azure AD で動作します。

## <a name="2-add-on-works-with-which-atlassian-products"></a>2.このアドオンはどの Atlassian 製品で動作しますか。

現在、このアドオンはオンプレミス バージョンの JIRA と Confluence で動作します。

## <a name="3-does-this-add-on-work-on-cloud-version"></a>手順 3.このアドオンはクラウド バージョンで動作しますか。

いいえ。 オンプレミス バージョンの JIRA と Confluence のみがサポートされています。

## <a name="4-which-versions-of-jira-and-confluence-are-supported"></a>4.どのバージョンの JIRA と Confluence がサポートされていますか。

サポートされているバージョンは次のとおりです。

* JIRA Core と JIRA Software: 6.0 から 7.2.2 
* JIRA Service Desk: 3.0 から 3.2 
* Confluence: 5.0 ～ 5.10

## <a name="5-is-this-add-on-free-or-paid"></a>5.このアドオンは無料ですか、それとも有料ですか。

無料のアドオンです。Atlassian のマーケット プレイスからインストールすることができます。

## <a name="6-do-i-need-to-restart-jiraconfluence-once-i-deploy-the-add-on"></a>6.アドオンを展開した後に JIRA/Confluence を再起動する必要はありますか

アドオンの展開後に再起動する必要はありません。 展開してすぐにアドオンを使用できます。

## <a name="7-how-do-i-get-support-for-the-add-on"></a>7.アドオンのサポートを受けるにはどうすればよいですか。

<email> でお問い合わせください。<> 時間以内に回答します。 Azure Portal チャネルを通じて Microsoft にサポート チケットを提出することもできます。 お問い合わせ先の電話番号は <Number> です。受付時間は平日 (月～金曜日) の午前 <> 時から午後 <> 時です。

## <a name="8-would-this-add-on-work-on-mac-or-ubuntu-installation-of-jira-and-confluence"></a>8.このアドオンは、Mac または Ubuntu にインストールされているの JIRA と Confluence で動作しますか?

このアドオンは、64 ビット版 Windows Server にインストールされた JIRA および Confluence でのみテストされています。

## <a name="9-does-this-add-on-work-with-other-idps-than-azure-ad"></a>9.このアドオンは Azure AD 以外の IdP でも動作しますか。

いいえ。 アドオンは Azure AD でのみ動作します。

## <a name="10-what-version-of-saml-does-the-add-on-work-with"></a>10.このアドオンはどのバージョンの SAML で動作しますか。

アドオンは SAML 2.0 で動作します。

## <a name="11-does-the-add-on-do-use-provisioning-as-well"></a>11.このアドオンはプロビジョニングも使用していますか。

いいえ。 現時点では、SAML 2.0 ベースの SSO のみが提供されています。 SSO ログイン前にアプリケーションでユーザーをプロビジョニングする必要があります。

## <a name="12-are-cluster-versions-of-jira-and-confluence-supported-by-add-on"></a>12.クラスター バージョンの JIRA および Confluence はアドオンでサポートされていますか。

いいえ。 このアドオンはオンプレミス バージョンの JIRA と Confluence で動作します。

## <a name="13-would-this-plugin-work-with-http-version-of-jira-and-confluence"></a>13.このアドオンは HTTP バージョンの JIRA および Confluence で動作しますか。

いいえ。 このアドオンは、HTTPS 対応のインストールでのみ動作します。

## <a name="14-do-i-need-to-buy-license-of-the-add-on"></a>14.アドオンのライセンスを購入する必要はありますか。

このアドオンは無料です。