---
title: dsregcmd コマンドを使用したデバイスのトラブルシューティング - Azure Active Directory
description: この記事では、Azure AD 内のデバイスの状態を把握するために dsregcmd コマンドからの出力を使用する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: ade33d37584595bcc5c4e9ce1d1fda6edadd67a4
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158069"
---
# <a name="troubleshoot-devices-by-using-the-dsregcmd-command"></a>dsregcmd コマンドを使用したデバイスのトラブルシューティング

この記事では、Azure Active Directory (Azure AD) 内のデバイスの状態を把握するために `dsregcmd` コマンドからの出力を使用する方法について説明します。 `dsregcmd /status` ユーティリティは、ドメイン ユーザー アカウントとして実行する必要があります。

## <a name="device-state"></a>デバイスの状態

このセクションには、デバイスの参加状態のパラメーターが一覧に示されます。 デバイスがさまざまな参加状態になるために必要な条件を次の表に一覧表示します。

| AzureAdJoined | EnterpriseJoined | DomainJoined | デバイスの状態 |
| ---   | ---   | ---   | ---   |
| YES | NO | NO | Azure AD 参加済み |
| NO | NO | YES | ドメイン参加済み |
| YES | NO | YES | ハイブリッド AD 参加済み |
| NO | YES | YES | オンプレミス DRS 参加済み |
| | |

> [!NOTE]
> Workplace Joined (Azure AD 登録済み) 状態は、[User State](#user-state) セクションに表示されます。

- **AzureAdJoined**: デバイスが Azure AD に参加している場合は、状態が *YES* に設定されます。 それ以外の場合は、状態が  *NO* に設定されます。
- **EnterpriseJoined**: デバイスがオンプレミスのデータ レプリケーション サービス (DRS) に参加している場合は、状態が *YES* に設定されます。 デバイスに EnterpriseJoined と AzureAdJoined の両方が指定されることはありません。
- **DomainJoined**: デバイスがドメイン (Active Directory) に参加している場合は、状態が *YES* に設定されます。
- **DomainName:** : デバイスがドメインに参加している場合は、状態がドメインの名前に設定されます。

### <a name="sample-device-state-output"></a>デバイスの状態の出力例

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>[デバイスの詳細]

状態は、デバイスが Azure AD 参加済みまたはハイブリッド Azure AD 参加済み (Azure AD に未登録) の場合にのみ表示されます。 このセクションには、Azure AD に格納されているデバイスを識別する詳細の一覧が示されます。

- **DeviceId**: Azure AD テナント内のデバイスの一意の ID。
- **Thumbprint**: デバイス証明書の拇印。
- **DeviceCertificateValidity**: デバイス証明書の有効性の状態。
- **KeyContainerId**: デバイス証明書に関連付けられているデバイスの秘密キーの ContainerId。
- **Keyprovider**: デバイスの秘密キーを格納するために使用される KeyProvider (ハードウェア/ソフトウェア)。
- **TpmProtected**: デバイスの秘密キーがハードウェア トラステッド プラットフォーム モジュール (TPM) に格納されている場合は、状態が *YES* に設定されます。
- **DeviceAuthStatus**: Azure AD でデバイスの正常性を確認するためのチェックを実行します。 正常性状態は次のとおりです。  
  * *SUCCESS* Azure AD にデバイスが存在し、有効になっている場合。  
  * *FAILED. Device is either disabled or deleted* デバイスが無効になっているか削除されている場合。 この問題の詳細については、[ Azure Active Directory デバイス管理の FAQ](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-11-devices)に関するページを参照してください。 
  * *FAILED. ERROR* テストを実行できなかった場合。 このテストには、Azure AD へのネットワーク接続が必要です。
    > [!NOTE]
    > **DeviceAuthStatus** フィールドは Windows 10 May 2021 Update (バージョン 21H1) で追加されました。  

### <a name="sample-device-details-output"></a>デバイスの詳細の出力例

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
          DeviceAuthStatus : SUCCESS
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Tenant details (テナントの詳細)

テナントの詳細は、デバイスが Azure AD 参加済みまたはハイブリッド Azure AD 参加済み (Azure AD に未登録) の場合にのみ表示されます。 このセクションには、デバイスが Azure AD に参加している場合に表示される一般的なテナントの詳細が示されます。

> [!NOTE]
> このセクションのモバイル デバイス管理 (MDM) URL フィールドが空である場合は、MDM が構成されなかったか、または現在のユーザーが MDM 登録の範囲内にないことを示します。 Azure AD のモビリティ設定をチェックして MDM の構成を確認してください。

> [!NOTE]
> MDM の URL が表示されている場合でも、デバイスが MDM によって管理されているわけではありません。 この情報は、デバイス自体が管理されていない場合でも、テナントに自動登録のための MDM 構成がある場合に表示されます。

### <a name="sample-tenant-details-output"></a>テナントの詳細の出力例

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>ユーザー状態

このセクションには、現在デバイスにログインしているユーザーのさまざまな属性の状態が示されます。

> [!NOTE]
> 有効な状態を取得するには、コマンドをユーザー コンテキストで実行する必要があります。

- **NgcSet**: 現在ログインしているユーザーに対して Windows Hello キーが設定されている場合は、状態が *YES* に設定されます。
- **NgcKeyId**: Windows Hello キーの ID (現在ログインしているユーザーに対して設定されている場合)。
- **CanReset**: ユーザーが Windows Hello キーをリセットできるかどうかを示します。
- **使用可能な値**: DestructiveOnly、NonDestructiveOnly、DestructiveAndNonDestructive、またはエラーの場合は Unknown。
- **WorkplaceJoined** Azure AD の登録済みアカウントが現在の NTUSER コンテキストでデバイスに追加されている場合は、状態が *YES* に設定されます。
- **WamDefaultSet**: ログインしているユーザーに対して Web アカウント マネージャー (WAM) の既定の WebAccount が作成されている場合は、状態が *YES* に設定されます。 このフィールドには、`dsregcmd /status` が管理者特権でのコマンド プロンプトから実行されている場合にエラーが表示されることがあります。
- **WamDefaultAuthority**: Azure AD の場合は、状態が *organizations* に設定されます。
- **WamDefaultId**: Azure AD の場合は常に *https://login.microsoft.com* が使用されます。
- **WamDefaultGUID**: 既定の WAM WebAccount の WAM プロバイダーの (Azure AD/Microsoft アカウント) GUID。

### <a name="sample-user-state-output"></a>ユーザーの状態の出力例

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO state (SSO 状態)

Azure AD 登録済みデバイスについては、このセクションを無視してかまいません。

> [!NOTE]
> ユーザーの有効な状態を取得するには、コマンドをユーザー コンテキストで実行する必要があります。

- **AzureAdPrt**: ログイン ユーザーのデバイスにプライマリ更新トークン (PRT) が存在する場合は、状態が *YES* に設定されます。
- **AzureAdPrtUpdateTime**: PRT が最後に更新されたときの協定世界時 (UTC) での時刻に状態が設定されます。
- **AzureAdPrtExpiryTime**: PRT が更新されない場合に、それらが期限切れになる UTC での時刻に状態が設定されます。
- **AzureAdPrtAuthority**: Azure AD 機関の URL
- **EnterprisePrt**: オンプレミスの Active Directory フェデレーション サービス (AD FS) からの PRT がデバイスにある場合は、状態が *YES* に設定されます。 ハイブリッド Azure AD 参加済みデバイスの場合、デバイスには Azure AD とオンプレミスの Active Directory からの PRT を同時に指定できます。 オンプレミスの参加済みデバイスでは、エンタープライズ PRT のみ指定されます。
- **EnterprisePrtUpdateTime**: エンタープライズ PRT が最後に更新されたときの UTC での時刻に状態が設定されます。
- **EnterprisePrtExpiryTime**: PRT が更新されない場合に、それが期限切れになる UTC での時刻に状態が設定されます。
- **EnterprisePrtAuthority**: ADFS 機関 URL

>[!NOTE]
> Windows 10 May 2021 Update (バージョン 21H1) で、次の PRT 診断フィールドが追加されました。

>[!NOTE]
> * **AzureAdPrt** フィールドに表示される診断情報は AzureAD PRT の取得と更新用であり、**EnterprisePrt** フィールドに表示される診断情報は Enterprise PRT の取得と更新用です。
> * 診断情報は、最後に成功した PRT 更新時間 (AzureAdPrtUpdateTime/EnterprisePrtUpdateTime) の後に取得または更新エラーが発生した場合にのみ表示されます。  
>共有デバイスでは、この診断情報は別のユーザーのログイン試行からのものである可能性があります。

- **AcquirePrtDiagnostics**: ログに取得された PRT 診断情報が含まれている場合は、状態が *PRESENT* に設定されます。  
   診断情報が使用できない場合、このフィールドはスキップされます。
- **Previous Prt Attempt**: 失敗した PRT の試行が発生したローカル時刻 (UTC 形式)。  
- **Attempt Status**: 返されたクライアント エラー コード (HRESULT)。
- **User Identity**: PRT の試行が発生したユーザーの UPN。
- **Credential Type**: PRT を取得または更新するために使用される資格情報。 一般的な資格情報の種類は、パスワードと Next Generation Credential (NGC) (Windows Hello の場合) です。
- **Correlation ID**: 失敗した PRT の試行に対してサーバーから送信された関連付け ID。
- **Endpoint URI**: 障害が発生する前に最後にアクセスしたエンドポイント。
- **HTTP Method**: エンドポイントへのアクセスに使用される HTTP メソッド。
- **HTTP Error**: WinHttp のトランスポート エラー コード。 追加の[ネットワーク エラー コード](/windows/win32/winhttp/error-messages)を取得します。
- **HTTP Status**: エンドポイントから返された HTTP の状態。
- **Server Error Code**: サーバーからのエラー コード。  
- **Server Error Description**: サーバーからのエラー メッセージ。
- **RefreshPrtDiagnostics**: ログに取得された PRT 診断情報が含まれている場合は、状態が *PRESENT* に設定されます。  
診断情報が使用できない場合、このフィールドはスキップされます。
診断情報のフィールドは **AcquirePrtDiagnostics** と同じです


### <a name="sample-sso-state-output"></a>SSO 状態の出力例

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : NO
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
     AcquirePrtDiagnostics : PRESENT
      Previous Prt Attempt : 2020-07-18 20:10:33.789 UTC
            Attempt Status : 0xc000006d
             User Identity : john@contoso.com
           Credential Type : Password
            Correlation ID : 63648321-fc5c-46eb-996e-ed1f3ba7740f
              Endpoint URI : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token/
               HTTP Method : POST
                HTTP Error : 0x0
               HTTP status : 400
         Server Error Code : invalid_grant
  Server Error Description : AADSTS50126: Error validating credentials due to invalid username or password.
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostics-data"></a>[診断データ]

### <a name="pre-join-diagnostics"></a>参加前の診断

この診断セクションは、デバイスがドメイン参加済みで、ハイブリッド Azure AD 参加ができない場合にのみ表示されます。

このセクションでは、参加エラーの診断に役立つさまざまなテストを実行します。 この情報には、エラー フェーズ、エラー コード、サーバー要求 ID、サーバー応答の http 状態、サーバー応答のエラー メッセージが含まれます。

- **User Context**: 診断が実行されるコンテキスト。 指定できる値SYSTEM、UN-ELEVATED User、ELEVATED User。

   > [!NOTE]
   > 実際の参加は SYSTEM コンテキストで実行されるため、SYSTEM コンテキストでの診断の実行が、実際の参加シナリオに最も近いものになります。 SYSTEM コンテキストで診断を実行するには、管理者特権でのコマンド プロンプトから `dsregcmd /status` コマンドを実行する必要があります。

- **Client Time**: システムの UTC での時刻。
- **AD Connectivity Test**: このテストは、ドメイン コントローラーへの接続テストを実行します。 このテストでエラーが発生すると、事前チェック フェーズで参加エラーが発生する可能性があります。
- **AD Configuration Test**: このテストでは、オンプレミスの Active Directory フォレストで、Service Connection Point (SCP) オブジェクトが適切に構成されているかどうかを読み取って確認します。 このテストでエラーが発生すると、検出フェーズでエラー コード 0x801c001d で参加エラーが発生する可能性があります。
- **DRS Discovery Test**: このテストでは、検出メタデータ エンドポイントから DRS エンドポイントを取得し、ユーザー領域要求を実行します。 このテストでエラーが発生すると、検出フェーズで参加エラーが発生する可能性があります。
- **DRS Connectivity Test**: このテストでは、DRS エンドポイントへの基本接続テストを実行します。
- **Token acquisition Test**: ユーザー テナントがフェデレーションされている場合、このテストでは Azure AD 認証トークンの取得が試行されます。 このテストでエラーが発生すると、認証フェーズで参加エラーが発生する可能性があります。 認証に失敗した場合、次のレジストリ キー設定でフォールバックが明示的に無効になっていない限り、フォールバックとして同期参加が試行されます。

  ```
  Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
  Value: FallbackToSyncJoin
  Type:  REG_DWORD
  Value: 0x0 -> Disabled
  Value: 0x1 -> Enabled
  Default (No Key): Enabled
  ```

- **Fallback to Sync-Join**: 前のレジストリ キー (認証失敗で同期参加にフォールバックすることを避けるためのもの) が存在 *しない* 場合、状態が *Enabled* に設定されます。 このオプションは、Windows 10 1803 以降で使用できます。
- **Previous Registration:** : 前回の参加試行が発生した時刻。 失敗した参加試行だけがログに記録されます。
- **Error Phase**: 中止された参加のステージ。 指定できる値は、*pre-check*、*discover*、*auth*、*join* です。
- **Client ErrorCode**: 返されたクライアント エラー コード (HRESULT)。
- **Server ErrorCode**: サーバーに要求が送信され、サーバーがエラー コードで応答した場合に表示されるサーバー エラー コード。
- **Server Message**: エラー コードと共に返されたサーバー メッセージ。
- **Https Status**: サーバーから返された HTTP の状態。
- **Request ID**: サーバーに送信されるクライアントの requestId。 要求 ID はサーバー側のログと関連付けるのに役立ちます。

### <a name="sample-pre-join-diagnostics-output"></a>参加前の診断の出力例

次の例は、検出エラーで失敗した診断テストを示しています。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                       |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

次の例では、診断テストは成功していますが、同期参加に予期される登録の試行がディレクトリ エラーで失敗しています。 Azure AD Connect の同期ジョブの完了後、デバイスを参加させることができます。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                       |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) isn't found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>参加後の診断

この診断セクションには、クラウドに参加しているデバイスで実行されたサニティ チェックの出力が表示されます。

- **AadRecoveryEnabled**: 値が *YES* の場合、デバイスに格納されているキーは使用できず、デバイスは復旧対象としてマークされます。 次回のサインインでは復旧フローがトリガーされ、デバイスが再登録されます。
- **KeySignTest:** : 値が *PASSED* の場合、デバイス キーは正常な状態です。 KeySignTest が失敗した場合、デバイスは通常、復旧対象としてマークされます。 次回のサインインでは復旧フローがトリガーされ、デバイスが再登録されます。 ハイブリッド Azure AD 参加済みデバイスの場合、復旧はサイレントです。 デバイスが Azure AD 参加済みまたは Azure AD 登録済みの場合、それらによって必要に応じてデバイスの復旧と再登録のためにユーザー認証が要求されます。 
   > [!NOTE]
   > KeySignTest には、昇格された特権が必要です。

#### <a name="sample-post-join-diagnostics-output"></a>参加後の診断の出力例

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisites-check"></a>NGC の前提条件チェック

この診断セクションでは、Windows Hello for Business (WHFB) を設定するための前提条件チェックを実行します。

> [!NOTE]
> ユーザーが既に WHFB を適切に構成している場合は、`dsregcmd /status` で NGC の前提条件チェックの詳細が表示されないことがあります。

- **IsDeviceJoined**: デバイスが Azure AD に参加している場合は、状態が *YES* に設定されます。
- **IsUserAzureAD**: ログインしているユーザーが Azure AD に存在する場合は、状態が *YES* に設定されます。
- **PolicyEnabled**: デバイスで WHFB ポリシーが有効になっている場合は、状態が *YES* に設定されます。
- **PostLogonEnabled**: WHFB 登録がプラットフォームによってネイティブでトリガーされる場合は、状態が *YES* に設定されます。 状態が *NO* に設定されている場合は、Windows Hello for Business の登録がカスタム メカニズムによってトリガーされることを示します。
- **DeviceEligible**: デバイスが WHFB に登録するためのハードウェア要件を満たしている場合は、状態が *YES* に設定されます。
- **SessionIsNotRemote**: 現在のユーザーがリモートではなくデバイスに直接ログインしている場合は、状態が *YES* に設定されます。
- **CertEnrollment**: この設定は、WHFB 証明書信頼のデプロイに固有であり、WHFB の証明書登録機関を示します。 WHFB ポリシーのソースがグループ ポリシーの場合は、状態が *enrollment authority* に設定され、ソースが MDM の場合は *mobile device management* に設定されます。 どちらのソースも適用しない場合は、状態が *none* に設定されます。
- **AdfsRefreshToken**: この設定は WHFB 証明書信頼のデプロイに固有であり、CertEnrollment の状態が *enrollment authority* である場合にのみ存在します。 この設定は、ユーザー用のエンタープライズ PRT がデバイスにあるかどうかを示します。
- **AdfsRaIsReady**: この設定は WHFB 証明書信頼のデプロイに固有であり、CertEnrollment の状態が *enrollment authority* である場合にのみ存在します。 ADFS で、探索メタデータに WHFB がサポートされていることが示され、*かつ* ログオン証明書テンプレートを利用できる場合は、状態が *YES* に設定されます。
- **LogonCertTemplateReady**: この設定は WHFB 証明書信頼のデプロイに固有であり、CertEnrollment の状態が *enrollment authority* である場合にのみ存在します。 ログイン証明書テンプレートの状態が有効であり、ADFS 登録機関 (RA) のトラブルシューティングに役立つ場合は、状態が *YES* に設定されます。
- **PreReqResult**: すべての WHFB の前提条件評価の結果が提示されます。 ユーザーが次回サインインするときに、ログイン後のタスクとして WHFB 登録が起動される場合は、状態が *Will Provision* に設定されます。

### <a name="sample-ngc-prerequisites-check-output"></a>NGC の前提条件チェックの出力例

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>次のステップ

[Microsoft エラー ルックアップ ツール](/windows/win32/debug/system-error-code-lookup-tool)にアクセスします。
