---
title: Azure Virtual Desktop のアーキテクチャに関する推奨事項 - Azure
description: アプリ開発者向けの Azure Virtual Desktop のアーキテクチャに関する推奨事項。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0b398fe41ec1cda06fce1cf9141ec6bd5d6bcbfb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798953"
---
# <a name="architecture-recommendations"></a>アーキテクチャに関する推奨事項

Azure Virtual Desktop のデプロイには、エンドユーザーのニーズやサービスをデプロイする組織の既存のインフラストラクチャなどのさまざまな要因に応じて、さまざまな形とサイズがあります。 組織のニーズに合った適切なアーキテクチャを使用していることを確認するにはどうすればよいでしょうか。

この記事では、Azure Virtual Desktop のデプロイ構造に関するガイダンスを提供します。 この記事に記載されている例は、Azure Virtual Desktop をデプロイできる唯一の方法ではありません。 ただし、組織内外のユーザーのために、最も基本的な種類のデプロイの 2 つについて説明します。

## <a name="deploying-azure-virtual-desktop-for-users-within-your-organization"></a>組織内のユーザー向けの Azure Virtual Desktop のデプロイ

組織内のユーザー向けに Azure Virtual Desktop のデプロイを行う場合は、すべてのユーザーとリソースを同じ Azure テナントでホストできます。 Azure Virtual Desktop で現在サポートされている ID 管理方法を使用して、ユーザーのセキュリティを保つこともできます。

これらは、組織内のユーザーに対して RemoteApp とデスクトップを提供できる Azure Virtual Desktop デプロイの最も基本的な要件です。

- ユーザー セッションをホストするための 1 つのホスト プール
- ホスト プールをホストするための 1 つの Azure サブスクリプション
- サブスクリプションと ID 管理の所有テナントとなる 1 つの Azure テナント

ただし、さまざまなユーザー グループにさまざまなアプリを提供する複数のホスト プールを使用してデプロイを構築することもできます。

それぞれの Azure Virtual Desktop デプロイを格納するために別個に Azure サブスクリプションを作成する顧客もいます。 この方法では、リソースを提供するサブ組織に基づいて、各デプロイのコストを相互に区別できます。 また、より細かなレベルでコストを区別するために Azure の課金スコープを使用する顧客もいます。 詳細については、「[スコープを理解して使用する](../../cost-management-billing/costs/understand-work-scopes.md)」を参照してください。

## <a name="deploying-azure-virtual-desktop-for-users-outside-your-organization"></a>組織外のユーザー向けの Azure Virtual Desktop のデプロイ

Azure Virtual Desktop のデプロイで組織外のエンドユーザー (特に、通常 Windows を使用しないユーザー、または組織の内部リソースにアクセスできないユーザー) にサービスを提供する場合は、セキュリティに関する追加の推奨事項を検討する必要があります。

現在、Azure Virtual Desktop では、企業間 (B2B) または企業対クライアント (B2C) ユーザーを含む外部 ID はサポートされていません。 これらの ID を手動で作成および管理し、自分でユーザーに資格情報を提供する必要があります。 ユーザーはこれらの ID を使用して、Azure Virtual Desktop 内のリソースにアクセスします。

セキュリティで保護されたソリューションを顧客に提供するために、Microsoft では、専用の Active Directory を使用して、顧客ごとに Azure Active Directory (AD) テナントとサブスクリプションを作成することを強くお勧めします。 この分離は、他のデプロイとそのリソースから完全に分離された個別の Azure Virtual Desktop デプロイを組織ごとに作成する必要があることを意味します。 情報を安全に保つために、各組織が使用する仮想マシンが他社のリソースにアクセスできないようにする必要があります。 Active Directory Domain Services (AD DS) と Azure AD Connect の組み合わせを使用するか、Azure AD Domain Services を使用して、これらの個別のデプロイを設定できます。
