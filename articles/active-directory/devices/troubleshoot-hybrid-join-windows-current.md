---
title: ハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング
description: この記事は、ハイブリッド Azure Active Directory に参加している Windows&nbsp;10 および Windows Server 2016 デバイスのトラブルシューティングに役立ちます。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: d2abaed55177a0e7acae22e3045ba68f56a5494e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718187"
---
# <a name="troubleshoot-hybrid-azure-ad-joined-devices"></a>ハイブリッド Azure AD 参加済みデバイスのトラブルシューティング

この記事では、Windows&nbsp;10 または Windows Server 2016 を実行しているデバイスの潜在的な問題を解決するのに役立つトラブルシューティング ガイダンスを提供します。

ハイブリッド Azure Active Directory (Azure AD) への参加では、Windows&nbsp;10 の 2015 年 11 月以降の更新プログラムがサポートされます。

他の Windows クライアントのトラブルシューティングについては、「[ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)」を参照してください。

この記事では、次のシナリオをサポートするように[構成されたハイブリッド Azure AD 参加済みデバイス](hybrid-azuread-join-plan.md)があることを前提としています。

- デバイスベースの条件付きアクセス
- [Enterprise State Roaming](./enterprise-state-roaming-overview.md)
- [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification)


> [!NOTE] 
> デバイス登録に関するよくある問題のトラブルシューティングを行うには、[デバイス登録のトラブルシューティング ツール](/samples/azure-samples/dsregtool/dsregtool/)を使用してください。


## <a name="troubleshoot-join-failures"></a>参加エラーのトラブルシューティング

### <a name="step-1-retrieve-the-join-status"></a>手順 1:参加状態を取得する

1. 管理者としてコマンド プロンプト ウィンドウを開きます。
1. 「`dsregcmd /status`.

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>手順 2:参加状態を評価する

次の表のフィールドを確認し、必要な値が設定されていることを確認します。

| フィールド | 必要な値 | 説明 |
| --- | --- | --- |
| DomainJoined | YES | このフィールドは、デバイスが Windows Server Active Directory に参加しているかどうかを示します。 <br><br>この値が *NO* である場合、デバイスはハイブリッド Azure AD 参加を実行できません。 |
| WorkplaceJoined | NO | このフィールドは、デバイスが ("*ワークプレースに参加済み*" としてマークされた) 個人所有のデバイスとして Azure AD に登録されているかどうかを示します。 ドメインに参加しており、かつハイブリッド Azure AD に参加しているコンピューターの場合、この値は *NO* にする必要があります。 <br><br>この値が *YES* の場合、ハイブリッド Azure AD 参加が完了する前に、職場または学校アカウントが追加されています。 この場合に Windows&nbsp;10 バージョン 1607 以降を使用していると、アカウントは無視されます。 |
| AzureAdJoined | YES | このフィールドは、デバイスが参加しているかどうかを示します。 デバイスが Azure AD 参加済みデバイスまたはハイブリッド Azure AD 参加済みデバイスのいずれかである場合、この値は *YES* になります。 <br><br>この値が *NO* の場合、Azure AD への参加はまだ完了していません。 |
|  |  |

詳細なトラブルシューティングを行うには、次の手順に進みます。

### <a name="step-3-find-the-phase-in-which-the-join-failed-and-the-error-code"></a>手順 3: 参加がエラーになったフェーズとエラー コードを見つける

**Windows&nbsp;10 バージョン 1803 以降の場合**

参加状態の出力の "Diagnostic Data" セクションで "Previous Registration" サブセクションを探します。 このセクションは、デバイスがドメイン参加済みで、ハイブリッド Azure AD 参加できない場合にのみ表示されます。

"Error Phase" フィールドは参加エラーのフェーズを示し、"Client ErrorCode" は参加操作のエラー コードを示します。

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) isn't found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

**Windows&nbsp;10 の古いバージョンの場合**

イベント ビューアーのログで、参加エラーのフェーズとエラー コードを確認します。

1. イベント ビューアーで **ユーザー デバイス登録** イベント ログを開きます。 これらは、 **[アプリケーションとサービス ログ]**  >  **[Microsoft]**  >  **[Windows]**  >  **[ユーザー デバイス登録]** の下に格納されています。
1. イベント ID が 304、305、307 のイベントを探します。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="イベント ID 304 が選択されており、その情報が表示され、エラー コードとフェーズが強調表示されているイベント ビューアーのスクリーンショット。" border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="イベント ID 305 が選択されており、その情報が表示され、エラー コードが強調表示されているイベント ビューアーのスクリーンショット。" border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions"></a>手順 4: 考えられる原因と解決策を確認する

#### <a name="pre-check-phase"></a>事前チェック フェーズ

考えられるエラーの原因:

- デバイスがドメイン コントローラーを認識していない。
   - デバイスは、組織の内部ネットワークに配置するか、オンプレミスの Active Directory ドメイン コントローラーをネットワークで認識する仮想プライベート ネットワークに配置する必要があります。

#### <a name="discover-phase"></a>検出フェーズ

考えられるエラーの原因:

-  サービス接続ポイント オブジェクトが正しく構成されていないか、ドメイン コントローラーから読み取ることができない。
   - デバイスが属している AD フォレストに、Azure AD 内の検証済みドメイン名を指す有効なサービス接続ポイント オブジェクトが必要です。
   - 詳細については、「[チュートリアル: フェデレーション ドメイン用のハイブリッド Azure Active Directory 参加の構成](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)」のサービス接続ポイントの構成に関するセクションを参照してください。
- 検出エンドポイントから検出メタデータに接続してフェッチできない。
   - 登録と承認のエンドポイントを検出するには、デバイスがシステム コンテキストで `https://enterpriseregistration.windows.net` にアクセスできる必要があります。
   - オンプレミス環境に送信プロキシが必要である場合は、デバイスのコンピューター アカウントが送信プロキシに対する検出とサイレント認証を実行できることを IT 管理者が確認する必要があります。
- ユーザー領域エンドポイントに接続して領域の検出を実行できない (Windows&nbsp;10 バージョン 1809 以降のみ)。
   - 検証済みドメインの領域検出を実行してドメインの種類 (マネージドまたはフェデレーション) を判別するには、デバイスがシステム コンテキストで `https://login.microsoftonline.com` にアクセスできる必要があります。
   - オンプレミス環境に送信プロキシが必要である場合、IT 管理者は、デバイスのシステム コンテキストで送信プロキシに対する検出とサイレント認証を確実に実行できるようにする必要があります。

**一般的なエラー コード:**

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611) | サービス接続ポイント (SCP) オブジェクトを読み取ることができず、Azure AD テナント情報を取得できません。 | [サービス接続ポイントの構成](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)に関するセクションを参照してください。 |
| **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607) | 一般的な検出エラー。 データ レプリケーション サービス (DRS) から検出メタデータを取得できませんでした。 | さらに調査するには、次のセクションでサブエラーを見つけます。 |
| **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609) | 検出の実行中に操作がタイムアウトになりました。 | システム コンテキストで `https://enterpriseregistration.windows.net` に確実にアクセスできるようにします。 詳細については、[ネットワーク接続の要件](hybrid-azuread-join-managed-domains.md#prerequisites)に関するセクションを参照してください。 |
| **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c003d/-2145648579) | 一般的な領域検出エラー。 STS からドメインの種類 (管理対象/フェデレーション) を判別できませんでした。 | さらに調査するには、次のセクションでサブエラーを見つけます。 |
| | |

**一般的なサブエラー コード:**

検出エラー コードのサブエラー コードを見つけるには、次のいずれかの方法を使用します。

##### <a name="windowsnbsp10-version-1803-or-later"></a>Windows&nbsp;10 バージョン 1803 以降

参加状態の出力の "Diagnostic Data" セクションで "DRS Discovery Test" を探します。 このセクションは、デバイスがドメイン参加済みで、ハイブリッド Azure AD 参加できない場合にのみ表示されます。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="earlier-windowsnbsp10-versions"></a>Windows&nbsp;10 の古いバージョン

イベント ビューアーのログを使用して、参加エラーのフェーズとエラー コードを探します。

1. イベント ビューアーで **ユーザー デバイス登録** イベント ログを開きます。 これらは、 **[アプリケーションとサービス ログ]**  >  **[Microsoft]**  >  **[Windows]**  >  **[ユーザー デバイス登録]** の下に格納されています。
1. イベント ID 201 を探します。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="イベント ID 201 が選択されており、その情報が表示され、エラー コードが強調表示されているイベント ビューアーのスクリーンショット。" border="false":::

**ネットワーク エラー**:

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867) | サーバーとの接続を確立できませんでした。 | 必要な Microsoft リソースとのネットワーク接続を確認します。 詳細については、[ネットワーク接続の要件](hybrid-azuread-join-managed-domains.md#prerequisites)に関するページを参照してください。 |
| **WININET_E_TIMEOUT** (0x80072ee2/-2147012894) | 一般的なネットワーク タイムアウト。 | 必要な Microsoft リソースとのネットワーク接続を確認します。 詳細については、[ネットワーク接続の要件](hybrid-azuread-join-managed-domains.md#prerequisites)に関するページを参照してください。 |
| **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721) | ネットワーク スタックがサーバーからの応答をデコードできませんでした。 | ネットワーク プロキシがサーバーの応答を妨害したり変更したりしていないようにします。 |
| | |


**HTTP エラー**:

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582) | サービス接続ポイント オブジェクトが不正なテナント ID で構成されているか、テナント内でアクティブなサブスクリプションが見つかりませんでした。 | サービス接続ポイント オブジェクトが、確実に正しい Azure AD テナント ID とアクティブなサブスクリプションで構成されているか、またはサービスがテナント内に存在しているようにします。 |
| **DSREG_SERVER_BUSY** (0x801c0025/-2145648603) | DRS サーバーからの HTTP 503。 | サーバーは現在使用できません。 サーバーがオンラインに戻ると、以降の参加の試行は成功する可能性があります。 |
| | |


**その他のエラー**:

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **E_INVALIDDATA** (0x8007000d/-2147024883) | サーバー応答 JSON を解析できませんでした。HTML 認証ページでプロキシが HTTP 200 を返していることが原因と考えられます。 | オンプレミス環境に送信プロキシが必要である場合、IT 管理者は、デバイスのシステム コンテキストで送信プロキシに対する検出とサイレント認証を確実に実行できるようにする必要があります。 |
| | |


#### <a name="authentication-phase"></a>認証フェーズ

この内容は、フェデレーション ドメイン アカウントにのみ適用されます。

エラーの原因:

- DRS リソースのアクセス トークンをサイレントに取得できません。
   - Windows&nbsp;10 デバイスでは、アクティブな WS-Trust エンドポイントに対する統合 Windows 認証を使用して、フェデレーション サービスから認証トークンを取得します。 詳細については、[フェデレーション サービスの構成](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)に関する記事をご覧ください。

**一般的なエラー コード**:

イベント ビューアーのログで、エラー コード、サブエラー コード、サーバー エラー コード、サーバー エラー メッセージを見つけます。

1. イベント ビューアーで **ユーザー デバイス登録** イベント ログを開きます。 これらは、 **[アプリケーションとサービス ログ]**  >  **[Microsoft]**  >  **[Windows]**  >  **[ユーザー デバイス登録]** の下に格納されています。
1. イベント ID 305 を探します。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="イベント ID 305 が選択されており、その情報が表示され、ADAL エラー コードと状態が強調表示されているイベント ビューアーのスクリーンショット。" border="false":::

**構成エラー**:

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057) | Azure AD Authentication Library (ADAL) 認証プロトコルが WS-Trust ではありません。 | オンプレミスの ID プロバイダーが WS-Trust をサポートしている必要があります。 |
| **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036) | オンプレミスのフェデレーション サービスから XML 応答が返されませんでした。 | メタデータ交換 (MEX) エンドポイントから有効な XML が返されていることを確認します。 プロキシが妨害を行ったり XML 以外の応答を返したりしていないことを確認します。 |
| **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045) | ユーザー名/パスワード認証のエンドポイントを検出できませんでした。 | オンプレミスの ID プロバイダーの設定を確認します。 WS-Trust エンドポイントが有効になっており、MEX 応答にこれらの正しいエンドポイントが含まれていることを確認します。 |
| | |


**ネットワーク エラー**:

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614) | 一般的なネットワーク タイムアウト。 | システム コンテキストで `https://login.microsoftonline.com` にアクセスできるようにします。 システム コンテキストでオンプレミスの ID プロバイダーにアクセスできるようにします。 詳細については、[ネットワーク接続の要件](hybrid-azuread-join-managed-domains.md#prerequisites)に関するページを参照してください。 |
| **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586) | 承認エンドポイントとの接続が中止されました。 | しばらくしてからやり直すか、または別の安定したネットワークの場所から参加を試みます。 |
| **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441) | サーバーによって送信されたトランスポート層セキュリティ (TLS) 証明書 (以前は Secure Sockets Layer (SSL) 証明書と呼ばれていました)、を検証できませんでした。 | クライアント時間のずれを確認します。 しばらくしてからやり直すか、または別の安定したネットワークの場所から参加を試みます。 |
| **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587) | `https://login.microsoftonline.com` への接続に失敗しました。 | `https://login.microsoftonline.com`へのネットワーク接続を確認します。 |
| | |


**その他のエラー**:

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829) | オンプレミスの ID プロバイダーからの SAML トークンが Azure AD で受け付けられませんでした。 | フェデレーション サーバーの設定を確認します。 認証ログでサーバー エラー コードを探します。 |
| **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060) | サーバーの WS-Trust 応答でエラー例外が報告され、アサーションの取得に失敗しました。 | フェデレーション サーバーの設定を確認します。 認証ログでサーバー エラー コードを探します。 |
| **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074) | トークン エンドポイントからアクセス トークンを取得するときにエラーが発生しました。 | ADAL ログで根本的なエラーを探します。 |
| **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323) | 一般的な ADAL エラー。 | 認証ログで、サブエラー コードまたはサーバー エラー コードを探します。 |
| | |


#### <a name="join-phase"></a>参加フェーズ

エラーの原因:

お使いの Windows 10 のバージョンに応じて、次の表で登録の種類とエラー コードを探します。

#### <a name="windowsnbsp10-version-1803-or-later"></a>Windows&nbsp;10 バージョン 1803 以降

参加状態の出力の "Diagnostic Data" セクションで "Previous Registration" サブセクションを探します。 このセクションは、デバイスがドメイン参加済みで、ハイブリッド Azure AD 参加ができない場合にのみ表示されます。

"Registration Type" フィールドは、実行された参加の種類を示します。

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="earlier-windowsnbsp10-versions"></a>Windows&nbsp;10 の古いバージョン

イベント ビューアーのログで、参加エラーのフェーズとエラー コードを確認します。

1. イベント ビューアーで **ユーザー デバイス登録** イベント ログを開きます。 これらは、 **[アプリケーションとサービス ログ]**  >  **[Microsoft]**  >  **[Windows]**  >  **[ユーザー デバイス登録]** の下に格納されています。
1. イベント ID 204 を探します。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="イベント ID 204 が選択されており、そのエラー コード、HTTP の状態、メッセージが強調表示されているイベント ビューアーのスクリーンショット。" border="false":::

**DRS サーバーから返される HTTP エラー**:

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630) | DRS から、ErrorCode が "DirectoryError" のエラー応答を受け取りました。 | 考えられる原因と解決策については、サーバー エラー コードを参照してください。 |
| **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638) | DRS から、ErrorCode が "AuthenticationError" で、ErrorSubCode が "DeviceNotFound" "*以外*" のエラー応答を受け取りました。 | 考えられる原因と解決策については、サーバー エラー コードを参照してください。 |
| **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634) | DRS から、ErrorCode が "DirectoryError" のエラー応答を受け取りました。 | 考えられる原因と解決策については、サーバー エラー コードを参照してください。 |
| | |


**TPM エラー**:

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **NTE_BAD_KEYSET** (0x80090016/-2146893802) | トラステッド プラットフォーム モジュール (TPM) 操作が失敗したか、または無効でした。 | 不正な sysprep イメージが原因と考えられます。 sysprep イメージの作成元であるマシンが Azure AD に参加していない、ハイブリッド Azure AD に参加していない、または Azure AD に登録されていないことを確認します。 |
| **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641) | 一般的な TPM エラー。 | このエラーが発生したデバイスで TPM を無効にします。 Windows&nbsp;10 バージョン 1809 以降では、TPM エラーが自動的に検出され、TPM を使用することなくハイブリッド Azure AD への参加が完了します。 |
| **TPM_E_NOTFIPS** (0x80280036/-2144862154) | FIPS モードの TPM は現在サポートされていません。 | このエラーが発生したデバイスで TPM を無効にします。 Windows 10 バージョン 1809 以降では、TPM エラーが自動的に検出され、TPM を使用することなくハイブリッド Azure AD への参加が完了します。 |
| **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775) | TPM がロックアウトされています。 | 一時的なエラーです。 クールダウン時間が終わるまで待ちます。 しばらくしてから参加を試みると成功します。 詳細については、「[TPM の基本事項](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)」を参照してください。 |
| | |


**ネットワーク エラー**:

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **WININET_E_TIMEOUT** (0x80072ee2/-2147012894) | DRS でデバイスを登録するときの一般的なネットワーク タイムアウトです。 | `https://enterpriseregistration.windows.net` とのネットワーク接続を確認します。 |
| **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889) | サーバーの名前またはアドレスを解決できませんでした。 | `https://enterpriseregistration.windows.net` とのネットワーク接続を確認します。 |
| **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866) | サーバーとの接続が異常終了しました。 | しばらくしてからやり直すか、または別の安定したネットワークの場所から参加を試みます。 |
| | |


**その他のエラー**:

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611) | ユーザー デバイス登録イベント ログにイベント ID 220 が存在します。 Windows で、Active Directory 内のコンピューター オブジェクトにアクセスできません。 イベントに Windows エラー コードが含まれているおそれがあります。 エラー コード ERROR_NO_SUCH_LOGON_SESSION (1312) および ERROR_NO_SUCH_USER (1317) は、オンプレミスの Active Directory のレプリケーション問題に関連しています。 | Active Directory のレプリケーションの問題のトラブルシューティングを行います。 これらのレプリケーションの問題は一時的である場合があり、しばらくすると解消される可能性があります。 |
| | |


**サーバーのフェデレーション参加エラー**:

| サーバー エラー コード | サーバー エラー メッセージ | 考えられる原因 | 解像度 |
| --- | --- | --- | --- |
| DirectoryError | Your request is throttled temporarily.\(要求は一時的に調整されました。\) Please try after 300 seconds.\(300 秒後にもう一度お試しください。\) | これは予期されるエラーです。複数の登録要求が立て続けに行われたことが原因と考えられます。 | クールダウン時間が終わったら参加を再試行します |
| | |

**サーバーの同期参加エラー**:

| サーバー エラー コード | サーバー エラー メッセージ | 考えられる原因 | 解像度 |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002:Tenant `UUID` not found.\(テナントが見つかりません\)。 This error might happen if there are no active subscriptions for the tenant.\(このエラーは、テナントにアクティブなサブスクリプションがない場合に発生することがあります。\) Check with your subscription administrator.\(サブスクリプション管理者にご確認ください。\) | サービス接続ポイント オブジェクト内のテナント ID が正しくありません。 | サービス接続ポイント オブジェクトが、確実に正しい Azure AD テナント ID とアクティブなサブスクリプションで構成されているか、またはサービスがテナント内に存在しているようにします。 |
| DirectoryError | The device object by the given ID is not found.\(指定された ID のデバイス オブジェクトが見つかりませんでした。\) | 同期参加では予期されるエラーです。 デバイス オブジェクトが AD から Azure AD に同期されていません。 | Azure AD Connect の同期が完了するのを待ち、同期完了後に次の参加を試みると問題が解決します。 |
| AuthenticationError | The verification of the target computer's SID\(ターゲット コンピューターの SID の検証\) | Azure AD デバイスの証明書が、同期参加時に BLOB に署名するために使用された証明書と一致しません。 このエラーは、通常、同期がまだ完了していないことを意味します。 |  Azure AD Connect の同期が完了するのを待ち、同期完了後に次の参加を試みると問題が解決します。 |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>手順 5:ログを収集して Microsoft サポートに問い合わせる

1. [*Auth.zip* ファイルをダウンロードします](https://cesdiagtools.blob.core.windows.net/windows/Auth.zip)。

1. ファイルを *c:\temp* などのフォルダーに解凍し、そのフォルダーに移動します。
1. 管理者特権の Azure PowerShell セッションから `.\start-auth.ps1 -v -accepteula` を実行します。
1. **[アカウントの切り替え]** を選択して、問題のあるユーザーとの別のセッションに切り替えます。
1. 問題を再現します。
1. **[アカウントの切り替え]** を選択して、トレースを実行している管理者セッションに戻ります。
1. 管理者特権の PowerShell セッションから `.\stop-auth.ps1` を実行します。
1. スクリプトが実行されたフォルダーから *Authlogs* フォルダーを Zip (圧縮) 形式で送信します。
    
## <a name="troubleshoot-post-join-authentication-issues"></a>参加後の認証の問題のトラブルシューティング

### <a name="step-1-retrieve-the-prt-status-by-using-dsregcmd-status"></a>手順 1: `dsregcmd /status` を使用して PRT の状態を取得する

1. コマンド プロンプト ウィンドウを開きます。 
   > [!NOTE] 
   > プライマリ更新トークン (PRT) の状態を取得するには、ログイン ユーザーのコンテキストでコマンド プロンプト ウィンドウを開きます。 

1. `dsregcmd /status` を実行します。 

   "SSO state" セクションに、現在の PRT の状態が表示されます。 

   AzureAdPrt フィールドが *NO* に設定されている場合、Azure AD からの PRT の取得中にエラーが発生しています。 

1. AzureAdPrtUpdateTime が 4 時間を超える場合、PRT の更新中に問題が発生したと考えられます。 デバイスをロックした後、ロックを解除し、PRT の更新を強制的に実行し、時間が更新されているかどうかを確認します。

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2020-07-12 22:57:53.000 UTC
      AzureAdPrtExpiryTime : 2019-07-26 22:58:35.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2020-07-12 22:57:54.000 UTC
   EnterprisePrtExpiryTime : 2020-07-26 22:57:54.000 UTC
    EnterprisePrtAuthority : https://corp.hybridadfs.contoso.com:443/adfs

+----------------------------------------------------------------------+
```

### <a name="step-2-find-the-error-code"></a>手順 2: エラーコードを見つける 

**`dsregcmd` の出力から**

> [!NOTE]
>  この出力は、Windows&nbsp; 10 の 2021 年 5 月の更新プログラム (バージョン 21H1) 以降で利用できます。

"AzureAdPrt" フィールドの下の "Attempt Status" フィールドに、前回の PRT 試行の状態が、その他必要なデバッグ情報と共に表示されます。 以前のバージョンの Windows では、この情報を Azure AD の分析ログと操作ログから抽出します。

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
```

**Azure AD の分析ログと操作ログから**

イベント ビューアーを使用して、PRT の取得時に Azure AD CloudAP プラグインによって記録されたログ エントリを探します。 

1. イベント ビューアーで、Azure AD の操作イベント ログを開きます。 これらは、 **[アプリケーションとサービス ログ]**  >  **[Microsoft]**  >  **[Windows]**  >  **[AAD]** の下に格納されています。 

   > [!NOTE]
   > CloudAP プラグインでは、エラー イベントが操作ログに、情報イベントが分析ログに記録されます。 問題のトラブルシューティングを行うには、分析ログと操作ログの両方のイベントが必要です。 

1. 分析ログ内のイベント 1006 は PRT 取得フローの開始を示し、分析ログ内のイベント 1007 は PRT 取得フローの終了を示します。 Azure AD ログ (分析ログと操作ログ) でイベント 1006 からイベント 1007 までの間に記録されているイベントはすべて、PRT 取得フローの過程で記録されたものです。 

1. 最終的なエラー コードはイベント 1007 に記録されます。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/event-viewer-prt-acquire.png" alt-text="イベント ID 1006 および 1007 が選択されており、最後のエラー コードが強調表示されているイベント ビューアーのスクリーンショット。" border="false":::

### <a name="step-3-troubleshoot-further-based-on-the-found-error-code"></a>手順 3: 見つかったエラー コードに応じてさらにトラブルシューティングを行う

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **STATUS_LOGON_FAILURE** (-1073741715/ 0xc000006d)<br>**STATUS_WRONG_PASSWORD** (-1073741718/ 0xc000006a) | <li>デバイスが Azure AD 認証サービスに接続できません。<li>Azure AD 認証サービスまたは WS-Trust エンドポイントからエラー応答 (HTTP 400) を受け取りました。<br>**注意**: フェデレーション認証には WS-Trust が必要です。 | <li>オンプレミス環境に送信プロキシが必要である場合は、デバイスのコンピューター アカウントが送信プロキシに対する検出とサイレント認証を実行できることを IT 管理者が確認する必要があります。<li>イベント 1081 とイベント 1088 (Azure AD 操作ログ) には、Azure AD 認証サービスから発生したエラーのサーバー エラー コードと WS-Trust エンドポイントから発生したエラーの説明が含まれます。 一般的なサーバー エラー コードとその解決策は、次のセクションに記載されています。 イベント 1081 またはイベント 1088 に先行するイベント 1022 (Azure AD 分析ログ) の最初のインスタンスには、アクセスしている URL が含まれます。 |
| **STATUS_REQUEST_NOT_ACCEPTED** (-1073741616/ 0xc00000d0) | Azure AD 認証サービスまたは WS-Trust エンドポイントからエラー応答 (HTTP 400) を受け取りました。<br>**注意**: フェデレーション認証には WS-Trust が必要です。 | イベント 1081 とイベント 1088 (Azure AD 操作ログ) にはそれぞれ、Azure AD 認証サービスおよび WS-Trust エンドポイントから発生したエラーのサーバー エラー コードとエラーの説明が含まれます。 一般的なサーバー エラー コードとその解決策は、次のセクションに記載されています。 イベント 1081 またはイベント 1088 に先行するイベント 1022 (Azure AD 分析ログ) の最初のインスタンスには、アクセスしている URL が含まれます。 |
| **STATUS_NETWORK_UNREACHABLE** (-1073741252/ 0xc000023c)<br>**STATUS_BAD_NETWORK_PATH** (-1073741634/ 0xc00000be)<br>**STATUS_UNEXPECTED_NETWORK_ERROR** (-1073741628/ 0xc00000c4) | <li>Azure AD 認証サービスまたは WS-Trust エンドポイントからエラー応答 (HTTP 400 以上) を受け取りました。<br>**注意**: フェデレーション認証には WS-Trust が必要です。<li>必要なエンドポイントへのネットワーク接続の問題。 | <li>サーバー エラーの場合、イベント 1081 とイベント 1088 (Azure AD 操作ログ) には、Azure AD 認証サービスのエラー コードと WS-Trust エンドポイントからのエラーの説明が含まれます。 一般的なサーバー エラー コードとその解決策は、次のセクションに記載されています。<li>接続の問題の場合、イベント 1022 (Azure AD 分析ログ) に、アクセスされている URL が含まれ、イベント 1084 (Azure AD 操作ログ) に、ネットワーク スタックからのサブエラー コードが含まれます。 |
| **STATUS_NO_SUCH_LOGON_SESSION**    (-1073741729/ 0xc000005f) | Azure AD 認証サービスでユーザーのドメインが見つからなかったため、ユーザー領域の検出に失敗しました。 | <li>ユーザーの UPN のドメインをカスタム ドメインとして Azure AD に追加する必要があります。 指定された UPN は、イベント 1144 (Azure AD 分析ログ) に含まれます。<li>オンプレミスのドメイン名がルーティング不可能な場合 (jdoe@contoso.local)、代替ログイン ID (AltID) を構成します。 参照: 「[前提条件](hybrid-azuread-join-plan.md)」、「[代替ログイン ID を構成する](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)」。 |
| **AAD_CLOUDAP_E_OAUTH_USERNAME_IS_MALFORMED**   (-1073445812/ 0xc004844c) | ユーザーの UPN の形式が、予期された形式ではありません。<br>**注**:<li>Azure AD 参加済みデバイスの場合、UPN は、ユーザーが LoginUI に入力したテキストです。 <li>ハイブリッド Azure AD 参加済みデバイスの場合は、UPN は、ログイン プロセス中にドメイン コントローラーから返されます。 | <li>ユーザーの UPN は、インターネット標準 [RFC 822](https://www.ietf.org/rfc/rfc0822.txt) に基づくインターネット形式のログイン名である必要があります。 指定された UPN は、イベント 1144 (Azure AD 分析ログ) に含まれます。<li>ハイブリッド参加済みデバイスの場合は必ず、正しい形式で UPN を返すようにドメイン コントローラーを構成します。 ドメイン コントローラーでは、構成済みの UPN が `whoami /upn` で表示されます。<li>オンプレミスのドメイン名がルーティング不可能である場合 (jdoe@contoso.local)、代替ログイン ID (AltID) を構成します。 参照: 「[前提条件](hybrid-azuread-join-plan.md)」、「[代替ログイン ID を構成する](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)」。 |
| **AAD_CLOUDAP_E_OAUTH_USER_SID_IS_EMPTY** (-1073445822/ 0xc0048442) | Azure AD 認証サービスから返された ID トークンにユーザーの SID がありません。 | ネットワーク プロキシがサーバーの応答を妨害したり変更したりしていないことを確認します。 |
| **AAD_CLOUDAP_E_WSTRUST_SAML_TOKENS_ARE_EMPTY** (--1073445695/ 0xc00484c1) | WS-Trust エンドポイントからエラーが返されました。<br>**注意**: フェデレーション認証には WS-Trust が必要です。 | <li>ネットワーク プロキシが WS-Trust の応答を妨害したり変更したりしていないことを確認します。<li>イベント 1088 (Azure AD 操作ログ) には、WS-Trust エンドポイントからのサーバー エラー コードとエラーの説明が含まれます。 一般的なサーバー エラー コードとその解決策は、次のセクションに記載されています。 |
| **AAD_CLOUDAP_E_HTTP_PASSWORD_URI_IS_EMPTY** (-1073445749/ 0xc004848b) | MEX エンドポイントが正しく構成されていません。 MEX 応答に、パスワードの URL が含まれていません。 | <li>ネットワーク プロキシがサーバーの応答を妨害したり変更したりしていないことを確認します。<li>有効な URL が応答で返されるように、MEX の構成を修正してください。 |
| **WC_E_DTDPROHIBITED** (-1072894385/ 0xc00cee4f) | WS-Trust エンドポイントからの XML 応答に、ドキュメント型定義 (DTD) が含まれていました。 XML 応答に DTD は想定されておらず、DTD が含まれていると応答の解析に失敗します。<br>**注意**: フェデレーション認証には WS-Trust が必要です。 | <li>XML 応答で DTD が送信されないように ID プロバイダーの構成を修正します。<li>イベント 1022 (Azure AD 分析ログ) には、アクセスされ、DTD を含む XML 応答を返す URL が含まれます。 |
| | |


**一般的なサーバー エラー コード:**

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **AADSTS50155: デバイス認証に失敗しました** | <li>Azure AD で、PRT を発行するデバイスを認証できません。<li>Azure portal で、デバイスが削除または無効化されていないことを確認します。 この問題の詳細については、「[Azure Active Directory デバイス管理の FAQ](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-11-devices)」を参照してください。 | 「[Azure Active Directory デバイス管理の FAQ](faq.yml#i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell--but-the-local-state-on-the-device-says-it-s-still-registered--what-should-i-do)」にあるこの問題に関する手順に従って、デバイスの参加の種類に基づいてデバイスを再登録します。 |
| **AADSTS50034: ユーザー アカウント `Account` が `tenant id` ディレクトリに存在しない** | Azure AD で、テナント内のユーザー アカウントを検出できません。 | <li>ユーザーが正しい UPN を入力していることを確認します。<li>オンプレミスのユーザー アカウントが Azure AD と同期されていることを確認します。<li>指定された UPN は、イベント 1144 (Azure AD 分析ログ) に含まれます。 |
| **AADSTS50126: 無効なユーザー名またはパスワードにより、資格情報の検証でエラーが発生しました。** | <li>Windows の LoginUI でユーザーが入力したユーザー名とパスワードが正しくありません。<li>テナントでパスワード ハッシュの同期が有効になっており、デバイスがハイブリッド参加済みで、ユーザーがパスワードを変更した直後である場合、新しいパスワードが Azure AD と同期されていない可能性があります。 | 新しい証明書を使用して新しい PRT を取得するには、Azure AD パスワード同期が完了するまで待ちます。 |
| | |


**一般的なネットワーク エラー コード**:

| エラー コード | 理由 | 解像度 |
| --- | --- | --- |
| **ERROR_WINHTTP_TIMEOUT** (12002)<br>**ERROR_WINHTTP_NAME_NOT_RESOLVED** (12007)<br>**ERROR_WINHTTP_CANNOT_CONNECT** (12029)<br>**ERROR_WINHTTP_CONNECTION_ERROR** (12030) | ネットワーク全般に関連した一般的な問題です。 | <li>イベント 1022 (Azure AD 分析ログ) と 1084 (Azure AD 操作ログ) には、アクセスされている URL が含まれます。<li>オンプレミス環境に送信プロキシが必要である場合は、デバイスのコンピューター アカウントが送信プロキシに対する検出とサイレント認証を実行できることを IT 管理者が確認する必要があります。<br><br>追加の[ネットワーク エラー コード](/windows/win32/winhttp/error-messages)を取得します。 |
| | |


### <a name="step-4-collect-logs"></a>手順 4: ログを収集する

**通常のログ**

1. https://aka.ms/icesdptool にアクセスすると、診断ツールを含む *.cab* ファイルが自動的にダウンロードされます。
1. ツールを実行し、シナリオを再現します。
1. Fiddler トレースの場合、ポップアップ表示された証明書の要求を受け入れます。
1. トレース ファイルを保護するためのパスワードの入力が、ウィザードによって求められます。 パスワードを指定します。
1. 最後に、収集したすべてのログが格納されているフォルダー (たとえば、 *%LOCALAPPDATA%\ElevatedDiagnostics\numbers* など) を開きます。
1. 最新の *.cab* の内容については、サポートにお問い合わせください。

**ネットワーク トレース**

> [!NOTE]
> ネットワーク トレースを収集する場合、再現中、Fiddler を使用 "*しない*" ことが重要です。

1.  `netsh trace start scenario=internetClient_dbg capture=yes persistent=yes` を実行します。
1. デバイスをロックした後、ロックを解除します。 ハイブリッド参加済みデバイスの場合、1 分以上待って、PRT 取得タスクを終了させてください。
1. `netsh trace stop` を実行します。
1. *nettrace.cab* ファイルをサポートと共有します。

---

## <a name="known-issues"></a>既知の問題
- モバイル ホットスポットまたは外部 WiFi ネットワークに接続し、 **[設定]**  >  **[アカウント]**  >  **[職場または学校にアクセスする]** の順に移動すると、ハイブリッド Azure AD 参加済みデバイスに 2 つの異なるアカウント (Azure AD 用のアカウントとオンプレミス AD 用のアカウント) が表示される場合があります。 これは UI のみの問題であり、機能には影響しません。

## <a name="next-steps"></a>次のステップ

- [`dsregcmd` コマンドを使用したデバイスのトラブルシューティング](troubleshoot-device-dsregcmd.md)。
- 「[Microsoft エラー ルックアップ ツール](/windows/win32/debug/system-error-code-lookup-tool)」にアクセスする。