---
title: 条件付きアクセスポリシーでデバイスを条件としてフィルター処理します。-Azure Active Directory
description: 条件付きアクセスでデバイスのフィルターを使用してセキュリティ体制を強化します。
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: da1ecbbc9f4d6b318c7829b946da5495104e4632
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135445"
---
# <a name="conditional-access-filter-for-devices"></a>条件付きアクセス: デバイスのフィルター

条件付きアクセス ポリシーを作成する際、管理者は、環境内の特定のデバイスを対象または対象外とする機能を求めています。 デバイスの条件フィルターは、管理者にこの機能を提供します。 [デバイスフィルターのサポートされている演算子とプロパティ](#supported-operators-and-device-properties-for-filters)、および条件付きアクセスポリシーで使用できるその他の割り当て条件を使用して、特定のデバイスを対象にすることができるようになりました。

:::image type="content" source="media/concept-condition-filters-for-devices/create-filter-for-devices-condition.png" alt-text="条件付きアクセス ポリシーで、デバイスのフィルター条件を作成する":::

## <a name="common-scenarios"></a>一般的なシナリオ

デバイスの条件にフィルターを使用して、組織で有効にできるシナリオが複数あります。 次に、いくつかの主要なシナリオで、この新しい条件をどのように使用するのかについて例を示します。

- Microsoft Azure Management のような特権リソースへのアクセスを、[特権またはセキュリティで保護された管理ワークステーション](/security/compass/privileged-access-devices)からアクセスする特権ユーザーに制限します。 このシナリオでは、組織は次の 2 つの条件付きアクセス ポリシーを作成します。
   - ポリシー 1: グローバル管理者のディレクトリロールを持つすべてのユーザー、Microsoft Azure 管理クラウドアプリへのアクセス、およびアクセス制御のためのアクセス許可が付与されますが、多要素認証を必要とし、デバイスに準拠としてマークを付ける必要があります。
   - ポリシー 2: グローバル管理者のディレクトリロールを持つすべてのユーザーが、Microsoft Azure 管理クラウドアプリにアクセスします。これには、ルール式 extensionAttribute1 を使用したデバイスのフィルターは含まれません。これは、アクセス制御の場合は、ブロックされます。
- Windows 7 など、サポートされていないオペレーティング システム バージョンを実行しているデバイスから組織のリソースへのアクセスをブロックします。 このシナリオでは、組織は次の 2 つの条件付きアクセス ポリシーを作成します。
   - ポリシー 1: すべてのユーザーが、すべてのクラウド アプリにアクセスし、アクセス制御についてアクセス権を付与しますが、デバイスは準拠としてマーク済みである必要がある、またはデバイスをハイブリッド Azure AD 参加済みにする必要があります。
   - ポリシー 2: すべてのクラウドアプリにアクセスするすべてのユーザーや、ルール式オペレーティングシステムを使用するデバイスのフィルターを含むすべてのユーザーが Windows と operatingSystemVersion startsWith "6.1" とアクセス制御のブロックに一致します。
- Teams 電話や Surface Hub デバイスなどの特定のデバイスで使用する場合は、サービスアカウントのような特定のアカウントに対して多要素認証を必要としません。 このシナリオでは、組織は次の 2 つの条件付きアクセス ポリシーを作成します。
   - ポリシー 1: サービスアカウントを除くすべてのユーザー、すべてのクラウドアプリへのアクセス、およびアクセス制御のためにアクセスを許可しますが、多要素認証が必要です。
   - ポリシー 2: [ユーザーとグループ] を選択し、[サービスアカウントのみを含むグループを含める]を選択します。ルール式デバイスを使用してデバイスのフィルターを除くすべてのクラウドアプリにアクセスします。 User.extensionattribute2 not は TeamsPhoneDevice に等しく、アクセス制御でブロックされます。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

デバイスのフィルターは、Azure portal で条件付きアクセスポリシーを作成する場合、または Microsoft Graph API を使用する場合のオプションです。

> [!IMPORTANT]
> デバイスの状態とデバイスのフィルターは、条件付きアクセスポリシーで一緒に使用することはできません。

次の手順を利用して、[一般的なシナリオ](#common-scenarios)の最初のシナリオをサポートする 2 つの条件付きアクセス ポリシーを作成します。 

ポリシー 1: グローバル管理者のディレクトリロールを持つすべてのユーザー、Microsoft Azure 管理クラウドアプリへのアクセス、およびアクセス制御のためのアクセス許可が付与されますが、多要素認証を必要とし、デバイスに準拠としてマークを付ける必要があります。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[対象]** で、 **[ディレクトリ ロール]** を選択し、 **[全体管理者]** を選択します。
   
      > [!WARNING]
      > 条件付きアクセス ポリシーでは、組み込みロールがサポートされています。 条件付きアクセス ポリシーは、[管理単位スコープ](../roles/admin-units-assign-roles.md)や[カスタム ロール](../roles/custom-create.md)など、その他の種類のロールには適用されません。

   1. **[除外]** で、 **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。 
   1. **[Done]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[対象]** で、 **[アプリを選択]** を選択し、 **[Microsoft Azure Management]** を選択します。
1. **[アクセス制御]**  >  **[許可]** で **[アクセス権の付与]** 、 **[多要素認証を要求する]** 、 **[デバイスは準拠としてマーク済みである必要があります]** を選択して、 **[選択]** を選択します。
1. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

ポリシー 2: グローバル管理者のディレクトリロールを持つすべてのユーザーが、Microsoft Azure 管理クラウドアプリにアクセスします。これには、ルール式 extensionAttribute1 を使用したデバイスのフィルターは含まれません。これは、アクセス制御の場合は、ブロックされます。

1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[対象]** で、 **[ディレクトリ ロール]** を選択し、 **[全体管理者]** を選択します。
   
      > [!WARNING]
      > 条件付きアクセス ポリシーでは、組み込みロールがサポートされています。 条件付きアクセス ポリシーは、[管理単位スコープ](../roles/admin-units-assign-roles.md)や[カスタム ロール](../roles/custom-create.md)など、その他の種類のロールには適用されません。

   1. **[除外]** で、 **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。 
   1. **[Done]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[対象]** で、 **[アプリを選択]** を選択し、 **[Microsoft Azure Management]** を選択します。
1. [ **条件**] で、 **デバイスをフィルター処理** します。
   1. **[構成]** を **[はい]** に切り替えます。
   1. **[Devices matching the rule]\(ルールに一致するデバイス\)** を **[Exclude filtered devices from policy]\(フィルター選択されたデバイスをポリシーから除外する\)** に設定します。
   1. プロパティを `ExtensionAttribute1`、演算子を `Equals`、値を `SAW` に設定します。
   1. **[Done]** を選択します。
1. **[アクセス制御]**  >  **[許可]** で、 **[アクセスのブロック]** 、 **[選択]** の順に選択します。
1. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

### <a name="setting-attribute-values"></a>属性値の設定

拡張属性の設定は、Graph API によって実現されています。 デバイス属性の設定の詳細については、「[デバイスの更新](/graph/api/device-update?view=graph-rest-1.0&tabs=http#example-2--write-extensionattributes-on-a-device)」という記事を参照してください。

### <a name="filter-for-devices-graph-api"></a>デバイスのフィルター処理 Graph API

デバイス API のフィルターは Microsoft Graph v1.0 エンドポイントで使用してアクセスでき https://graph.microsoft.com/v1.0/identity/conditionalaccess/policies/ ます。 新しい条件付きアクセスポリシーを作成するときにデバイスのフィルターを構成できます。また、既存のポリシーを更新して、デバイスのフィルターを構成することもできます。 既存のポリシーを更新するには、既存のポリシーのポリシー ID を追加し、次の要求本文を実行することで、前述の Microsoft Graph v1.0 エンドポイントでパッチ呼び出しを実行できます。 次の例では、SAWデバイスとして認識していないデバイスを除外するデバイス条件を設定します。 ルール構文は、複数の単一式で構成できます。 構文の詳細については、「 [Azure Active Directory のグループのダイナミックメンバーシップ規則](../enterprise-users/groups-dynamic-membership.md)」を参照してください。 

```json
{
    "conditions": {
        "devices": {
            "deviceFilter": {
                "mode": "exclude",
                "rule": "device.extensionAttribute1 -ne \"SAW\""
            }
        }
    }
}
```

## <a name="supported-operators-and-device-properties-for-filters"></a>フィルターでサポートされている演算子とデバイスのプロパティ

次のデバイス属性は、条件付きアクセスでデバイスのフィルター条件と共に使用できます。

| サポートされているデバイス属性 | サポートされている演算子 | サポート状況の値 | 例 |
| --- | --- | --- | --- |
| deviceId | Equals、NotEquals、In、NotIn | GUID である有効な deviceId | (device.deviceid -eq “498c4de7-1aee-4ded-8d5d-000000000000”) |
| displayName | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 任意の文字列 | (device.displayName -contains “ABC”) |
| deviceOwnership | Equals、NotEquals | サポートされている値は、個人のデバイスを取り込む場合は「個人」、企業所有のデバイスを所有する場合は「会社」です。  | (device.deviceOwnership -eq “Company”) |
| isCompliant | Equals、NotEquals | 準拠デバイスの場合は "True"、準拠していないデバイスの場合は "False" がサポートされている値です。  | (device.isCompliant -eq “True”) |
| manufacturer | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 任意の文字列 | (device.manufacturer -startsWith “Microsoft”) |
| mdmAppId | Equals、NotEquals、In、NotIn | 有効な MDM アプリケーション ID | (device.mdmAppId -in [“0000000a-0000-0000-c000-000000000000”]) |
| model | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 任意の文字列 | (device.model -notContains “Surface”) |
| operatingSystem | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 有効なオペレーティング システム (Windows、iOS、Android など) | (device.operatingSystem -eq “Windows”) |
| operatingSystemVersion | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 有効なオペレーティング システムのバージョン (Windows 7 の場合は 6.1、Windows 8 の場合は 6.2、Windows 10 の場合は 10.0、Windows 11 など) | (device.operatingSystemVersion -in [“10.0.18363”, “10.0.19041”, “10.0.19042”, “10.0.22000”]) |
| physicalIds | Contains、NotContains | たとえば、Windows オートパイロット デバイスにはすべて、デバイスの physicalIds プロパティに ZTDId (インポートされたすべての Windows オートパイロット デバイスに割り当てられた一意の値) が格納されています。 | (devicePhysicalIDs-contains "[ZTDId]: value") |
| profileType | Equals、NotEquals | デバイスに設定されている有効なプロファイルの種類。 サポートされている値は、RegisteredDevice (既定値)、SecureVM (Azure AD サインインで有効になっている Azure の Windows VM に使用)、プリンター (プリンターに使用)、共有 (共有デバイスに使用)、IoT (IoT デバイスに使用) | (device.profileType -notIn [“Printer”, “Shared”, “IoT”]) |
| systemLabels | Contains、NotContains | システムによってデバイスに適用されているラベルの一覧。 サポートされている値は、AzureResource (Azure AD サインインで有効になっている Azure の Windows VM に使用)、M365Managed (Microsoft マネージド デスクトップを使用して管理されるデバイスに使用)、MultiUser (共有デバイスに使用) など | (device.systemLabels - "M365Managed" を含む) |
| trustType | Equals、NotEquals | デバイスの有効な登録済み状態。 サポートされている値は、AzureAD (Azure AD 参加デバイスに使用)、ServerAD (Hybrid Azure AD 参加済みデバイスに使用)、Workplace (Azure AD 登録済みデバイスに使用) | (device.trustType -notIn ‘ServerAD, Workplace’) |
| extensionAttribute1-15 | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | extensionAttributes1-15 は、お客様がデバイス オブジェクトに使用できる属性です。 顧客は、カスタム値を使用して extensionAttributes1 を15から更新し、条件付きアクセスのデバイスのフィルター条件でそれらを使用できます。 任意の文字列値を使用できます。 | (device.extensionAttribute1 -eq ‘SAW’) |

## <a name="policy-behavior-with-filter-for-devices"></a>デバイスのフィルターを使用したポリシービヘイビアー

条件付きアクセスのデバイスのフィルター条件は、Azure AD で登録されているデバイスのデバイス属性に基づいてポリシーを評価するため、ポリシーが適用されるか適用されないかの状況を理解することが重要です。 次の表は、デバイスのフィルター条件が構成されている場合のビヘイビアーの例です。 

| デバイスの条件をフィルター処理する | デバイスの登録状態 | デバイス フィルターの適用 
| --- | --- | --- |
| 対象/対象外モードで正の演算子 (Equals、StartsWith、EndsWith、Contains、In) を指定し、任意の属性を使用する | 未登録のデバイス | No |
| 対象/対象外モードで正の演算子 (Equals、StartsWith、EndsWith、Contains、In) を指定し、extensionAttributes1-15 を対象外とする属性を使用する | 登録済みのデバイス | はい (条件が満たされている場合) |
| 対象/対象外モードで正の演算子 (Equals、StartsWith、EndsWith、Contains、In) を指定し、extensionAttributes1-15 を対象とする属性を使用する | Intune で管理されている登録済みデバイス | はい (条件が満たされている場合) |
| 対象/対象外モードで正の演算子 (Equals、StartsWith、EndsWith、Contains、In) を指定し、extensionAttributes1-15 を対象とする属性を使用する | Intune で管理されていない登録済みデバイス | はい (条件が満たされている場合、かつ、デバイスが準拠している、または Hybrid Azure AD 参加済みの場合) |
| 対象/対象外モードで負の演算子 (NotEquals、NotStartsWith、NotEndsWith、NotContains、NotIn) を指定し、任意の属性を使用する | 未登録のデバイス | Yes |
| 対象/対象外モードで負の演算子 (NotEquals、NotStartsWith、NotEndsWith、NotContains、NotIn) を指定し、extensionAttributes1-15 を対象外とする任意の属性を使用する | 登録済みのデバイス | はい (条件が満たされている場合) |
| 対象/対象外モードで負の演算子 (NotEquals、NotStartsWith、NotEndsWith、NotContains、NotIn) を指定し、extensionAttributes1-15 を対象とする任意の属性を使用する | Intune で管理されている登録済みデバイス | はい (条件が満たされている場合) |
| 対象/対象外モードで負の演算子 (NotEquals、NotStartsWith、NotEndsWith、NotContains、NotIn) を指定し、extensionAttributes1-15 を対象とする任意の属性を使用する | Intune で管理されていない登録済みデバイス | はい (条件が満たされている場合、かつ、デバイスが準拠している、または Hybrid Azure AD 参加済みの場合) |

## <a name="next-steps"></a>次のステップ

- [デバイスを更新する Graph API](/graph/api/device-update?view=graph-rest-1.0&tabs=http)
- [条件付きアクセス:条件](concept-conditional-access-conditions.md)
- [一般的な条件付きアクセス ポリシー](concept-conditional-access-policy-common.md)
- [特権アクセスの一部としてデバイスをセキュリティで保護する](/security/compass/privileged-access-devices)
