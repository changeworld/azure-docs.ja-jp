---
title: Azure Active Directory のデプロイ計画 | Microsoft Docs
description: Azure Active Directory の機能をデプロイする方法についてのエンド ツー エンドのガイダンスを紹介します。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 08/03/2018
ms.author: lizross
ms.openlocfilehash: 07d3915fd007c0827b885b0603eb176b9e408576
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39508364"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory のデプロイ計画
ここでは、Azure Active Directory (Azure AD) の各種機能をデプロイする方法についてのエンド ツー エンドのガイダンスを紹介しています。 以下のデプロイ計画では、Azure AD のいくつかの代表的な機能について、そのビジネス上の価値や、正しく展開するうえで必要な計画の考慮事項、設計、運用手順をひととおり説明しています。 

これらのドキュメントには、メール テンプレートやシステム アーキテクチャ図、一般的なテスト ケースなどが含まれています。 

ドキュメントについてのご意見をぜひお寄せください。 ドキュメントがお役に立てたかどうかについて、簡単な[アンケート](https://aka.ms/deploymentplanfeedback)にご協力いただければ幸いです。 

|シナリオ |説明 |
|-|-|
|[シングル サインオン](https://aka.ms/SSODPDownload)|シングル サインオンは、1 つのユーザー アカウントを使って 1 回サインインするだけで作業に必要なすべてのアプリとリソースにアクセスできる機能です。 いったんサインインすれば、再度認証 (パスワードの入力など) を求められることなく、Microsoft Office から SalesForce や Box へと移動することができます。|
|[ユーザー プロビジョニング](https://aka.ms/UserProvisioningDPDownload)|Azure AD を使用すると、Dropbox、Salesforce、ServiceNow などのクラウド (SaaS) アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。|
|[Multi-Factor Authentication](https://aka.ms/MFADPDownload)|Azure Multi-Factor Authentication (MFA) は、Microsoft の 2 段階認証ソリューションです。 管理者が承認した認証方式を使用することにより、Azure MFA では、データやアプリケーションへのアクセスを保護しながら、シンプルなサインイン プロセスへの要求に応えることができます。|
|[条件付きアクセス](https://aka.ms/CADPDownload)|条件付きアクセスを使用すると、クラウド アプリへのアクセスをだれに許可するかを各種の条件に基づいて自動的に判断するアクセスの制御を実装できます。|
|[ADFS からパススルー認証への移行](https://aka.ms/ADFSTOPTADPDownload)|Azure AD パススルー認証を使用すると、ユーザーは同じパスワードを使用して、オンプレミスのアプリケーションとクラウド ベースのアプリケーションの両方にサインインできます。 この機能により、ユーザー エクスペリエンスが向上します。ユーザーは、覚えておくパスワードが 1 つ少なくなり、ユーザーがサインイン方法を忘れる可能性が低くなるため IT ヘルプデスクのコストが削減します。 この機能により、ユーザーが Azure AD を使用してサインインするとき、ユーザーのパスワードがオンプレミスの Active Directory に対して直接検証されます。|
|[ADFS からパスワード ハッシュ同期への移行](https://aka.ms/ADFSTOPHSDPDownload)|パスワード ハッシュ同期では、ユーザー パスワードのハッシュがオンプレミスの Active Directory から Azure AD に同期されます。これにより Azure AD は、オンプレミスの Active Directory とやり取りすることなくユーザーを認証することができます。|
|[シームレス SSO](https://aka.ms/SeamlessSSODPDownload)|Azure Active Directory シームレス シングル サインオン (Azure AD シームレス SSO) では、ユーザーが企業ネットワークに接続される会社のデバイスを使用するときに、自動的にサインインを行います。 この機能を有効にすると、ユーザーは Azure AD にサインインするためにパスワードを入力する必要がなくなります。また、通常はユーザー名の入力も不要です。 この機能により、追加のオンプレミス コンポーネントを必要とせずに、ユーザーはクラウド ベースのアプリケーションに簡単にアクセスできるようになります。|
|[セルフサービスのパスワード リセット](https://aka.ms/SSPRDPDownload)|セルフサービスによるパスワードのリセットにより、ユーザーは、いつでも、どこでも、管理者の介入なしでパスワードをリセットできます。|
|[Azure AD アプリケーション プロキシ](https://aka.ms/AppProxyDPDownload)|現在、従業員は、どこでも、いつでも、どんなデバイスからでも生産的であることを望んでいます。 タブレット、電話、ラップトップを問わず、自分のデバイスで作業したいと考えています。 そして、すべてのアプリケーション (クラウドにある SaaS アプリとオンプレミスの社内アプリの両方) にアクセスできることを期待しています。 オンプレミスのアプリケーションへのアクセス提供には、従来、仮想プライベート ネットワーク (VPN) や非武装地帯 (DMZ) が必要でした。 これらのソリューションは、複雑でセキュリティ保護が困難であるだけでなく、設定と管理にコストがかかります。 もっと良い方法があります。 それが Azure AD アプリケーション プロキシです。|
