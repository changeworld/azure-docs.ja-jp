---
title: Azure Stack のサービスとしての検証で Microsoft のソフトウェア更新プログラムを検証する | Microsoft Docs
description: Microsoft のソフトウェア更新プログラムをサービスとしての検証で検証する方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: bcb56789b2781cd1d081af8c112222a1f1269a74
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304892"
---
# <a name="validate-software-updates-from-microsoft"></a>Microsoft のソフトウェア更新プログラムの検証

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft は、Azure Stack ソフトウェアの更新プログラムを定期的にリリースします。 これらの更新プログラムは、Azure Stack 共同エンジニアリング パートナーに提供されます。 更新プログラムの一般公開より前に提供されます。 独自のソリューションに対して更新プログラムのチェックを行い、Microsoft にフィードバックを返すことができます。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>毎月の更新プログラムの適用

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>ワークフローを作成する

更新プログラムの検証では、**ソリューションの検証**と同じワークフローを使用します。

## <a name="run-tests"></a>テストの実行

1. 更新プログラムの検証では、**ソリューションの検証**と同じワークフローを使用します。 

2. [ソリューションの検証テストの実行](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)に関する手順に従ってください。 代わりに、以下のテストを選択します。
    - Monthly Azure Stack Update Verification (月次 Azure Stack 更新プログラムの検証)
    - Cloud Simulation Engine (クラウド シミュレーション エンジン)

更新プログラムの検証のためにパッケージの署名を要求する必要はありません。

## <a name="next-steps"></a>次の手順

- [VaaS ポータルでのテストの監視と管理](azure-stack-vaas-monitor-test.md)