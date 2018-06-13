---
title: Azure App Service を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護する | Microsoft Docs
description: " PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、Azure App Service のセキュリティ ベスト プラクティスについて説明します。 "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: d2e606fe23a3a6eb9d2310b0932ccec8fcfc518c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
ms.locfileid: "31415078"
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Azure App Service を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護する

この記事では、PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、[Azure App Service](https://azure.microsoft.com/services/app-service/) の一連のセキュリティ ベスト プラクティスについて説明します。 このベスト プラクティスは、Azure に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

## <a name="azure-app-service"></a>Azure App Service
PaaS である [Azure App Service](../app-service/app-service-web-overview.md) を使用すると、任意のプラットフォームまたはデバイスを対象とした Web アプリとモバイル アプリを作成し、クラウドやオンプレミスにあるあらゆる場所のデータにアクセスできます。 App Service には、以前は Azure Websites および Azure Mobile Services として個別に提供されていた Web 機能とモバイル機能が含まれています。 さらに、ビジネス プロセスの自動化やクラウド API のホストに利用できる新しい機能も備えています。 単一の統合サービスである App Service により、Web、モバイル、および統合シナリオで豊富な機能セットを利用できます。

## <a name="best-practices"></a>ベスト プラクティス

App Service を使用する場合は、次のベスト プラクティスに従ってください。

- [Azure Active Directory (AD) を使用した認証](../app-service/app-service-authentication-overview.md)。 App Service は、ID プロバイダーに対して OAuth 2.0 サービスを提供します。 OAuth 2.0 は、Web アプリケーション、デスクトップ アプリケーション、および携帯電話に特定の認証フローを提供しながら、クライアント開発者のシンプル性を実現することに焦点を当てています。 Azure AD が OAuth 2.0 を使用することにより、ユーザーはモバイル アプリケーションと Web アプリケーションへのアクセスを承認することができます。
- 知る必要性と最小権限という 2 つのセキュリティ原則に基づいて、アクセスを制限します。 アクセスの制限は、データ アクセスにセキュリティ ポリシーを適用する必要がある組織にとって、絶対に欠かせないものです。 ロールベースのアクセス制御 (RBAC) を使用して、特定のスコープ内のユーザー、グループ、アプリケーションにアクセス許可を割り当てることができます。 ユーザーへのアプリケーション アクセス付与の詳細については、[アクセス管理の概要](../role-based-access-control/overview.md)に関するページを参照してください。
- キーを保護します。 サブスクリプション キーを紛失してしまえば、いかにセキュリティが優れていても関係ありません。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 Key Vault を使用すると、キーとシークレット (認証キー、ストレージ アカウント キー、データ暗号化キー、PFX ファイル、パスワードなど) をハードウェア セキュリティ モジュール (HSM) で保護されたキーを使用して暗号化できます。 さらに安心感を高めたい場合には、HSM でキーのインポートや生成を行うことができます。 詳細については、「[Azure Key Vault とは](../key-vault/key-vault-whatis.md)」を参照してください。 Key Vault を使用して、自動更新で TLS 証明書の管理することもできます。
- 受信ソース IP アドレスを制限します。 [App Service 環境](../app-service/environment/intro.md)には、ネットワーク セキュリティ グループ (NSG) による受信ソース IP アドレスの制限に役立つ、仮想ネットワーク統合機能が用意されています。 Azure Virtual Network (VNET) とは、Azure リソースの多くをインターネット以外のルーティング可能なネットワークに配置できる機能です。配置先のネットワークへのアクセスは制御できます。 詳細については、「[アプリを Azure 仮想ネットワークに統合する](../app-service/web-sites-integrate-with-vnet.md)」を参照してください。

## <a name="next-steps"></a>次の手順
この記事では、PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、App Service の一連のセキュリティ ベスト プラクティスについて説明しました。 PaaS デプロイのセキュリティ保護の詳細については、次のリンク先をご覧ください。

- [PaaS デプロイをセキュリティで保護する](security-paas-deployments.md)
- [Azure SQL Database と SQL Data Warehouse を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティで保護する](security-paas-applications-using-sql.md)
