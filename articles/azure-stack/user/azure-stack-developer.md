---
title: Azure Stack 用アプリの開発 | Microsoft Docs
description: Azure Stack でアプリケーションの試作品を開発する際の考慮事項
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 60336a56437b3d5c5d39d8c1584d91c909ac47ff
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246196"
---
# <a name="develop-for-azure-stack"></a>Azure Stack 向けの開発

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack 環境へのアクセス権がなくても、アプリケーションの開発をさっそく始めることができます。 Azure Stack は、お使いのデータセンターで実行される Microsoft Azure サービスを提供しているため、Azure で使用するのと同様のツールとプロセスを使用して、Azure Stack 向けの開発ができます。

## <a name="development-considerations"></a>開発の考慮事項

簡単な準備をして次のトピックの説明に従えば、Azure を使用して Azure Stack 環境をエミュレートできます。

* Azure で、Azure Stack にデプロイできる Azure Resource Manager テンプレートを作成できます。 移植性が確保されたテンプレートの作成について詳しくは、[テンプレートに関する考慮事項](azure-stack-develop-templates.md)をご覧ください。
* Azure と Azure Stack 間には、サービスの可用性とサービスのバージョン管理に違いがあります。 [Azure Stack ポリシー モジュール](azure-stack-policy-module.md)を使用して、Azure サービスの可用性やリソースの種類を Azure Stack で使用できるものに制限できます。 サービスを制限することで、Azure Stack で使用できるサービスにアプリケーションが依存することになります。
* [Azure Stack クイックスタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)は、Azure と Azure Stack にデプロイできるテンプレートを開発する方法を示す一般的なシナリオ例です。

## <a name="next-steps"></a>次の手順

Azure Stack 開発の詳細については、次の記事をご覧ください。

* [Azure Resource Manager テンプレートのベスト プラクティス](azure-stack-develop-templates.md)
* [GitHub 上の Azure Stack クイック スタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)