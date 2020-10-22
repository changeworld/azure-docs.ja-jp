---
title: Azure Security Center でのアラートの検証 | Microsoft Docs
description: Azure Security Center でセキュリティ アラートが正しく構成されていることを検証する方法について説明します
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 999888b12f10c07f7d42f14289e88030f9542a36
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340820"
---
# <a name="alert-validation-in-azure-security-center"></a>Azure Security Center でのアラートの検証
このドキュメントは、Azure Security Center のアラートに対してシステムが適切に構成されているかどうかを確認する方法を学ぶのに役立ちます。

## <a name="what-are-security-alerts"></a>セキュリティの警告とは何か
アラートは、Security Center がリソース上の脅威を検出したときに生成する通知です。 アラートは優先順位を付けられ、問題の迅速な調査に必要な情報と共に一覧表示されます。 Security Center は、攻撃をどのように修復できるかに関する推奨事項も提供します。
詳細については、[Security Center のセキュリティ アラート](security-center-alerts-overview.md)および[セキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)に関する記事をご覧ください

## <a name="validate-alerts-on-windows-vms"></a>Windows VM 上でのアラートの検証 <a name="validate-windows"></a>

Security Center エージェントをコンピューターにインストールした後、アラートの攻撃対象リソースとして使用するコンピューターから次の手順を実行します。

1. 実行可能ファイル (**calc.exe** など) をコンピューターのデスクトップまたは他の適切なディレクトリにコピーし、名前を **ASC_AlertTest_662jfi039N.exe** に変更します。
1. コマンド プロンプトを開き、引数 (単なるダミーの引数名) を指定してこのファイルを実行します。例: ```ASC_AlertTest_662jfi039N.exe -foo```
1. 5 ～ 10 分待って、Security Center のアラートを開きます。 アラートが表示されます。

> [!NOTE]
> Windows のこのテスト アラートを確認する場合は、 **[Arguments Auditing Enabled]\(引数の監査が有効\)** フィールドが **[true]** であることを確認してください。 それが **[false]** である場合は、コマンド ライン引数の監査を有効にする必要があります。 これを有効にするには、次のコマンドを使用します。
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Linux VM 上でのアラートの検証 <a name="validate-linux"></a>

Security Center エージェントをコンピューターにインストールした後、アラートの攻撃対象リソースとして使用するコンピューターから次の手順を実行します。
1. 実行可能ファイルを任意の場所にコピーし、名前を **./asc_alerttest_662jfi039n** に変更します。例:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. コマンド プロンプトを開き、このファイルを実行します。

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 5 ～ 10 分待って、Security Center のアラートを開きます。 アラートが表示されます。


## <a name="validate-alerts-on-kubernetes"></a>Kubernetes 上でのアラートの検証 <a name="validate-kubernetes"></a>

Azure Kubernetes Service と Security Center を統合してある場合は、次の kubectl コマンドを使用して、アラートが動作していることをテストできます。

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Kubernetes のノードとクラスターの防御の詳細については、「[Azure Defender for Kubernetes の概要](defender-for-kubernetes-introduction.md)」を参照してください

## <a name="next-steps"></a>次のステップ
この記事では、アラートの検証プロセスについて説明しました。 この検証について理解できたら、次の記事をお読みください。

* [Azure Security Center による Azure Key Vault 脅威検出](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md) - Security Center で、アラートの管理とセキュリティ インシデントへの対応を行う方法について説明しています。
* 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」 - Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](./security-center-alerts-overview.md) - さまざまな種類のセキュリティ アラートについて説明しています。