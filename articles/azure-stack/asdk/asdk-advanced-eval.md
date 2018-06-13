---
title: 高度な Azure Stack 評価タスク | Microsoft Docs
description: この記事では、高度な Azure Stack 評価タスクについて説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: c4bf76aa07ec5025d9e53b5518929199ace27e18
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
ms.locfileid: "29974115"
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>高度な Azure Stack Development Kit 評価タスク
基本的な Azure Stack Development Kit (ASDK) サービスの機能について把握したら、より高度なシナリオをテストして Azure Stack の理解を深めることができます。 これらのより高度な評価タスクは、Azure Stack のオペレーター ドキュメントに詳しく記載されています。

> [!NOTE]
> 多くのオペレーター タスクは ASDK デプロイと運用環境のマルチノード Azure Stack デプロイの両方でサポートされますが、ASDK デプロイではすべての使用シナリオがサポートされるわけではありません。 詳しくは、「[ASDK and multi-node Azure Stack differences (ASDK とマルチノード Azure Stack の相違点)](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences)」をご覧ください。

## <a name="delegate-offers-in-azure-stack"></a>Azure Stack でのプランの委任
Azure Stack オペレーターとして、オファー作成とユーザー サインアップを他のユーザーに担当してもらいたいことがよくあります。 たとえば、あなたがサービス プロバイダーで、再販業者に顧客のサインアップと管理を担当してもらいたい場合があります。 あるいは、あなたが社内の中枢の IT グループにいて、子会社で直接、ユーザーのサインアップをして欲しい場合も同様です。

[Azure Stack でのプランの委任](.\.\azure-stack-delegated-provider.md)はこうしたタスクの手助けをするもので、自分で直接行うよりも多くのユーザーに接触し管理することができます。 

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>SQL データベースを Azure Stack ユーザーから使用可能にする
Azure Stack オペレーターとして、ユーザー (テナント) が自分のクラウド ネイティブなアプリ、Web サイト、およびワークロードで使用できる SQL データベースを作成できるようにするオファーを作成できます。 これらのカスタムの、オンデマンドで、クラウド ベースのデータベースをユーザーに提供することによって、ユーザーの時間とリソースを節約できます。 

SQL Server リソースプロバイダー アダプターを使って、[SQL データベースを Azure Stack ユーザーが Azure Stack のサービスとして使用できるようにします](.\.\azure-stack-tutorial-sql-server.md)。 リソースプロバイダーをインストールした後で、1 つまたは複数の SQL Server インスタンスに接続します。

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Web アプリおよび API アプリを Azure Stack ユーザーが使用できるようにする
Azure Stack オペレーターは、ユーザー (テナント) が Azure Functions、Web アプリケーション、API アプリケーションを作成できるようにするプランを作成できます。 これらのオンデマンドで、クラウド ベースのアプリへのアクセスをユーザーに提供することによって、ユーザーの時間とリソースを節約できます。

App Service リソース プロバイダーをデプロイして、[Web アプリおよび API アプリを Azure Stack ユーザーが使用できるようにします](.\.\azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>次の手順
[Azure Stack 統合システムでのサービスの提供の詳細について学習する](.\.\azure-stack-offer-services-overview.md)