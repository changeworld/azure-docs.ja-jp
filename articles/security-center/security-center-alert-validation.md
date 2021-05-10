---
title: Azure Security Center でのアラートの検証 | Microsoft Docs
description: Azure Security Center でセキュリティ アラートが正しく構成されていることを検証する方法について説明します
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: b90ca39d7bf01b84400464240bb581a5e7bc922a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602385"
---
# <a name="alert-validation-in-azure-security-center"></a>Azure Security Center でのアラートの検証
このドキュメントは、Azure Security Center のアラートに対してシステムが適切に構成されているかどうかを確認する方法を学ぶのに役立ちます。

## <a name="what-are-security-alerts"></a>セキュリティの警告とは何か
アラートは、Security Center がリソース上の脅威を検出したときに生成する通知です。 アラートは優先順位を付けられ、問題の迅速な調査に必要な情報と共に一覧表示されます。 Security Center は、攻撃をどのように修復できるかに関する推奨事項も提供します。
詳細については、[Security Center のセキュリティ アラート](security-center-alerts-overview.md)および[セキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)に関する記事をご覧ください


## <a name="generate-sample-azure-defender-alerts"></a>Azure Defender アラートのサンプルを生成する

「[Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)」で説明されている新しいアラート エクスペリエンス (プレビュー) を使用している場合は、Azure portal の [セキュリティ アラート] ページから数回のクリックでアラートのサンプルを作成できます。

アラートのサンプルを使用して次の作業を行います。

- Azure Defender の価値と機能を評価する
- セキュリティ アラートに対して行った構成 (SIEM 統合、ワークフローの自動化、電子メール通知など) を検証する

アラートのサンプルを作成するには:

1. **セキュリティ管理者** または **サブスクリプション共同作成者** のロールを持つユーザーで、アラート ページのツールバーから **[アラートのサンプルの作成]** を選択します。
1. サブスクリプションを選択します。
1. アラートを表示する関連する Azure Defender プラン (複数可) を選択します。 
1. **[アラートのサンプルの作成]** を選択します。

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="Azure Security Center でアラートのサンプルを作成する手順":::
    
    アラート のサンプルが作成されていることを知らせる通知が表示されます。

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="アラートのサンプルが生成されていることを示す通知。":::

    数分後に、[セキュリティ アラート] ページにアラートが表示されます。 また、Azure Security Center セキュリティ アラート (接続された SIEM、電子メール通知など) を受信するように構成した他の場所にも表示されます。

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="セキュリティ アラートの一覧でのアラートのサンプル":::

    > [!TIP]
    > このアラートは、シミュレートされたリソースを対象とします。

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Azure VM でアラートをシミュレートする (Windows) <a name="validate-windows"></a>

Security Center エージェントをコンピューターにインストールした後、アラートの攻撃対象リソースとして使用するコンピューターから次の手順を実行します。

1. 実行可能ファイル (**calc.exe** など) をコンピューターのデスクトップまたは他の適切なディレクトリにコピーし、名前を **ASC_AlertTest_662jfi039N.exe** に変更します。
1. コマンド プロンプトを開き、引数 (単なるダミーの引数名) を指定してこのファイルを実行します。例: ```ASC_AlertTest_662jfi039N.exe -foo```
1. 5 ～ 10 分待って、Security Center のアラートを開きます。 アラートが表示されます。

> [!NOTE]
> Windows のこのテスト アラートを確認する場合は、 **[Arguments Auditing Enabled]\(引数の監査が有効\)** フィールドが **[true]** であることを確認してください。 それが **[false]** である場合は、コマンド ライン引数の監査を有効にする必要があります。 これを有効にするには、次のコマンドを使用します。
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Azure VM でアラートをシミュレートする (Linux) <a name="validate-linux"></a>

Security Center エージェントをコンピューターにインストールした後、アラートの攻撃対象リソースとして使用するコンピューターから次の手順を実行します。
1. 実行可能ファイルを任意の場所にコピーし、名前を **./asc_alerttest_662jfi039n** に変更します。例:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. コマンド プロンプトを開き、このファイルを実行します。

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 5 ～ 10 分待って、Security Center のアラートを開きます。 アラートが表示されます。


## <a name="simulate-alerts-on-kubernetes"></a>Kubernetes でアラートをシミュレートする<a name="validate-kubernetes"></a>

Azure Kubernetes Service と Security Center を統合してある場合は、次の kubectl コマンドを使用して、アラートが動作していることをテストできます。

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Kubernetes のノードとクラスターの防御の詳細については、「[Azure Defender for Kubernetes の概要](defender-for-kubernetes-introduction.md)」を参照してください

## <a name="next-steps"></a>次のステップ
この記事では、アラートの検証プロセスについて説明しました。 この検証について理解できたら、次の記事をお読みください。

* [Azure Security Center による Azure Key Vault 脅威検出](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md) - Security Center で、アラートの管理とセキュリティ インシデントへの対応を行う方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](./security-center-alerts-overview.md) - さまざまな種類のセキュリティ アラートについて説明しています。