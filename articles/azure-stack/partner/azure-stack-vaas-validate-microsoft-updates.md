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
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: a9e1bb2f134e0e51803266f5e7f94c05ab6966f2
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234491"
---
# <a name="validate-software-updates-from-microsoft"></a>Microsoft のソフトウェア更新プログラムの検証

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft は、Azure Stack ソフトウェアの更新プログラムを定期的にリリースします。 これらの更新プログラムは、Azure Stack の共同エンジニアリング パートナーが各自のソリューションを前提に検証し、Microsoft にフィードバックを送ることができるよう、公開前にこれらのパートナーに提供されます。

## <a name="test-an-existing-solution"></a>既存のソリューションのテスト

1. [検証ポータル](https://azurestackvalidation.com)にサインインします。

2. Microsoft の更新プログラムがデプロイされている既存のソリューションを選択し、**[パッケージの検証]** タイルの **[開始]** を選択します。

    ![パッケージの検証](media/image3.png)

3. 検証の名前を入力します。

4. デプロイ時にソリューションにインストールされた OEM パッケージの URL を入力します。 Azure Blob service に格納されたパッケージの URL を使用してください。 詳細については、「[Create an Azure storage blob to store logs (ログを格納する Azure Storage Blob の作成)](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)」を参照してください。

5. **[アップロード]** を選択して、デプロイ構成ファイルを追加します。 デプロイ構成ファイルのアップロードについては、[新しい Azure Stack ソリューションの検証](azure-stack-vaas-validate-solution-new.md)に関するページを参照してください。

6. その後、適切な環境パラメーター ファイルでデプロイ構成ファイルをカスタマイズする必要があります。詳細については、「[Environment parameters (環境パラメーター)](azure-stack-vaas-parameters.md#environment-parameters)」を参照してください。

    > [!Note]   
    > デプロイ構成ファイルは、共通テスト パラメーターを追加することで、さらにカスタマイズすることができます。 詳細については、「[Workflow common parameters for Azure Stack validation as a service (Azure Stack のサービスとしての検証に使用されるワークフロー共通パラメーター)](azure-stack-vaas-parameters.md)」を参照してください。

7. テナント ユーザー、サービス管理者、クラウド管理者のユーザー名とパスワードは、手動で入力する必要があります。

8. 診断ログを格納する Azure Storage Blob の URL を指定します。 詳細については、「[Create an Azure storage blob to store logs (ログを格納する Azure Storage Blob の作成)](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)」を参照してください。

    > [!Note]  
    > ワークフローにラベルを付けるために、わかりやすいタグを入力できます。

10. **[送信]** を選択してワークフローを保存します。

ソリューションのワークフローは約 24 時間実行されます。 テストをスケジュールするためのリンクを追加するか、その手順を追加してください。 ツール内でクリアします。

検証の実行に関する進行状況の監視について詳しくは、「[テストの監視](azure-stack-vaas-monitor-test.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

- [Azure Stack のサービスとしての検証](https://docs.microsoft.com/azure/azure-stack/partner)について、さらに詳しい情報をご覧ください。
