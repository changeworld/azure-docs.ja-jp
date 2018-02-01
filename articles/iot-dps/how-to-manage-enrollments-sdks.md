---
title: "Azure デバイス プロビジョニング サービス SDK でデバイスの登録を管理する | Microsoft Docs"
description: "IoT Hub Device Provisioning Service と Service SDK でデバイス登録を管理する方法"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 12/01/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: arjmands
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 14e353af82342bc7a580e1a0a02b8b4e29514fb9
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2018
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Azure デバイス プロビジョニング サービス SDK でデバイスの登録を管理する方法
"*デバイス登録*" では、ある時点でデバイス プロビジョニング サービスに登録できる、1 つのデバイスまたはデバイス グループのレコードが作成されます。 登録レコードには、必要な IoT ハブを含む、目的のデバイス初期構成がその登録の一部として含まれます。 この記事では、Azure IoT プロビジョニング サービス SDK を使ってプログラムでプロビジョニング サービスのデバイス登録を管理する方法を示します。  この SDK は、GitHub の Azure IoT SDK と同じリポジトリにあります。

## <a name="samples"></a>サンプル
この記事では、Azure IoT プロビジョニング サービス SDK を使ったプログラムによるプロビジョニング サービスのデバイス登録の管理に関する概念の概要を説明します。  API の正確な呼び出し方法は、言語によって異なる場合があります。  詳しくは、GitHub で提供されているサンプルをご覧ください。
* [Java によるプロビジョニング サービス クライアントのサンプル](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
* [Node.js によるプロビジョニング サービス クライアントのサンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
* [.NET によるプロビジョニング サービス クライアントのサンプル](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="prerequisites"></a>前提条件
* デバイス プロビジョニング サービス インスタンスからの接続文字列
* デバイス セキュリティ アーティファクト:
    * [**TPM**](https://docs.microsoft.com/azure/iot-dps/concepts-security):
        * 個別登録: 物理デバイスまたは TPM シミュレーターからの登録 ID と TPM 保証キー。
        * 登録グループは、TPM 構成証明には適用されません。
    * [**X.509**](https://docs.microsoft.com/azure/iot-dps/concepts-security):
        * 個別登録: 物理デバイスまたは DICE エミュレーターからの[リーフ証明書](https://docs.microsoft.com/azure/iot-dps/concepts-security#leaf-certificate)。
        * 登録グループ: 物理デバイスでデバイス証明書を生成するために使われる[ルート証明書](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate)または[中間証明書](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)。  DICE エミュレーターから生成することもできます。

## <a name="create-a-device-enrollment"></a>デバイス登録を作成する

プロビジョニング サービスにデバイスを登録する方法には次の 2 つがあります。

* **登録グループ**は、[ルート証明書](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate)または[中間証明書](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)によって署名された、X.509 証明書の構成証明メカニズムを共有するデバイス グループのエントリです。 必要な初期構成を共有する多数のデバイスがある場合や、すべてのデバイスが同じテナントに配置される場合は、登録グループを使用することをお勧めします。 "*登録グループ*" として X.509 構成証明メカニズムを使用するデバイスのみを登録することができます。 

    以下のワークフローに従って、SDK で登録グループを作成できます。

    1. 登録グループの場合、構成証明メカニズムは X.509 ルート証明書を使います。  ルート証明書を指定してサービス SDK の API ```X509Attestation.createFromRootCertificate``` を呼び出し、登録の構成証明を作成します。  X.509 ルート証明書は、PEM ファイルまたは文字列で提供されます。
    1. 作成された ```attestation``` と一意の ```enrollmentGroupId``` を使って、新しい ```EnrollmentGroup``` 変数を作成します。  必要に応じて、```Device ID```、```IoTHubHostName```、```ProvisioningStatus``` などのパラメーターを設定できます。
    2. バックエンド アプリケーションで ```EnrollmentGroup``` を指定してサービス SDK の API ```createOrUpdateEnrollmentGroup``` を呼び出し、登録グループを作成します。

* **個別登録**は、登録する単一のデバイスのエントリです。 個別加入では、構成証明メカニズムとして X.509 証明書または (実際の TPM または仮想 TPM の) SAS トークンを使用できます。 固有の初期構成を必要とするデバイスや、TPM または仮想 TPM を介した SAS トークンのみを構成証明メカニズムとして使用できるデバイスには、個別登録を使用することをお勧めします。 個別登録では、必要な IoT ハブ デバイス ID が指定されている場合があります。

    以下のワークフローに従って、SDK で個別登録を作成できます。
    
    1. ```attestation``` メカニズム (TPM または X.509) を選びます。
        1. **TPM**: 物理デバイスまたは TPM シミュレーターからの保証キーを入力して使い、サービス SDK の API ```TpmAttestation``` を呼び出して、登録の構成証明を作成できます。 
        2. **X.509**: クライアント証明書を入力として使い、サービス SDK の API ```X509Attestation.createFromClientCertificate``` を呼び出して、登録の構成証明を作成できます。
    2. 作成された ```attestation``` と一意の ```registrationId``` (デバイス上にあるか、または TPM シミュレーターから生成されます) を入力として使って、新しい ```IndividualEnrollment``` 変数を作成します。  必要に応じて、```Device ID```、```IoTHubHostName```、```ProvisioningStatus``` などのパラメーターを設定できます。
    3. バックエンド アプリケーションで ```IndividualEnrollment``` を指定してサービス SDK の API ```createOrUpdateIndividualEnrollment``` を呼び出し、個別登録を作成します。

登録が正常に作成されると、デバイス プロビジョニング サービスは登録結果を返します。

このワークフローについては、[サンプル](#samples)をご覧ください。

## <a name="update-an-enrollment-entry"></a>登録エントリを更新する

登録エントリを作成した後、登録の更新が必要になることがあります。  たとえば、必要なプロパティの更新、構成証明方法の更新、デバイス アクセスの取り消しなどです。  個別登録とグループ登録には異なる API がありますが、構成証明メカニズムに違いはありません。

次のワークフローに従って、登録エントリを更新できます。
* **個々の登録**：
    1. 最初に、サービス SDK の API ```getIndividualEnrollment``` を使って、プロビジョニング サービスから最新の登録を取得します。
    2. 必要に応じて、最新の登録のパラメーターを変更します。 
    3. 最新の登録を使い、サービス SDK の API ```createOrUpdateIndividualEnrollment``` を呼び出して、登録エントリを更新します。
* **グループ登録**:
    1. 最初に、サービス SDK の API ```getEnrollmentGroup``` を使って、プロビジョニング サービスから最新の登録を取得します。
    2. 必要に応じて、最新の登録のパラメーターを変更します。
    3. 最新の登録を使い、サービス SDK の API ```createOrUpdateEnrollmentGroup``` を呼び出して、登録エントリを更新します。

このワークフローについては、[サンプル](#samples)をご覧ください。

## <a name="remove-an-enrollment-entry"></a>登録エントリを削除する

* **個別登録**は、```registrationId``` を使ってサービス SDK の API ```deleteIndividualEnrollment``` を呼び出すことにより削除できます。
* **グループ登録**は、```enrollmentGroupId``` を使ってサービス SDK の API ```deleteEnrollmentGroup``` を呼び出すことにより削除できます。

このワークフローについては、[サンプル](#samples)をご覧ください。

## <a name="bulk-operation-on-individual-enrollments"></a>個別登録の一括操作

以下のワークフローで示す一括操作を実行して、複数の個別登録を作成、更新、または削除できます。

1. 複数の ```IndividualEnrollment``` を含む変数を作成します。  この変数の実装は、言語ごとに異なります。  詳しくは、GitHub の一括操作サンプルをご覧ください。
2. 目的の操作の ```BulkOperationMode``` と個別登録の変数を指定して、サービス SDK の API ```runBulkOperation``` を呼び出します。 create、update、updateIfMatchEtag、delete の 4 つのモードがサポートされています。

操作が正常に実行されると、デバイス プロビジョニング サービスは一括操作の結果を返します。

このワークフローについては、[サンプル](#samples)をご覧ください。
