---
title: IoT Hub のアクセス制御とセキュリティ | Microsoft Docs
description: IoT Hub へのアクセスを制御する方法の概要には、AAD 統合および SAS オプションに関する詳細な記事へのリンクが含まれています。
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/15/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: eb28d0384f0daa4029319597c5f3680a185c4ab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727811"
---
# <a name="control-access-to-iot-hub"></a>IoT Hub へのアクセスの制御

この記事では、Azure IoT Hub をセキュリティで保護するためのオプションについて説明します。 IoT Hub では、"*アクセス許可*" を使用して、IoT Hub の各エンドポイントへのアクセス権を付与します。 次のアクセス許可により、機能に応じて IoT Hub へのアクセスを制限します。

IoT Hub へのアクセスを制御するには、次の 3 つの方法があります。

- サービス API 用の **Azure Active Directory (Azure AD) 統合**。 Azure では、AAD を使用した ID ベースの認証と、Azure のロールベースのアクセス制御 (Azure RBAC) を使用したきめ細かな承認が提供されます。 Azure AD と RBAC の統合は、IoT hub サービス API でのみサポートされています。 詳細については、「[Azure Active Directory を使用して IoT Hub へのアクセスを制御する](iot-hub-dev-guide-azure-ad-rbac.md)」を参照してください。
- **共有アクセス署名** を使用すると、アクセス許可をグループ化し、アクセス キーと署名されたセキュリティ トークンを使用して、これらをアプリケーションに付与することができます。 詳細については、[Shared Access Signature を使用した IoT Hub へのアクセス制御](iot-hub-dev-guide-sas.md)に関する記事を参照してください。 
- **デバイスごとのセキュリティ資格情報**。 各 IoT Hub には [ID レジストリ](iot-hub-devguide-identity-registry.md)が含まれます。この ID レジストリ内の各デバイスでは、デバイスのエンドポイントを対象として DeviceConnect アクセス許可を付与するセキュリティ資格情報を構成できます。 詳細については、「[デバイスを IoT Hub に認証](iot-hub-dev-guide-sas.md#authenticating-a-device-to-iot-hub)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory を使用して IoT Hub へのアクセスを制御する](iot-hub-dev-guide-azure-ad-rbac.md)
- [Shared Access Signature を使用して IoT Hub へのアクセスを制御する](iot-hub-dev-guide-sas.md)
- [デバイスを IoT Hub に認証](iot-hub-dev-guide-sas.md#authenticating-a-device-to-iot-hub)
