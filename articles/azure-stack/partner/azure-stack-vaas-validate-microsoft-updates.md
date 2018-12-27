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
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7fcc7d5a1d87fe93d32772dbbb84f1d3c91d5631
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648787"
---
# <a name="validate-software-updates-from-microsoft"></a>Microsoft のソフトウェア更新プログラムの検証

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft は、Azure Stack ソフトウェアの更新プログラムを定期的にリリースします。 これらの更新プログラムは、Azure Stack の共同エンジニアリング パートナーが各自のソリューションを前提に検証し、Microsoft にフィードバックを送ることができるよう、公開前にこれらのパートナーに提供されます。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>毎月の更新プログラムの適用

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>ワークフローを作成する

更新プログラムの検証では、**パッケージの検証**と同じワークフローを使用します。 「[パッケージの検証ワークフローの作成](azure-stack-vaas-validate-oem-package.md#create-a-package-validation-workflow)」の手順に従ってください。

## <a name="run-tests"></a>テストの実行

更新プログラムの検証では、**パッケージの検証**と同じワークフローを使用します。 [パッケージの検証テストの実行](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)に関する説明の手順に従ってください。

更新プログラムの検証のためにパッケージの署名を要求する必要はありません。

## <a name="next-steps"></a>次の手順

- [VaaS ポータルでのテストの監視と管理](azure-stack-vaas-monitor-test.md)
