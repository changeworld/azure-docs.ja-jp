---
title: "Azure Stack 開発キット デプロイ クイックスタート | Microsoft Docs"
description: "Azure Stack 開発キットのデプロイについて説明します"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/10/2017
ms.author: erikje
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 81b6282addd1e88e4146367c4dd9a2ee7b8c84bf
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---
# <a name="azure-stack-development-kit-deployment-quickstart"></a>Azure Stack 開発キット デプロイ クイックスタート

[Azure Stack 開発キット](azure-stack-poc.md)は、Azure Stack の機能やサービスを評価したり、実演したりするためにデプロイできるテスト/開発環境です。 この環境を立ち上げるには、環境ハードウェアを用意し、いくつかのスクリプトを実行する (これには数時間かかります) 必要があります。 その後、管理者とテナントのポータルにサインインし、Azure Stack を管理したり、プランをテストしたりできます。 

1. [**ハードウェア、ソフトウェア、ネットワークを計画します**](azure-stack-deploy.md)。 開発キットをホストするコンピューター (開発キット ホスト) は、ハードウェア、ソフトウェア、ネットワークの要件を満たす必要があります。 Azure Active Directory か Active Directory フェデレーション サービス (AD FS) の使用を選択する必要もあります。 インストール プロセスが滞りなく進行するように、デプロイの開始前に以上の前提条件を必ず満たしてください。 

2. [**デプロイ パッケージをダウンロードし、抽出します**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit)。 開発キット ホストや別のコンピューターにデプロイ パッケージをダウンロードできます。 抽出されたデプロイ ファイルは 60 GB の空きディスク容量を占めます。そのため、別のコンピューターを利用することで、開発キット ホストのハードウェア要件を緩和できます。

3. インストーラーを利用して[**開発キット ホスト**](azure-stack-run-powershell-script.md#prepare-the-development-kit-host)を用意します。 この手順の後、開発キット ホストは Cloudbuilder.vhdx (起動可能オペレーティング システムと Azure Stack インストール ファイルを含む仮想ハード ドライブ) を起動します。

4. 開発キット ホストで[**開発キットをデプロイします**](azure-stack-run-powershell-script.md#deploy-the-development-kit)。

5. Azure Stack 開発で Azure Active Directory を利用する場合、Azure Stack に [Azure Marketplace アイテムをダウンロード](azure-stack-download-azure-marketplace-item.md)できるように [Azure に Azure Stack を登録する](azure-stack-register.md)必要があります。

以上のテジョンを完了すると、管理者ポータルとテナント ポータルの両方で開発キット環境が与えられます。 これでポータルに[接続し、サインイン](azure-stack-connect-azure-stack.md)できます。 サインインしたら、リソース プロバイダーのデプロイ、[プラン](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions)の作成、Azure Stack [Marketplace](azure-stack-marketplace.md) のデータ入力を開始できます。

