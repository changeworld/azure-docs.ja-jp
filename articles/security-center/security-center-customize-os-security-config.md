---
title: "Azure Security Center での OS セキュリティ構成のカスタマイズ [プレビュー] | Microsoft Docs"
description: "この記事では、Security Center の評価をカスタマイズする方法について説明します"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: 2fa63515d290e6700fbe4a90ae509f4635b19f29
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2018
---
# <a name="customizing-os-security-configurations-in-azure-security-center-preview"></a>Azure Security Center での OS セキュリティ構成のカスタマイズ [プレビュー]

このチュートリアルでは、Azure Security Center で OS セキュリティ構成の評価をカスタマイズする方法について説明します。

## <a name="what-are-os-security-configurations"></a>OS セキュリティ構成とは

Azure Security Center は、ファイアウォール、監査、パスワード ポリシーなどに関連する規則を含め、OS を強化するための 150 を超える推奨される規則のセットを使用してセキュリティ構成を監視します。 脆弱な構成を持つマシンが見つかった場合は、セキュリティに関する推奨事項が生成されます。

規則のカスタマイズは、どの構成オプションが環境により適しているかを組織がコントロールするのに役立ちます。 この機能により、ユーザーはカスタマイズされた評価ポリシーを設定して、サブスクリプション内のすべての該当するマシンに適用することができます。

> [!NOTE]
> - 現在、OS セキュリティ構成のカスタマイズは Windows Server 2008、2008R2、2012、2012R2 のオペレーティング システムのみで使用できます。
- 構成は、選択したサブスクリプションの下にあるすべてのワークスペースに接続されたすべての VM とコンピューターに適用されます。
- OS セキュリティ構成のカスタマイズは、Security Center の Standard レベルでのみ使用できます。
>
>

OS セキュリティ構成規則をカスタマイズする方法

特定の規則の有効化と無効化、既存の規則の目的の設定の変更、サポートされている規則の種類 (レジストリ、監査ポリシー、セキュリティ ポリシー) に基づく新しい規則の追加によって、OS セキュリティ構成規則をカスタマイズできます。 現時点では、目的の設定は正確な値にする必要があります。

新しい規則は、同じ種類の他の既存の規則と同じ形式および構造にする必要があります。

> [!NOTE]
> OS セキュリティ構成をカスタマイズするには、サブスクリプションの所有者、サブスクリプションの共同作成者、またはセキュリティ管理者の役割が割り当てられている必要があります。
>
>

## <a name="customize-security-configuration"></a>セキュリティ構成のカスタマイズ

Security Center で既定の OS セキュリティ構成をカスタマイズするには:

1.  **[Security Center]** ダッシュボードを開きます。

2.  Security Center のメイン メニューで、**[セキュリティ ポリシー]** を選択します。  **[Security Center - セキュリティ ポリシー]** が開きます。

3.  カスタマイズを実行するサブスクリプションを選びます。

    ![](media/security-center-customize-os-security-config/open-security-policy.png)

4. **[ポリシー コンポーネント]** で **[セキュリティ構成の編集]** を選択します。

6.  **[セキュリティ構成の編集]** が開きます。 画面で強調表示されている手順に従って、ファイルをダウンロードして編集し、変更したファイルをアップロードします。

    ![](media/security-center-customize-os-security-config/blade.png)

  > [!NOTE]
  > 既定では、ダウンロードする構成ファイルは *json* 形式です。 このファイルを変更する手順については、「[構成ファイルのカスタマイズ](#customize-the-configuration-file)」をご覧ください。
  >

7. ファイルを正常に保存すると、構成は、選択したサブスクリプションの下にあるすべてのワークスペースに接続されたすべての VM とコンピューターに適用されます。 このプロセスには時間かかる場合があり、通常は数分ですが、インフラストラクチャのサイズによってはさらに時間がかかることがあります。 **[保存]** を選択して変更をコミットします。そうしないとポリシーは保存されません。

    ![](media/security-center-customize-os-security-config/save-successfully.png)

任意の時点で、**[OS セキュリティ構成規則の編集]** の **[リセット]** オプションを選択して、現在のポリシー構成を既定のポリシー状態にリセットできます。 このオプションを選択すると、次のポップアップ警告が表示されます。 **[はい]** を選択して確定します。

![](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>構成ファイルのカスタマイズ

カスタマイズ ファイルでは、サポートされている各 OS バージョンに一連の規則 (規則セット) があります。 各規則セットには、次の例に示すように独自の名前と一意 ID があります。

![](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> このファイルは Visual Studio を使用して編集されていますが、JSON ビューアー プラグインがインストールされていればメモ帳を使用することもできます。
>
>

このファイルを編集するときは、1 つの規則またはすべての規則を変更できます。 各規則セットには、次に示すようにレジストリ、監査ポリシー、セキュリティ ポリシーの 3 つのカテゴリに分類される規則を格納する *rules* セクションが含まれています。

![](media/security-center-customize-os-security-config/rules-section.png)

各カテゴリには、独自の属性セットがあります。 既存の規則では、以下の属性を変更できます。

- expectedValue: この属性のフィールド データの種類は規則の種類ごとにサポートされている値と一致する必要があります。次に例を示します。

  - baselineRegistryRules: 値はその規則で定義されている [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884(v=vs.85) と一致する必要があります。

  - baselineAuditPolicyRules: 値には次のいずれかの文字列値を指定する必要があります。

    - Success and Failure

    - 成功

  - baselineSecurityPolicyRules: 値には次のいずれかの文字列値を指定する必要があります。

    - "No one"

    - "Administrators, Backup Operators" など、許可されているユーザー グループの一覧

-   state: "Disabled" または "Enabled" オプションを含むことのできる文字列。 このプライベート プレビュー リリースでは、文字列の大文字と小文字が区別されます。

これらのフィールドのみ構成できます。 ファイルの形式またはサイズに違反した場合は、変更を保存できません。 次のエラー メッセージは、ファイルを処理できない場合に表示されます。

![](media/security-center-customize-os-security-config/invalid-json.png)

発生する可能性のあるエラーの一覧については、「[エラーコード](#error-codes)」をご覧ください。

これらの規則と変更できる属性 (太字) の例を次に示します。

**Rules セクション:** baselineRegistryRules
```
{

    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "**expectedValue**": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "**state**": "Disabled"

}
```

**Rules セクション:** baselineAuditPolicyRules
```
{
"auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
"ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
"originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
"cceId": "CCE-11001-5",
"ruleName": "Audit Policy: Account Management: Other Account Management Events",
"ruleType": "AuditPolicy",
"**expectedValue**": "Success and Failure",
"severity": "Critical",
"analyzeOperation": "Equals",
"source": "Microsoft",
"**state**": "Enabled"
},
```

**Rules セクション:** baselineSecurityPolicyRules
```
{
"sectionName": "Privilege Rights",
"settingName": "SeIncreaseWorkingSetPrivilege",
"ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
"originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
"cceId": "CCE-10548-6",
"ruleName": "Increase a process working set",
"ruleType": "SecurityPolicy",
"**expectedValue**": "Administrators, Local Service",
"severity": "Warning",
"analyzeOperation": "Equals",
"source": "Microsoft", "**state**": "Enabled"
},
```

異なる OS の種類に対して重複している規則がいくつかあります。 重複する規則には、同じ 'originalId' があります。

## <a name="adding-a-new-custom-rule"></a>新しいカスタム規則の追加

新しい規則を作成することもできます。 新しい規則を作成する前に、次の制限にご注意ください。

-   スキーマのバージョン、*baselineId* と *baselineName* は変更できません。

-   規則セットを削除することはできません。

-   規則セットを追加することはできません。

-   許可されている規則の最大数 (既定の規則を含む) は 1000 規則です。

新しいカスタム規則には、新しいカスタム ソースのマーク (! ="Microsoft") が付けられます。 *ruleId* フィールドは null または空にすることができます。 空の場合は、Microsoft によって生成されます。 空でない場合は、すべての規則 (既定とカスタム) 間で一意の有効な GUID が必要です。 コア フィールドに関する以下の制約をご確認ください。

-   *originalId* - null または空にすることができます。 *originalId* が空でない場合は、有効な GUID である必要があります。

-   *cceId* - null または空にすることができます。 *cceId* が空でない場合は、一意である必要があります。

-   *ruleType* - Registry、AuditPolicy、SecurityPolicy のいずれかです。

-   *Severity* - Unknown、Critical、Warning、Informational のいずれかです。

-   *analyzeOperation* - Equals を指定する必要があります。

-   *auditPolicyId* - 有効な GUID を指定する必要があります。

-   *regValueType* - Int、Long、String、MultipleString のいずれかを指定する必要があります。

> [!NOTE]
> Hive には *LocalMachine* を指定する必要があります。
>
>

新しいカスタム規則の例:

**レジストリ**:
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule”, "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
   }
```
**セキュリティ ポリシー**:
```
{

   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**監査ポリシー:**
```
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>ファイル アップロード エラー

値または書式設定のエラーが原因で、送信された構成ファイルが無効な場合は、エラーが表示されます。 csv 形式の詳しいエラー レポートをダウンロードし、エラーを修復および修正してから、修正した構成ファイルを再送信できます。

![](media/security-center-customize-os-security-config/invalid-configuration.png)

エラー ファイルの例:

![](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>エラー コード

以下の一覧には、発生する可能性のあるすべてのエラーが含まれます。

| **エラー**                                | **説明**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | 無効または空のプロパティ 'schemaVersion' が見つかりました。 この値は '{0}' に設定する必要があります。                                                         |
| BaselineInvalidStringError               | プロパティ '{0}' に値 '\\n' を含めることはできません。                                                                                                         |
| BaselineNullRuleError                    | ベースライン構成規則の一覧に、'null' 値を持つ規則が含まれています。                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID '{0}' が一意ではありません。                                                                                                                  |
| BaselineRuleEmptyProperty                | プロパティ: '{0}' がないか、無効です。                                                                                                       |
| BaselineRuleIdNotInDefault               | 規則にはソース プロパティ 'Microsoft' がありますが、Microsoft の既定の規則セットには見つかりませんでした。                                                   |
| BaselineRuleIdNotUniqueError             | 規則 ID が一意ではありません。                                                                                                                       |
| BaselineRuleInvalidGuid                  | 無効なプロパティ '{0}' が見つかりました。 値は有効な GUID ではありません。                                                                             |
| BaselineRuleInvalidHive                  | Hive には LocalMachine を指定する必要があります。                                                                                                                   |
| BaselineRuleNameNotUniqueError           | 規則名が一意ではありません。                                                                                                                 |
| BaselineRuleNotExistInConfigError        | 規則が新しい構成に見つかりませんでした。 規則を削除できません。                                                                     |
| BaselineRuleNotFoundError                | 規則が既定の基準規則セットに見つかりませんでした。                                                                                        |
| BaselineRuleNotInPlace                   | 規則は、種類 {0} の既定の規則と一致し、{1} の一覧に表示されています。                                                                       |
| BaselineRulePropertyTooLong              | プロパティ: '{0}' が長すぎます。 許容される最大長は {1} です。                                                                                        |
| BaselineRuleRegTypeInvalidError          | 予期される値 '{0}' は、定義されているレジストリ値の種類と一致しません。                                                              |
| BaselineRulesetAdded                     | Id '{0}' の規則セットが既定の構成に見つかりませんでした。 規則セットを追加できません。                                               |
| BaselineRulesetIdMustBeUnique            | 指定した基準規則セット '{0}' は一意である必要があります。                                                                                           |
| BaselineRulesetNotFound                  | Id '{0}' と名前 '{1}' の規則セットが指定された構成に見つかりませんでした。 規則セットを削除できません。                                |
| BaselineRuleSourceNotMatch               | Id '{0}' の規則は既に定義されています。                                                                                                       |
| BaselineRuleTypeDoesntMatch              | 既定の規則の種類は '{0}' です。                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | 規則の実際の種類は {0} ですが、ruleType プロパティは {1} です。                                                                          |
| BaselineRuleUnpermittedChangesError      | 変更できるのは 'expectedValue' および 'state' プロパティだけです。                                                                       |
| BaselineTooManyRules                     | カスタマイズされた規則の許容される最大数は {0} 規則です。 指定された構成には {1} 規則が含まれています。 ({2} の既定の規則 + {3} のカスタマイズされた規則。 |
| ErrorNoConfigurationStatus               | 構成状態が見つかりませんでした。 目的の構成状態 ('Default' または 'Custom') を指定してください。                                    |
| ErrorNonEmptyRulesetOnDefault            | 構成状態が既定値に設定されています。 BaselineRulesets の一覧は null または空にする必要があります。                                                          |
| ErrorNullRulesetsPropertyOnCustom        | 指定された構成状態は 'Custom' ですが、'baselineRulesets' プロパティは null または空です。                                             |
| ErrorParsingBaselineConfig               | 指定された構成が無効です。 定義されている 1 つ以上の値が null 値または無効な種類です。                                  |
| ErrorParsingIsDefaultProperty            | 指定された 'configurationStatus' 値 '{0}' が無効です。 値には、'Default' または 'Custom' のみを指定できます。                                         |
| InCompatibleViewVersion                  | バージョンの表示: {0} はこのワークスペースの種類でサポートされていません。                                                                                   |
| InvalidBaselineConfigurationGeneralError | 指定されたベースライン構成に、1 つ以上の種類の検証エラーが見つかりました。                                                          |
| ViewConversionError                      | ビューは、そのワークスペースでサポートされているバージョンよりも前のバージョンです。 ビューの変換に失敗しました: {0}                                                                 |

十分なアクセス許可がない場合は、一般エラーが発生する可能性があります。

![](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>次の手順
この記事では、Security Center で OS セキュリティ構成の評価をカスタマイズする方法について説明しました。 構成規則と修復について詳しくは、以下をご覧ください。

- [Security Center の一般的な構成識別子と基準規則](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。
- Security Center では、CCE (Common Configuration Enumeration) を使用して構成規則に一意の識別子を割り当てます。 詳細については、 [CCE](https://nvd.nist.gov/config/cce/index) サイトをご覧ください。
- [セキュリティ構成の修復](security-center-remediate-os-vulnerabilities.md)に関する記事では、OS 構成が推奨されるセキュリティ構成規則に適合していない場合に脆弱性を解決する方法を示しています。
