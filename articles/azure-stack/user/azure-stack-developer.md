---
title: Azure Stack 用アプリの開発 | Microsoft Docs
description: Azure Stack でアプリケーションの試作品を開発する際の考慮事項
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a1b5a90ca40ce2b19186220344b22ec0ae77e34b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199196"
---
# <a name="develop-for-azure-stack"></a>Azure Stack 向けの開発

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack 環境へのアクセス権がなくても、アプリケーションの開発をさっそく始めることができます。 Azure Stack は、お使いのデータセンターで実行される Microsoft Azure サービスを提供しているため、Azure で使用するのと同様のツールとプロセスを使用して、Azure Stack 向けの開発ができます。 簡単な準備をして次のトピックの説明に従えば、Azure を使用して Azure Stack 環境をエミュレートできます。

* Azure で、Azure Stack にデプロイできる Azure Resource Manager テンプレートを作成できます。 移植性が確保されたテンプレートの作成について詳しくは、[テンプレートに関する考慮事項](azure-stack-develop-templates.md)をご覧ください。
* Azure と Azure Stack 間には、サービスの可用性とサービスのバージョン管理に違いがあります。 [Azure Stack ポリシー モジュール](azure-stack-policy-module.md)を使用して、Azure Stack で使用できる Azure サービスの可用性やリソースの種類を制限できます。 サービスを制限することで、Azure Stack で使用できるサービスにアプリケーションが依存することになります。
* [Azure Stack クイックスタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)は、Azure と Azure Stack にデプロイできるテンプレートを開発する方法を示す一般的なシナリオ例です。
