---
title: GitOps と Azure Dev/Test プラン
description: Azure Dev/Test との関連付けに GitOps を使用する
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: df3fd830c04f90315126bd68a07a22a95cbfbc53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092224"
---
# <a name="using-gitops-with-azure-devtest-offer-to-optimize-and-secure-applications"></a>Azure Dev/Test プランで GitOps を使用してアプリケーションを最適化し、セキュリティで保護する

GitOps は、運用フレームワークです。 アプリケーションの開発に使用されるベストプラクティスを DevOps し、インフラストラクチャの自動化に適用します。  

チームで GitOps を行うときは、コードとして格納されている構成ファイル (インフラストラクチャコード) を使用します。 これらのファイルは、デプロイされるたびに同じ環境を生成します。 これは、ビルドするたびに同じアプリケーションバイナリを生成するアプリケーションソースコードのようなものと考えてください。  

## <a name="gitops-methodology"></a>GitOps 手法  

このプロセス (方法) では、Git リポジトリを使用します。 これらのリポジトリは、アプリケーションに対して定義した状態と構成の実際のソースです。 これらには、運用環境で必要なインフラストラクチャの宣言型の説明が含まれています。 自動化されたプロセスにより、環境はリポジトリ内の記述された状態と一致します。  

新しいアプリケーションをデプロイするか、既存のアプリケーションを更新するには、リポジトリを更新するだけで済みます。自動処理では、他のアプリケーションはすべて処理されます。  

## <a name="benefits-of-gitops"></a>GitOps の利点  

- インフラストラクチャの変更のコラボレーションを有効にします  
- 強化されたアクセス制御  
- 市場投入期間の短縮  
- リスクの軽減  
- コストの削減  
- エラーが発生しにくい  

## <a name="use-gitops-with-devtest"></a>開発/テストで GitOps を使用する  

GitOps as プロセスとフレームワークは、非運用環境インスタンスに適用する必要があります。 DevTest 環境で検証または使用できます。 GitOps 原則を使用して、DevOps プロセスを改善できます。 GitOps の原則を持つ DevTest の特典と環境を使用して、アクティビティを最適化し、アプリケーションのセキュリティと信頼性を維持します。  

GitOps は、git などの自動化と一般的に使用されるコラボレーションフレームワークを組み合わせたものです。 エンタープライズセキュリティ標準に準拠しながら、クラウドインフラストラクチャの迅速な配信を実現するために組み合わせることができます。  

GitOps と Azure の詳細については、次を参照してください。  

- [Azure 金曜日ビデオ: Azure Arc Enabled Kubernetes と GitOps](https://azure.microsoft.com/resources/videos/azure-friday-azure-arc-enabled-kubernetes-with-gitops/)  
- [Azure 金曜日ブログ: Azure Arc Enabled Kubernetes と GitOps](https://techcommunity.microsoft.com/t5/azure-arc/azure-arc-enabled-kubernetes-with-gitops/ba-p/1654171?ocid=AID754288&wt.mc_id=azfr-c9-scottha&wt.mc_id=CFID0570)  
- [Azure インフラストラクチャライフサイクルの自動化のための GitOps](https://github.com/travisnielsen/azure-gitops)
