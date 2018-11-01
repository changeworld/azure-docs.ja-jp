---
title: Azure Stack の検証のベスト プラクティス | Microsoft Docs
description: この記事では、サービスとしての検証のベスト プラクティスについて説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: John.Haskin
ms.openlocfilehash: 988b32d378b9affccb79f3351761f0eca5c91346
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650987"
---
# <a name="best-practices-for-validation-as-a-service"></a>サービスとしての検証のベスト プラクティス

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

この記事では、サービスとしての検証 (VaaS) でリソースを管理するためのベスト プラクティスについて説明します。 VaaS リソースの概要については、「[サービスとしての検証の主要概念](azure-stack-vaas-key-concepts.md)」をご覧ください。

## <a name="solution-management"></a>ソリューション管理

### <a name="naming-convention-for-vaas-solutions"></a>VaaS ソリューションの名前付け規則

VaaS に登録するすべてのソリューションに、一貫性のある名前付け規則を使用します。 たとえば、次のようにハードウェアのプロパティに基づいてソリューション名を作成できます。

|製品名 | 一意のハードウェア要素 1 | 一意のハードウェア要素 2 | ソリューション名
|---|---|---|---|
My Solution XYZ |  All Flash | My Switch X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>新しい VaaS ソリューションを作成するタイミング

同じハードウェア SKU に対してワークフローを実行する場合は、同じ VaaS ソリューションを使用します。 新しい VaaS ソリューションを作成するのは、ハードウェア SKU に変更が発生した場合だけです。

## <a name="workflow-management"></a>ワークフロー管理

### <a name="naming-convention-for-vaas-workflows"></a>VaaS ワークフローの名前付け規則

すべての VaaS ワークフロー実行に、一貫性のある名前付け規則を使用します。 たとえば、次のようにビルドのプロパティに基づいてワークフロー名を作成します。

|ビルド番号 (メジャー) | 日付 | ソリューションのサイズ | ワークフロー名
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>次の手順

- [サービスとしての検証の主要概念](azure-stack-vaas-key-concepts.md)を確認する