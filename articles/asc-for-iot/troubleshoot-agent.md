---
title: Azure Security Center for IoT Linux セキュリティ エージェントのトラブルシューティング ガイド| Microsoft Docs
description: Linux 用の Azure Security Center for IoT セキュリティ エージェントの使用に関するトラブルシューティング。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f3bd4be3ef927f73643146a457bc551ef86a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "68600318"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>セキュリティ エージェントのトラブルシューティング ガイド (Linux)

この記事では、セキュリティ エージェントの起動プロセスで発生する可能性のある問題の解決方法について説明します。

Azure Security Center for IoT エージェントは、インストールの直後に自己起動します。 エージェントの起動プロセスには、ローカル構成の読み取り、Azure IoT Hub への接続、およびリモート ツイン構成の取得が含まれます。 これらの手順のいずれかに失敗すると、セキュリティ エージェントでエラーが発生する可能性があります。

このトラブルシューティング ガイドでは、次の方法について説明します。
> [!div class="checklist"]
> * セキュリティ エージェントが実行されているかどうかを確認する
> * セキュリティ エージェントのエラーを取得する
> * セキュリティ エージェントのエラーについて理解し、修復を行う 

## <a name="validate-if-the-security-agent-is-running"></a>セキュリティ エージェントが実行されているかどうかを確認する

1. セキュリティ エージェントが実行されていることを確認するには、エージェントのインストール後、数分待機し、次のコマンドを実行します。 
     <br>

    **C エージェント**
    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```
    **C# エージェント**
    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```
2. コマンドにより空の行が返された場合、セキュリティ エージェントが正常に起動できなかったことになります。    

## <a name="force-stop-the-security-agent"></a>セキュリティ エージェントを強制的に停止する 
セキュリティ エージェントを起動できない場合は、次のコマンドを使用してエージェントを停止し、以下のエラー表に進みます。

```bash
systemctl stop ASCIoTAgent.service
```
## <a name="get-security-agent-errors"></a>セキュリティ エージェントのエラーを取得する
1. 次のコマンドを実行して、セキュリティ エージェントのエラーを取得します。
    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```
2. このセキュリティ エージェントのエラー取得コマンドにより、Azure Security Center for IoT エージェントによって作成されたすべてのログが取得されます。 次の表を使用して、エラーについて理解し、修復のための正しい手順を実行します。 

> [!Note]
> エラー ログは時系列順に表示されます。 修復に役立つように、各エラーのタイムスタンプに注意してください。 

## <a name="restart-the-agent"></a>エージェントを再起動する

1. セキュリティ エージェントのエラーを検出して修正した後、次のコマンドを実行してエージェントを再起動します。 
    ```bash
    systemctl restart ASCIoTAgent.service
    ```
1. エージェントの起動プロセスが引き続き失敗する場合は、前の手順を繰り返し、停止してからエラーを取得します。 

## <a name="understand-security-agent-errors"></a>セキュリティ エージェントのエラーを理解する

セキュリティ エージェントのエラーのほとんどは、次の形式で表示されます。 
```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```
| エラー コード | エラー サブ コード | エラーの詳細 | C での修復 | C# での修復 |
|:-----------|:---------------|:--------|:------------|:------------|
| Local Configuration (ローカル構成) | Missing configuration (構成の欠落) | ローカル構成ファイルに構成がありません。 エラー メッセージにより、どのキーが欠落しているか示されます。 | 欠落しているキーを /var/LocalConfiguration.json ファイルに追加します。詳細については、[cs-localconfig-reference](azure-iot-security-local-configuration-c.md) に関する記事を参照してください。| 欠落しているキーを General.config ファイルに追加します。詳細については、[c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md) に関する記事を参照してください。 |
| Local Configuration (ローカル構成) | Cant Parse Configuration (構成を解析できない) | 構成値を解析できません。 エラー メッセージにより、どのキーを解析できないかが示されます。 値が、予期された型ではないか、または範囲外であるため、構成値を解析できません。 | LocalConfiguration スキーマに一致するように /var/LocalConfiguration.json ファイルでキーの値を修正します。詳細については、[c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md) に関する記事を参照してください。 |  スキーマに一致するように General.config ファイルでキーの値を修正します。詳細については、[cs-localconfig-reference](azure-iot-security-local-configuration-c.md) に関する記事を参照してください。|
| Local Configuration (ローカル構成) | ファイル形式 | 構成ファイルを解析できませんでした。 | 構成ファイルが破損しています。エージェントをダウンロードして再インストールします。 | |
| Remote Configuration (リモート構成) | タイムアウト | エージェントが、タイムアウト期間内に azureiotsecurity モジュール ツインをフェッチできませんでした。 | 認証の構成が正しいことを確認し、再試行してください。 | エージェントが、タイムアウト期間内に azureiotsecurity モジュール ツインをフェッチできませんでした。 | 認証の構成が正しいことを確認し、再試行してください。 |
| 認証 | File Not Exist (ファイルが存在しない) | 指定されたパスにファイルが存在しません。 | 指定されたパスにファイルが存在することを確認します。または **LocalConfiguration.json** ファイルに移動して、**FilePath** 構成を変更します。 | 指定されたパスにファイルが存在することを確認します。または **Authentication.config** ファイルに移動して、**FilePath** 構成を変更します。|
| 認証 | File Permission (ファイルのアクセス許可) | エージェントに、ファイルを開くための十分なアクセス許可がありません。 | 指定されたパスのファイルへの読み取りアクセス許可を **asciotagent** ユーザーに付与します。 | ファイルにアクセスできることを確認します。 |
| 認証 | ファイル形式 | 指定されたファイルの形式が正しくありません。 | ファイルの形式が正しいことを確認します。 サポートされているファイル形式は .pfx と .pem です。 | ファイルが有効な証明書ファイルであることを確認します。 |
| 認証 | 権限がありません | エージェントが、指定された資格情報を使用して IoT Hub に対して認証できませんでした。 | LocalConfiguration ファイルで認証構成を検証し、認証構成全体を調べてすべての詳細が正しいことを確認し、ファイル内のシークレットが認証済み ID と一致することを確認します。 | Authentication.config で認証構成を検証し、認証構成全体を調べてすべての詳細が正しいことを確認し、ファイル内のシークレットが認証済み ID と一致することを確認します。
| 認証 | 見つかりません | デバイス/モジュールが検出されました。 | 認証構成を検証します - ホスト名が正しく、デバイスが IoT Hub に存在し、デバイスに azureiotsecurity ツイン モジュールがあることを確認します。 |  認証構成を検証します - ホスト名が正しく、デバイスが IoT Hub に存在し、デバイスに azureiotsecurity ツイン モジュールがあることを確認します。 |
| 認証 | Missing Configuration (構成の欠落) | *Authentication.config* ファイルに構成がありません。 エラー メッセージにより、どのキーが欠落しているか示されます。 | 欠落しているキーを *LocalConfiguration.json* ファイルに追加します。| 欠落しているキーを *Authentication.config* ファイルに追加します。詳細については、[c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md) に関する記事を参照してください。 |
| 認証 | Cant Parse Configuration (構成を解析できない) | 構成値を解析できません。 エラー メッセージにより、どのキーを解析できないかが示されます。 値が、予期された型ではないか、または範囲外であるため、構成値を解析できません。 |**LocalConfiguration.json** ファイル内のキーの値を修正します。 |スキーマに一致するように **Authentication.config** ファイルでキーの値を修正します。詳細については、[cs-localconfig-reference](azure-iot-security-local-configuration-c.md) に関する記事を参照してください。|
|

## <a name="restart-the-agent"></a>エージェントを再起動する
1. セキュリティ エージェントのエラーを検出して修正した後、次のコマンドを実行してエージェントを再起動します。

    ```bash
    systemctl restart ASCIoTAgent.service
    ```
2. エージェントの起動プロセスが引き続き失敗する場合、必要に応じて、前の手順を繰り返してエージェントを強制的に停止し、エラーを取得します。 

## <a name="next-steps"></a>次のステップ
- Azure Security Center for IoT サービスの[概要](overview.md)を読みます
- Azure Security Center for IoT の[アーキテクチャ](architecture.md)の詳細を確認します
- Azure Security Center for IoT の[サービス](quickstart-onboard-iot-hub.md)を有効にします
- Azure Security Center for IoT サービスの [FAQ](resources-frequently-asked-questions.md) を読みます
- [未加工のセキュリティ データ](how-to-security-data-access.md)にアクセスする方法を学習します
- [レコメンデーション](concept-recommendations.md)について理解します
- セキュリティの[アラート](concept-security-alerts.md)について理解します
