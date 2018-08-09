---
title: Azure DevOps Projects の概要 | Microsoft Docs
description: Azure DevOps Projects の価値について説明します
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: eef71aabaab71f908f950a4467da918dca1787fa
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492546"
---
# <a name="overview-of-azure-devops-project"></a>Azure DevOps Projects の概要

Azure DevOps Projects を利用すると、Azure を使い始めるのが簡単になります。 DevOps Projects リソースは、Azure portal から簡単な手順により、任意の Azure サービス上で好みのアプリの種類を起動するのに役立ちます。 DevOps Projects は、アプリケーションの開発、展開、監視に必要なすべてのものを設定します。
DevOps Projects ダッシュボードでは、Azure portal の単一のビューからコードのコミット、ビルド、展開のすべてを監視できます。

## <a name="why-should-i-use-the-azure-devops-project"></a>Azure DevOps Projects を使用する理由

Azure DevOps Projects は、Azure への継続的インテグレーション (CI) および継続的デリバリー (CD) パイプライン全体のセットアップを自動化します。  既存のコードで始めるか、提供されているサンプル アプリケーションの 1 つを使って、Virtual Machines、App Service、Azure Container Service、Azure SQL Database、Azure Service Fabric などのさまざまな Azure サービスに、そのアプリケーションをすばやく展開できます。  

Azure DevOps Projects は、初期 Git リポジトリの設定、CI/CD パイプラインの構成、監視用の Application Insights リソースの作成、Azure portal 上の Azure DevOps Projects ダッシュボードの作成によるソリューション全体の単一ビューの提供などのすべてを含む、DevOps パイプラインの初期構成のすべての作業を行います。

Azure DevOps Projects を使って次のことができます。

* アプリケーションを Azure に短時間で展開します
* VSTS CI/CD パイプラインのセットアップを自動化します
* DevOps Projects をテンプレートとして使って VSTS で Azure に CI/CD を正しく設定する方法を確認して理解します
* Azure への CI/CD パイプラインを開始した後、特定のシナリオに基づいてリリース パイプラインをさらにカスタマイズします

## <a name="how-do-i-use-the-azure-devops-project"></a>Azure DevOps Projects を使用する方法

Azure DevOps Projects は Azure portal から利用できます。  他の Azure リソースと同じように、ポータルから Azure DevOps Projects リソースを作成します。  DevOps Projects では、さまざまな構成オプションに対して、ウィザードに似たステップ バイ ステップのエクスペリエンスが提供されます。  

初期セットアップの一部として、いくつかの構成オプションを選びます。  設定できるオプションは次のとおりです。

* 提供されたサンプル アプリを使うか、独自のコードを作成します
* アプリの言語を選びます
* 言語に基づいてアプリのフレームワークを選びます
* Azure サービス (展開ターゲット) を選びます
* VSTS アカウント (新規または既存)
* Azure サブスクリプションを選びます
* Azure サービスの場所を選びます
* Azure サービスのさまざまな価格レベルから選びます

Azure DevOps Projects を使用した後は、Azure portal 上の Azure DevOps Projects ダッシュボードから一元的に、すべてのリソースを削除することもできます。

## <a name="azure-devops-project-and-vsts-integration"></a>Azure DevOps Projects と VSTS の統合

DevOps Projects は VSTS を利用しています。  DevOps Projects は、VSTS で Azure への CI/CD を設定するために必要なすべての作業を自動化します。  新規または既存の VSTS アカウントに、Git リポジトリが作成されます。  DevOps Projects は、サンプル アプリケーションまたは既存のコードを、新しい Git リポジトリにコミットします。  オートメーションはまた、新しいコードがコミットされるたびにビルドが開始するように、ビルドに対する CI トリガーも確立します。  また、DevOps Projects は CD トリガーを作成し、新しく成功したすべてのビルドを、ユーザーが選んだ Azure サービスに展開します。  ビルドとリリースの定義は、他のシナリオに合わせてカスタマイズできます。  また、他のプロジェクトで使用するために、ビルドとリリースの定義を複製することもできます。

DevOps Projects を作成した後は、次のことができます。

* ビルドとリリースのパイプラインをカスタマイズします
* プル要求を使ってコードのフローを管理し、高い品質を維持します
* コードをマージして品質の水準を引き上げる前に、各コミットをテストしてビルドします
* アプリケーションと共にプロジェクトのバックログと問題を追跡します

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Azure DevOps Projects の使用を始める方法

* [Azure DevOps プロジェクトの使用を開始する](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Azure DevOps Projects のビデオ

* [Azure DevOps プロジェクトで CI/CD を作成する](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
