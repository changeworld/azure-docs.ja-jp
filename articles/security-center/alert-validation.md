---
title: Microsoft Defender for Cloud でのアラートの検証 |Microsoft Docs
description: Microsoft Defender for Cloud でセキュリティ アラートが正しく構成されていることを検証する方法について説明します
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0070bf9a071de8108fff21e0a1816c9d73f9a226
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092712"
---
# <a name="alert-validation-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud でのアラートの検証

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このドキュメントでは、Microsoft Defender for Cloud アラート用にシステムが正しく構成されていることを確認する方法について説明します。

## <a name="what-are-security-alerts"></a>セキュリティの警告とは何か
アラートは、リソースに対する脅威を検出するときに Defender for Cloud によって生成される通知です。 アラートは優先順位を付けられ、問題の迅速な調査に必要な情報と共に一覧表示されます。 Defender for Cloud には、攻撃を修復する方法に関する推奨事項も用意されています。
詳細については [、「Defender for Cloud のセキュリティ アラート」](alerts-overview.md) および「セキュリティ アラートの [管理と対応」を参照してください。](managing-and-responding-alerts.md)


## <a name="generate-sample-security-alerts"></a>サンプル セキュリティ アラートを生成する

[「Microsoft Defender for Cloud](managing-and-responding-alerts.md)でのセキュリティ アラートの管理と対応」で説明されている新しいプレビュー アラート エクスペリエンスを使用している場合は、Azure portal のセキュリティ アラート ページから数回クリックしてサンプル アラートを作成できます。

アラートのサンプルを使用して次の作業を行います。

- Microsoft Defender プランの価値と機能を評価する
- セキュリティ アラートに対して行った構成 (SIEM 統合、ワークフローの自動化、電子メール通知など) を検証する

アラートのサンプルを作成するには:

1. **サブスクリプション共同作成者** ロールを持つユーザーとして、アラート ページのツール バーから **[アラートのサンプルの作成]** を選択します。
1. サブスクリプションを選択します。
1. アラートを表示する関連する Microsoft Defender プランを選択します。 
1. **[アラートのサンプルの作成]** を選択します。

    :::image type="content" source="media/alert-validation/create-sample-alerts-procedures.png" alt-text="Microsoft Defender for Cloud でサンプル アラートを作成する手順。":::
    
    アラート のサンプルが作成されていることを知らせる通知が表示されます。

    :::image type="content" source="media/alert-validation/notification-sample-alerts-creation.png" alt-text="アラートのサンプルが生成されていることを示す通知。":::

    数分後に、[セキュリティ アラート] ページにアラートが表示されます。 また、Microsoft Defender for Cloud セキュリティ アラート (接続された VM、電子メール通知など) を受信するように構成した他の場所にも表示されます。

    :::image type="content" source="media/alert-validation/sample-alerts.png" alt-text="セキュリティ アラートの一覧でのアラートのサンプル。":::

    > [!TIP]
    > このアラートは、シミュレートされたリソースを対象とします。

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Azure VM でアラートをシミュレートする (Windows) <a name="validate-windows"></a>

Log Analytics エージェントがマシンにインストールされた後、アラートの攻撃されたリソースになるコンピューターから次の手順に従います。

1. 実行可能ファイル (**calc.exe** など) をコンピューターのデスクトップまたは他の適切なディレクトリにコピーし、名前を **ASC_AlertTest_662jfi039N.exe** に変更します。
1. コマンド プロンプトを開き、引数 (単なるダミーの引数名) を指定してこのファイルを実行します。例: ```ASC_AlertTest_662jfi039N.exe -foo```
1. 5 分から 10 分待ち、Defender for Cloud アラートを開きます。 アラートが表示されます。

> [!NOTE]
> Windows のこのテスト アラートを確認する場合は、 **[Arguments Auditing Enabled]\(引数の監査が有効\)** フィールドが **[true]** であることを確認してください。 それが **[false]** である場合は、コマンド ライン引数の監査を有効にする必要があります。 これを有効にするには、次のコマンドを使用します。
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Azure VM でアラートをシミュレートする (Linux) <a name="validate-linux"></a>

Log Analytics エージェントがマシンにインストールされた後、アラートの攻撃されたリソースになるコンピューターから次の手順に従います。

1. 実行可能ファイルを便利な場所にコピーし、名前を `./asc_alerttest_662jfi039n` に変更します。 次に例を示します。

    `cp /bin/echo ./asc_alerttest_662jfi039n`

1. コマンド プロンプトを開き、このファイルを実行します。

    `./asc_alerttest_662jfi039n testing eicar pipe`

1. 5 分から 10 分待ち、Defender for Cloud アラートを開きます。 アラートが表示されます。

## <a name="simulate-alerts-on-kubernetes"></a>Kubernetes でアラートをシミュレートする<a name="validate-kubernetes"></a>

Defender for Cloud とAzure Kubernetes Service統合している場合は、次の kubectl コマンドを使用してアラートが動作しているテストを行います。

`kubectl get pods --namespace=asc-alerttest-662jfi039n`

Kubernetes ノードとクラスターの防御の詳細については[、「Microsoft Defender for Kubernetes の概要」を参照してください](defender-for-kubernetes-introduction.md)。

## <a name="next-steps"></a>次のステップ
この記事では、アラートの検証プロセスについて説明しました。 この検証について理解できたら、次の記事をお読みください。

* [Microsoft Defender for Cloud Azure Key Vault脅威検出の検証](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Microsoft Defender for Cloud](managing-and-responding-alerts.md) でのセキュリティ アラートの管理と対応 - Defender for Cloud でアラートを管理し、セキュリティ インシデントに対応する方法について説明します。
* [Microsoft Defender for Cloud のセキュリティ アラート](./alerts-overview.md) について - さまざまな種類のセキュリティ アラートについて説明します。
