---
title: Azure Active Directory を使用して IoT Hub へのアクセスを制御する
description: 開発者ガイド。 Azure AD と Azure RBAC を使用してバックエンド アプリの IoT Hub へのアクセスを制御する方法。
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/20/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: a95501462fe1db0a06e6596054d09645c86ee64f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223532"
---
# <a name="control-access-to-iot-hub-by-using-azure-active-directory"></a>Azure Active Directory を使用して IoT Hub へのアクセスを制御する

Azure Active Directory (Azure AD) を使用して、デバイス ID の作成やダイレクト メソッドの呼び出しといった、Azure IoT Hub サービス API に対する要求を認証できます。 Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、これらの同じサービス API を認可することもできます。 これらのテクノロジを一緒に使用すると、IoT Hub サービス API にアクセスするためのアクセス許可を Azure AD のセキュリティ プリンシパルに付与できます。 このセキュリティ プリンシパルは、ユーザー、グループ、またはアプリケーション サービス プリンシパルの場合があります。

Azure AD を使用してアクセスを認証し、Azure RBAC を使用してアクセス許可を制御することで、[セキュリティ トークン](iot-hub-dev-guide-sas.md)よりも優れたセキュリティと使いやすさを実現できます。 セキュリティ トークンに固有の潜在的なセキュリティ問題を最小限に抑えるために、[可能な限り、お使いの IoT ハブでは Azure AD を使用する](#azure-ad-access-and-shared-access-policies)ことをお勧めします。 

> [!NOTE]
> Azure AD による認証は、IoT Hub の "*デバイス API*" (デバイスとクラウド間のメッセージや報告されたプロパティの更新など) ではサポートされていません。 IoT Hub に対してデバイスを認証するには、[対称キー](iot-hub-dev-guide-sas.md#use-a-symmetric-key-in-the-identity-registry)または [X.509](iot-hub-x509ca-overview.md) を使用してください。

## <a name="authentication-and-authorization"></a>認証と権限承認

Azure AD セキュリティ プリンシパルで IoT Hub サービス API へのアクセスを要求すると、プリンシパルの ID が最初に "*認証*" されます。 認証では、実行時にその要求に OAuth 2.0 アクセス トークンが含まれている必要があります。 トークンを要求する場合のリソース名は `https://iothubs.azure.net` です。 アプリケーションは、Azure VM、Azure Functions アプリ、Azure App Service アプリなどの Azure リソース内で実行される場合、[マネージド ID](../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md) として表すことができます。 

Azure AD プリンシパルが認証されたら、次の手順は "*認可*" です。 このステップでは、IoT Hub で Azure AD のロールの割り当てサービスを使用して、プリンシパルに付与されたアクセス許可を判別します。 プリンシパルのアクセス許可が要求されたリソースまたは API と一致する場合、IoT Hub によって要求が承認されます。 そのため、このステップでは、セキュリティ プリンシパルに 1 つ以上の Azure ロールを割り当てる必要があります。 IoT Hub には、一般的なアクセス許可グループを持つ組み込みのロールがいくつか用意されています。

## <a name="manage-access-to-iot-hub-by-using-azure-rbac-role-assignment"></a>Azure RBAC ロールの割り当てを使用して IoT Hub へのアクセスを管理する

Azure AD と RBAC を使用する場合、IoT Hub では、API を要求するプリンシパルに、承認のために適切なレベルのアクセス許可があることが求められます。 プリンシパルにアクセス許可を付与するには、ロールの割り当てを付与します。 

- プリンシパルがユーザー、グループ、またはアプリケーション サービス プリンシパルの場合、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」のガイドラインに従ってください。
- プリンシパルがマネージド ID の場合は、「[Azure portal を使用してリソースにマネージド ID アクセスを割り当てる](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)」のガイダンスに従ってください。

特権を最小限にするには、常に、可能な限り最も低い[リソースのスコープ](#resource-scope) (おそらく、IoT Hub のスコープ) で適切なロールを割り当てます。

IoT Hub には、Azure AD と RBAC を使って IoT Hub サービス API へのアクセスを認可するために、次の Azure 組み込みロールが用意されています。

| ロール | 説明 | 
| ---- | ----------- | 
| [IoT Hub データ共同作成者](../role-based-access-control/built-in-roles.md#iot-hub-data-contributor) | IoT Hub データ プレーン操作へのフル アクセスを許可します。 |
| [IoT Hub データ リーダー](../role-based-access-control/built-in-roles.md#iot-hub-data-reader) | IoT Hub データ プレーン プロパティへの読み取りのフル アクセスを許可します。 |
| [IoT Hub レジストリ共同作成者](../role-based-access-control/built-in-roles.md#iot-hub-registry-contributor) | IoT Hub デバイス レジストリへのフル アクセスを許可します。 |
| [IoT Hub ツイン共同作成者](../role-based-access-control/built-in-roles.md#iot-hub-twin-contributor) | すべての IoT Hub デバイスとモジュール ツインに対する読み取りおよび書き込みのアクセスを許可します。 |

必要な[アクセス許可](#permissions-for-iot-hub-service-apis)を組み合わせることで、IoT Hub で使用するカスタム ロールを定義することもできます。 詳細については、[Azure のロールベースのアクセス制御のためのカスタム ロールを作成する](../role-based-access-control/custom-roles.md)に関するページを参照してください。

### <a name="resource-scope"></a>リソースのスコープ

セキュリティ プリンシパルに Azure RBAC ロールを割り当てる前に、セキュリティ プリンシパルに必要なアクセスのスコープを決定します。 常にできるだけ狭いスコープのみを付与するのが最善の方法です。 より広い範囲で定義されている Azure RBAC ロールは、その下のリソースによって継承されます。

次の一覧で、IoT Hub へのアクセスのスコープとして指定できるレベルを、最も狭いスコープから順に示します。

- **IoT ハブ。** このスコープでは、ロールの割り当てが IoT ハブに適用されます。 個々の IoT ハブより小さいスコープはありません。 個々のデバイス ID やツイン セクションなど、より小さなスコープでのロールの割り当てはサポートされていません。
- **リソース グループ。** このスコープでは、ロールの割り当てがリソース グループ内のすべての IoT ハブに適用されます。
- **サブスクリプション。** このスコープでは、ロールの割り当てがサブスクリプションにあるすべてのリソース グループ内のすべての IoT ハブに適用されます。
- **管理グループ。** このスコープでは、ロールの割り当てが、管理グループ内のすべてのサブスクリプションにあるすべてのリソース グループ内のすべての IoT ハブに適用されます。

## <a name="permissions-for-iot-hub-service-apis"></a>IoT Hub サービス API のアクセス許可

次の表では、IoT Hub サービス API 操作で使用できるアクセス許可について説明します。 クライアントで特定の操作を呼び出せるようにするには、クライアントの割り当て済み RBAC ロールで、その操作に十分なアクセス許可が提供されることを確認します。

| RBAC アクション | 説明 |
|-|-|
| `Microsoft.Devices/IotHubs/devices/read` | デバイスまたはモジュール ID を読み取ります。 |
| `Microsoft.Devices/IotHubs/devices/write`  | デバイスまたはモジュール ID を作成または更新します。  |
| `Microsoft.Devices/IotHubs/devices/delete` | デバイスまたはモジュール ID を削除します。 |
| `Microsoft.Devices/IotHubs/twins/read` | デバイスまたはモジュール ツインを読み取ります。 |
| `Microsoft.Devices/IotHubs/twins/write` | デバイスまたはモジュール ツインを書き込みます。 |
| `Microsoft.Devices/IotHubs/jobs/read` | ジョブの一覧を返します。 |
| `Microsoft.Devices/IotHubs/jobs/write` | ジョブを作成または更新します。 |
| `Microsoft.Devices/IotHubs/jobs/delete` | ジョブを削除します。 |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/send/action` | cloud-to-device メッセージを任意のデバイスに送信します。  |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/feedback/action` | cloud-to-device メッセージのフィードバック通知を受信、完了、または破棄します。 |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/queue/purge/action` | デバイスの保留中のすべてのコマンドを削除します。  |
| `Microsoft.Devices/IotHubs/directMethods/invoke/action` | 任意のデバイスまたはモジュールでダイレクト メソッドを呼び出します。 |
| `Microsoft.Devices/IotHubs/fileUpload/notifications/action`  | ファイルのアップロード通知を受信、完了、または破棄します。 |
| `Microsoft.Devices/IotHubs/statistics/read` | デバイスとサービスの統計情報を読み取ります。 |
| `Microsoft.Devices/IotHubs/configurations/read` | デバイス管理構成を読み取ります。 |
| `Microsoft.Devices/IotHubs/configurations/write` | デバイス管理構成を作成または更新します。 |
| `Microsoft.Devices/IotHubs/configurations/delete` | デバイス管理構成を削除します。 |
| `Microsoft.Devices/IotHubs/configurations/applyToEdgeDevice/action`  | エッジ デバイスに構成内容を適用します。 |
| `Microsoft.Devices/IotHubs/configurations/testQueries/action` | 構成のターゲット条件およびカスタム メトリック クエリを検証します。 |

> [!TIP]
> - [一括レジストリ更新](/rest/api/iothub/service/bulkregistry/updateregistry)操作には、`Microsoft.Devices/IotHubs/devices/write` と `Microsoft.Devices/IotHubs/devices/delete` の両方が必要です。
> - [ツイン クエリ](/rest/api/iothub/service/query/gettwins)操作には `Microsoft.Devices/IotHubs/twins/read` が必要です。
> - [デジタル ツインの取得](/rest/api/iothub/service/digitaltwin/getdigitaltwin)には `Microsoft.Devices/IotHubs/twins/read` が必要です。 [デジタル ツインの更新](/rest/api/iothub/service/digitaltwin/updatedigitaltwin)には `Microsoft.Devices/IotHubs/twins/write` が必要です。
> - [コンポーネント コマンドの呼び出し](/rest/api/iothub/service/digitaltwin/invokecomponentcommand)と[ルート レベル コマンドの呼び出し](/rest/api/iothub/service/digitaltwin/invokerootlevelcommand)の両方に、`Microsoft.Devices/IotHubs/directMethods/invoke/action` が必要です。

> [!NOTE]
> Azure AD を使用して IoT Hub からデータを取得するには、[別のイベント ハブへのルーティングを設定](iot-hub-devguide-messages-d2c.md#event-hubs-as-a-routing-endpoint)します。 [組み込みの Event Hubs 互換エンドポイント](iot-hub-devguide-messages-read-builtin.md)にアクセスするには、以前のように接続文字列 (共有アクセス キー) 方式を使用します。 

## <a name="azure-ad-access-and-shared-access-policies"></a>Azure AD のアクセスと共有アクセスのポリシー

既定で IoT Hub では、Azure AD と、[共有アクセス ポリシーおよびセキュリティ トークン](iot-hub-dev-guide-sas.md)の両方を介したサービス API アクセスをサポートします。 セキュリティ トークンに固有の潜在的なセキュリティ脆弱性を最小限に抑えるために、共有アクセス ポリシーを使用してアクセスを無効にします。 

1. サービスのクライアントとユーザーが、IoT ハブへの[十分なアクセス権](#manage-access-to-iot-hub-by-using-azure-rbac-role-assignment)を持っていることを確認してください。 [最小限の特権の原則](../security/fundamentals/identity-management-best-practices.md)に従います。
1. [Azure portal](https://portal.azure.com) で IoT Hub に移動します。
1. 左側のペインで、 **[共有アクセス ポリシー]** を選択します。
1. **[共有アクセス ポリシーを使用して接続する]** で、 **[拒否]** を選択します。
    :::image type="content" source="media/iot-hub-dev-guide-azure-ad-rbac/disable-local-auth.png" alt-text="IoT Hub 共有アクセス ポリシーを無効にする方法を示すスクリーンショット。" border="true":::
1. 警告を確認し、 **[保存]** を選択します。

IoT Hub サービス API には、Azure AD と RBAC を使用してのみアクセスできるようになりました。

## <a name="azure-ad-access-from-the-azure-portal"></a>Azure portal からの Azure AD のアクセス

IoT Hub にアクセスしようとすると、最初に、Azure portal によって、`Microsoft.Devices/iotHubs/listkeys/action` を持つ Azure ロールが割り当てられているかどうかが確認されます。 そうである場合、Azure portal では IoT Hub にアクセスするために、共有アクセス ポリシーのキーが使用されます。 そうではない場合、Azure portal では、Azure AD アカウントを使用してデータへのアクセスが試みられます。 

Azure AD アカウントを使用して Azure portal から IoT Hub にアクセスするには、IoT Hub データ リソース (デバイスやツインなど) にアクセスするためのアクセス許可が必要です。 また、Azure portal 内の IoT Hub リソースに移動するためのアクセス許可も必要です。 IoT Hub によって提供される組み込みロールでは、デバイスやツインなどのリソースへのアクセスが付与されます。 ただし、IoT Hub リソースへのアクセスは付与されません。 そのため、ポータルへのアクセスには、[閲覧者](../role-based-access-control/built-in-roles.md#reader)などの Azure Resource Manager ロールの割り当ても必要です。 閲覧者のロールは、ポータルのナビゲートが可能な最も制限されたロールであるため、適切な選択肢です。 (共有アクセス ポリシーを介してすべての IoT Hub データ リソースへのアクセスを提供する) `Microsoft.Devices/iotHubs/listkeys/action` アクセス許可は含まれていません。 

アカウントで、割り当てられたアクセス許可の範囲外にアクセスできないようにするには、カスタム ロールの作成時に `Microsoft.Devices/iotHubs/listkeys/action` アクセス許可を含めないでください。 たとえば、デバイス ID の読み取りはできてもデバイスの作成や削除はできないカスタム ロールを作成するには、次のようなカスタム ロールを作成します。
- `Microsoft.Devices/IotHubs/devices/read` データ アクションを持つ。
- `Microsoft.Devices/IotHubs/devices/write` データ アクションを持たない。
- `Microsoft.Devices/IotHubs/devices/delete` データ アクションを持たない。
- `Microsoft.Devices/iotHubs/listkeys/action` アクションを持たない。

次に、`Microsoft.Devices/iotHubs/listkeys/action` アクセス許可を持つ他のロール ([所有者](../role-based-access-control/built-in-roles.md#owner)や[共同作成者](../role-based-access-control/built-in-roles.md#contributor)など) がアカウントにないことを確認します。 アカウントでリソースにアクセスでき、ポータルをナビゲートできるようにするには、[閲覧者](../role-based-access-control/built-in-roles.md#reader)を割り当てます。

## <a name="azure-iot-extension-for-azure-cli"></a>Azure CLI 用 Azure IoT 拡張機能

IoT Hub に対するほとんどのコマンドでは、Azure AD 認証がサポートされます。 `key` または `login` 値を受け取る `--auth-type` パラメーターを使用して、コマンドの実行に使用される認証の種類を制御できます。 `key` 値が既定値です。

- 前のように `--auth-type`の値が `key` の場合、CLI により、IoT Hub と対話するときに適切なポリシーが自動的に検出されます。

- `--auth-type` の値が `login` の場合、プリンシパルでログインした Azure CLI からのアクセス トークンがこの操作に使用されます。

詳細については、[Azure CLI 用の Azure IoT 拡張機能のリリース ページ](https://github.com/Azure/azure-iot-cli-extension/releases/tag/v0.10.12)を参照してください。

## <a name="sdk-samples"></a>SDK のサンプル

- [.NET Microsoft.Azure.Devices SDK のサンプル](https://aka.ms/iothubaadcsharpsample)
- [Java SDK のサンプル](https://aka.ms/iothubaadjavasample)

## <a name="next-steps"></a>次のステップ

- アプリケーションで Azure AD を使用する利点の詳細については、[Azure Active Directory との統合](../active-directory/develop/active-directory-how-to-integrate.md)に関する記事を参照してください。
- Azure AD からユーザーやサービス プリンシパルのアクセス トークンを要求する方法の詳細については、「[Azure AD の認証シナリオ](../active-directory/develop/authentication-vs-authorization.md)」をご覧ください。
