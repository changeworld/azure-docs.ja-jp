---
title: Azure AD のセキュリティで保護されたハイブリッド アクセス | Microsoft Docs
description: この記事では、オンプレミス、パブリッククラウド、またはプライベート クラウドにあるレガシ アプリケーションを Azure AD と統合するためのパートナー ソリューションについて説明します。 アプリ デリバリー コントローラーまたはネットワークを Azure AD に接続することで、レガシ アプリをセキュリティで保護します。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21b89704bdd2836057db8f7a78244205efb9cc90
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896190"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-app-delivery-controllers-and-networks"></a>安全なハイブリッド アクセス:アプリ デリバリー コントローラーとネットワークを使用してレガシ アプリをセキュリティで保護する

オンプレミスやクラウドのレガシ認証アプリケーションを、既存のアプリケーション デリバリー コントローラーまたはネットワークによって Azure AD に接続することで、それらの認証アプリケーションを保護できるようになりました。 これにより、 Azure AD 条件付きアクセスや Azure AD Identity Protection Azure AD のような Azure AD 機能を使用して、すべてのアプリケーションにわたり、ギャップを埋めてセキュリティ体制を強化することができます。

ビジネス プロセスにとってまだ非常に重要であるのに、Azure AD を使用する前は保護できなかったレガシ アプリケーションを、既存のネットワークとデリバリー コントローラーを使用して容易に保護できます。 お客様はおそらく、これらのアプリケーションの保護を開始するために必要なものはすべて既に所有しています。

![安全なハイブリッド アクセスを示しているイメージ](media/secure-hybrid-access/secure-hybrid-access.png)

以下のベンダーが、Azure AD との統合のために事前に構築されたソリューションと詳細なガイダンスを提供しています。

* [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)
* [Zscaler Private Access (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
