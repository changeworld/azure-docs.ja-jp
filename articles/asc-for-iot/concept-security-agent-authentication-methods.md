---
title: ASC for IoT プレビューの認証方法 | Microsoft Docs
description: ASC for IoT サービスを使用する際に使用可能なさまざまな認証方法について説明します。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 23bc4d0df1c8124ec225ac31239c7acb3f1ab546
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541523"
---
# <a name="security-agent-authentication-methods"></a>セキュリティ エージェントの認証方法 

> [!IMPORTANT]
> ASC for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、IoT Hub で認証するために AzureIoTSecurity エージェントで使用できるさまざまな認証方法について説明します。

IoT Hub で ASC for IoT にオンボードされたデバイスごとに、セキュリティ モジュールが必要です。 デバイスを認証するために、ASC for IoT では 2 つの方法のいずれかを使用できます。 既存の IoT ソリューションにとって最適な方法を選択してください。 

> [!div class="checklist"]
> * セキュリティ モジュール オプション
> * デバイス オプション

## <a name="authentication-methods"></a>認証方法

AzureIoTSecurity エージェントで認証を実行するための 2 つの方法:

 - **モジュール**認証モード<br>
   モジュールは、デバイス ツインとは別に認証されます。
   この種類の認証に必要な情報は、C# の Authentication.config ファイルと C の LocalConfiguration.json ファイルで定義されています。
        
 - **デバイス**認証モード<br>
    この方法では、セキュリティ エージェントによって最初にデバイスに対して認証が行われます。 初期認証後、ASC for IoT エージェントでは、デバイスの認証データと共に Rest API を使用して、IoT Hub への **Rest** 呼び出しが実行されます。 その後、ASC for IoT エージェントによって IoT Hub からセキュリティ モジュールの認証方法とデータが要求されます。 最後の手順では、ASC for IoT エージェントにより、ASC for IoT モジュールに対して認証が実行されます。    

構成方法については、[セキュリティ エージェントのインストール パラメーター](#security-agent-installation-parameters)に関するセクションを参照してください。
                                
## <a name="authentication-methods-known-limitations"></a>認証方法の既知の制限

- **モジュール**認証モードでは、対称キーの認証のみがサポートされています。
- **デバイス**認証モードでは、証明機関署名付き証明書はサポートされていません。  

## <a name="security-agent-installation-parameters"></a>セキュリティ エージェントのインストール パラメーター

[セキュリティ エージェントをデプロイする](select-deploy-agent.md)際には、認証の詳細を引数として指定する必要があります。
これらの引数を次の表に示します。


|パラメーター|説明|オプション|
|---------|---------------|---------------|
|**identity**|認証モード| **Module** または **Device**|
|**type**|認証の種類|**SymmetricKey** または **SelfSignedCertificate**|
|**filePath**|証明書または対称キーを含むファイルの完全な絶対パス| |
|**gatewayHostname**|IoT Hub の FQDN|例:ContosoIotHub.azure-devices.net|
|**deviceId**|Device ID|例:MyDevice1|
|**certificateLocationKind**|証明書ストレージの場所|**LocalFile** または **Store**|


セキュリティ エージェントのインストール スクリプトを使用すると、次の構成が自動的に実行されます。

セキュリティ エージェントの認証を手動で編集するには、構成ファイルを編集します。 

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
- [セキュリティ エージェントをデプロイする](select-deploy-agent.md)
- [未加工のセキュリティ データにアクセスする](how-to-security-data-access.md)
