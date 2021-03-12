---
title: セキュリティ エージェントの認証方法
description: Defender for IoT サービスを使用する際に使用可能なさまざまな認証方法について説明します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 85a5edc1485cb5a1eeb4428b201380ad9ca89422
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449171"
---
# <a name="security-agent-authentication-methods"></a>セキュリティ エージェントの認証方法

この記事では、IoT Hub で認証するために AzureIoTSecurity エージェントで使用できるさまざまな認証方法について説明します。

IoT ハブの Defender for IoT にオンボードされるデバイスごとに、セキュリティ モジュールが必要です。 デバイスを認証するために、Defender for IoT では 2 つの方法のいずれかを使用できます。 既存の IoT ソリューションにとって最適な方法を選択してください。

- SecurityModule オプション
- デバイス オプション

## <a name="authentication-methods"></a>認証方法

Defender for IoT AzureIoTSecurity エージェントで認証を実行するための 2 つの方法を次に示します。

- **SecurityModule** 認証モード<br>
エージェントは、デバイス ID とは無関係にセキュリティモジュール ID を使用して認証されます。
この認証の種類は、セキュリティ エージェントでセキュリティ モジュールを通して専用の認証方法 (対称キーのみ) を使用する場合に使用します。

- **デバイス** 認証モード<br>
この方法では、セキュリティ エージェントは最初にデバイス ID に対して認証します。 初期認証の後、Defender for IoT エージェントによって、REST API とデバイスの認証データを使用した IoT ハブへの **REST** 呼び出しが実行されます。 その後、Defender for IoT エージェントによって、セキュリティ モジュールの認証方法と IoT ハブからのデータが要求されます。 最後の手順では、Defender for IoT エージェントによって、Defender for IoT モジュールに対して認証が実行されます。

この認証の種類は、セキュリティ エージェントで既存のデバイス認証方法 (自己署名証明書または対称キー) を再利用する場合に使用します。

構成方法については、[セキュリティ エージェントのインストール パラメーター](#security-agent-installation-parameters)に関するセクションを参照してください。

## <a name="authentication-methods-known-limitations"></a>認証方法の既知の制限

- **SecurityModule** 認証モードでは、対称キーの認証のみがサポートされています。
- **デバイス** 認証モードでは、証明機関署名付き証明書はサポートされていません。

## <a name="security-agent-installation-parameters"></a>セキュリティ エージェントのインストール パラメーター

[セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)際には、認証の詳細を引数として指定する必要があります。
これらの引数を次の表に示します。

|Linux のパラメーター名 | Windows のパラメーター名 | 短縮形のパラメーター |説明|Options|
|---------------------|---------------|---------|---------------|---------------|
|authentication-identity|AuthenticationIdentity|aui|認証 ID| **SecurityModule** または **Device**|
|authentication-method|AuthenticationMethod|aum|認証方法|**SymmetricKey** または **SelfSignedCertificate**|
|file-path|FilePath|f|証明書または対称キーを含むファイルの完全な絶対パス| |
|host-name|HostName|hn|IoT Hub の FQDN|例:ContosoIotHub.azure-devices.net|
|device-id|deviceId|di|Device ID|例:MyDevice1|
|certificate-location-kind|CertificateLocationKind|cl|証明書ストレージの場所|**LocalFile** または **Store**|
|

セキュリティ エージェントのインストール スクリプトを使用すると、次の構成が自動的に実行されます。 セキュリティ エージェントの認証を手動で編集するには、構成ファイルを編集します。

## <a name="change-authentication-method-after-deployment"></a>デプロイ後に認証方法を変更する

インストール スクリプトを使用してセキュリティ エージェントをデプロイすると、構成ファイルが自動的に作成されます。

デプロイ後に認証方法を変更するには、構成ファイルを手動で編集する必要があります。

### <a name="c-based-security-agent"></a>C# ベースのセキュリティ エージェント

次のパラメーターを使用して、_Authentication.config_ を編集します。

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C ベースのセキュリティ エージェント

次のパラメーターを使用して、_LocalConfiguration.json_ を編集します。

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>関連項目

- [セキュリティ エージェントの概要](security-agent-architecture.md)
- [セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)
- [未加工のセキュリティ データにアクセスする](how-to-security-data-access.md)
