---
title: Azure Stack とは | Microsoft Docs
description: Azure Stack を使用すると、お使いのデータセンターで Azure のサービスを実行できます。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 89552bc568ce38c6153683e9c3b0f3f26f569083
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959543"
---
# <a name="what-is-azure-stack"></a>Azure Stack とは

Microsoft Azure Stack は、データセンター内の Azure のサービスを提供できるようにする、ハイブリッド クラウド プラットフォームです。 このプラットフォームは、進化し続けるビジネス要件をサポートするために設計されています。 Azure Stack は、最新アプリケーション向けの新しいシナリオ (エッジ環境や非接続環境など) を実現し、セキュリティとコンプライアンスの要件に対応できるように設計されています。

Azure Stack には、お客様のニーズに合わせて 2 つのデプロイ オプションが用意されています。

## <a name="azure-stack-integrated-systems"></a>Azure Stack 統合システム
Azure Stack 統合システムは Microsoft と[ハードウェア パートナー](https://azure.microsoft.com/overview/azure-stack/integrated-systems/) のパートナーシップによって提供され、クラウドと歩調を合わせた革新と、コンピューティングの管理のしやすさを両立させたソリューションを実現します。 Azure Stack は統合されたハードウェアおよび ソフトウェア システムとして提供されているため、必要な柔軟性やコントロールが得られるほか、クラウドから革新を図ることができます。 Azure Stack 統合システムには 4 ノードから 16 ノードまでのサイズがあり、ハードウェア パートナーと Microsoft によって共同でサポートされます。  Azure Stack 統合システムの使用により、運用ワークロードに向けて、新しいシナリオを作成し、新しいソリューションをデプロイできます。

## <a name="azure-stack-development-kit"></a>Azure Stack Development Kit

Microsoft [Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) は、Azure Stack の評価と学習に使用できる Azure Stack の単一ノード デプロイです。  ASDK を開発環境として使用し、Azure と一貫性のある API やツールを使用してアプリをビルドできます。

>[!Note]
>ASDK の運用環境での使用は想定されていません。

ASDK には次の制限事項があります。

* ASDK は、単一の Azure Active Directory (Azure AD) または Active Directory フェデレーション サービス (AD FS) ID プロバイダーに関連付けられます。 このディレクトリに複数のユーザーを作成し、各ユーザーにサブスクリプションを割り当てることができます。
* Azure Stack のコンポーネントは単一のホスト コンピューターにデプロイされるため、テナントのリソースに対して使用できる物理リソースは限られています。 この構成は、拡張性やパフォーマンスの評価のためのものではありません。
* 単一のホストと NIC のデプロイ要件により、ネットワークのシナリオは限られています。

## <a name="next-steps"></a>次の手順

[主要機能および概念](azure-stack-key-features.md)

[Azure Stack: Azure の拡張機能 (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
