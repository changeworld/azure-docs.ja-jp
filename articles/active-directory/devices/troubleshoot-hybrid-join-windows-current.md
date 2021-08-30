---
title: ハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング
description: Windows 10 と Windows Server 2016 のハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 5c601d81053979108ab7c49dee5b1bccbb33bf53
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464012"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>ハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング

この記事の内容は、Windows 10 または Windows Server 2016 を実行しているデバイスに適用されます。

その他の Windows クライアントについては、「[ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)」の記事を参照してください。

この記事は、[ハイブリッド Azure Active Directory 参加済みデバイスの構成](hybrid-azuread-join-plan.md)が済んでいて、次のシナリオに対応していることが前提となります。

- デバイスベースの条件付きアクセス
- [設定のエンタープライズ ローミング](./enterprise-state-roaming-overview.md)
- [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

このドキュメントでは、潜在的な問題を解決するためのトラブルシューティング ガイダンスを示します。

Windows 10 および Windows Server 2016 でハイブリッド Azure Active Directory 参加がサポートされるのは、Windows 10 November 2015 Update 以降となります。

## <a name="troubleshoot-join-failures"></a>参加エラーのトラブルシューティング

### <a name="step-1-retrieve-the-join-status"></a>手順 1:参加状態を取得する

**参加状態を取得するには:**

1. 管理者としてコマンド プロンプトを開きます。
2. 「`dsregcmd /status`」と入力します。

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

以下のフィールドを確認し、必要な値が設定されていることを確認します。

#### <a name="domainjoined--yes"></a>DomainJoined :YES

このフィールドは、デバイスがオンプレミス Active Directory に参加しているかどうかを示します。 この値が **NO** である場合、デバイスはハイブリッド Azure AD 参加を実行できません。

#### <a name="workplacejoined--no"></a>WorkplaceJoined :NO

このフィールドは、デバイスが ("*ワークプレースに参加済み*" としてマークされた) 個人所有のデバイスとして Azure AD に登録されているかどうかを示します。 ドメインに参加していて、なおかつハイブリッド Azure AD 参加済みのコンピューターでは、この値は **NO** になります。 この値が **YES** である場合、ハイブリッド Azure AD 参加が完了する前に、職場または学校アカウントが追加されています。 この場合、Windows 10 バージョン 1607 以降を使用しているときは、このアカウントは無視されます。

#### <a name="azureadjoined--yes"></a>AzureAdJoined :YES

このフィールドは、デバイスが参加しているかどうかを示します。 デバイスが Azure AD 参加済みデバイスであるか、ハイブリッド Azure AD 参加済みデバイスである場合、この値は **YES** になります。
この値が **NO** である場合、Azure AD への参加は済んでいません。

詳細なトラブルシューティングを行うには、次の手順に進みます。

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>手順 3:参加がエラーとなったフェーズとエラー コードを確認する

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 以降

参加状態の出力の [Diagnostic Data] セクションで [Previous Registration] サブセクションを探します。 このセクションは、デバイスがドメイン参加済みで、ハイブリッド Azure AD 参加ができない場合にのみ表示されます。
[Error Phase] フィールドは参加エラーのフェーズを表し、[Client ErrorCode] は参加操作のエラー コードを表します。

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Windows 10 の古いバージョン

イベント ビューアーのログで、参加エラーのフェーズとエラー コードを確認します。

1. イベント ビューアーで **[User Device Registration]\(ユーザー デバイス登録\)** イベントのログを開きます。 **[Applications and Services Log]\(アプリケーションとサービスのログ\)**  >  **[Microsoft]**  >  **[Windows]**  >  **[User Device Registration]\(ユーザー デバイス登録\)** の下にあります。
2. イベント ID が 304、305、307 のイベントを探します。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="イベント ビューアーのスクリーンショット。ID 304 のイベントが選択され、その情報が表示され、エラー コードとフェーズが強調表示されている。" border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="イベント ビューアーのスクリーンショット。ID 305 のイベントが表示され、その情報が表示され、エラー コードが強調表示されている。" border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>手順 4:以下の一覧から考えられる原因と解決策を確認する

#### <a name="pre-check-phase"></a>事前チェック フェーズ

考えられるエラーの原因:

- デバイスがドメイン コントローラーを認識していない。
   - デバイスは、組織の内部ネットワークに配置するか、オンプレミスの Active Directory (AD) ドメイン コントローラーをネットワークで認識する VPN に配置する必要があります。

#### <a name="discover-phase"></a>検出フェーズ

考えられるエラーの原因:

- サービス接続ポイント (SCP) オブジェクトが正しく構成されていない、または DC から SCP オブジェクトを読み取ることができません。
   - デバイスが属している AD フォレストに、Azure AD 内の検証済みドメイン名を指す有効な SCP オブジェクトが必要です。
   - 詳細については、[サービス接続ポイントの構成](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)に関するセクションを参照してください。
- 検出エンドポイントから検出メタデータに接続してフェッチできません。
   - 登録と承認のエンドポイントを検出するには、デバイスがシステム コンテキストで `https://enterpriseregistration.windows.net` にアクセスできる必要があります。
   - オンプレミス環境に送信プロキシが必要である場合は、デバイスのコンピューター アカウントが送信プロキシに対する検出とサイレント認証を実行できることを IT 管理者が確認する必要があります。
- ユーザー領域エンドポイントに接続して領域の検出を実行できません。 (Windows 10 バージョン 1809 以降のみ)
   - 検証済みドメインの領域検出を実行してドメインの種類 (管理対象/フェデレーション) を判別するには、デバイスがシステム コンテキストで `https://login.microsoftonline.com` にアクセスできる必要があります。
   - オンプレミス環境に送信プロキシが必要である場合は、デバイスのシステム コンテキストで送信プロキシに対する検出とサイレント認証を実行できることを IT 管理者が確認する必要があります。

**一般的なエラー コード:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - 理由:SCP オブジェクトを読み取れず、Azure AD テナント情報を取得できません。
   - 解決策:[サービス接続ポイントの構成](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)に関するセクションを参照してください。
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - 理由:一般的な検出エラー。 DRS から検出メタデータを取得できませんでした。
   - 解決策:この後のサブエラーを確認して詳細を調べます。
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609)
   - 理由:検出の実行中に操作がタイムアウトになりました。
   - 解決策:システム コンテキストで `https://enterpriseregistration.windows.net` にアクセスできることを確認します。 詳細については、[ネットワーク接続の要件](hybrid-azuread-join-managed-domains.md#prerequisites)に関するセクションを参照してください。
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - 理由:一般的な領域検出エラー。 STS からドメインの種類 (管理対象/フェデレーション) を判別できませんでした。
   - 解決策:この後のサブエラーを確認して詳細を調べます。

**一般的なサブエラー コード:**

検出エラー コードのサブエラー コードを確認するには、次のいずれかの方法を使用します。

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 以降

参加状態の出力の [Diagnostic Data] セクションで [DRS Discovery Test] を探します。 このセクションは、デバイスがドメイン参加済みで、ハイブリッド Azure AD 参加ができない場合にのみ表示されます。

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

##### <a name="older-windows-10-versions"></a>Windows 10 の古いバージョン

イベント ビューアーのログで、参加エラーのフェーズとエラー コードを確認します。

1. イベント ビューアーで **[User Device Registration]\(ユーザー デバイス登録\)** イベントのログを開きます。 **[Applications and Services Log]\(アプリケーションとサービスのログ\)**  >  **[Microsoft]**  >  **[Windows]**  >  **[User Device Registration]\(ユーザー デバイス登録\)** の下にあります。
2. イベント ID が 201 のイベントを探します。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="イベント ビューア―のスクリーンショット。ID 201 のイベントが選択され、その情報が表示され、エラー コードが強調表示されている。" border="false":::

###### <a name="network-errors"></a>ネットワーク エラー

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - 理由:サーバーとの接続を確立できませんでした。
   - 解決策:必要な Microsoft リソースとのネットワーク接続を確認します。 詳細については、[ネットワーク接続の要件](hybrid-azuread-join-managed-domains.md#prerequisites)に関するページを参照してください。
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - 理由:一般的なネットワーク タイムアウト。
   - 解決策:必要な Microsoft リソースとのネットワーク接続を確認します。 詳細については、[ネットワーク接続の要件](hybrid-azuread-join-managed-domains.md#prerequisites)に関するページを参照してください。
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - 理由:ネットワーク スタックがサーバーからの応答をデコードできませんでした。
   - 解決策:ネットワーク プロキシがサーバーの応答を妨害したり変更したりしていないことを確認します。

###### <a name="http-errors"></a>HTTP エラー

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - 理由:SCP オブジェクトが間違ったテナント ID で構成されています。 あるいは、テナントにアクティブなサブスクリプションが見つかりませんでした。
   - 解決策:SCP オブジェクトが正しい Azure AD テナント ID で構成されていることと、アクティブなサブスクリプションがテナントに存在することを確認します。
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - 理由:DRS サーバーからの HTTP 503。
   - 解決策:サーバーを現在使用できません。 サーバーがオンラインに戻れば、今後の参加試行が成功する可能性があります。

###### <a name="other-errors"></a>その他のエラー

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - 理由:サーバー応答 JSON を解析できませんでした。 HTML 認証ページでプロキシが HTTP 200 を返したことが原因と考えられます。
   - 解決策:オンプレミス環境に送信プロキシが必要である場合は、デバイスのシステム コンテキストで送信プロキシに対する検出とサイレント認証を実行できることを IT 管理者が確認する必要があります。

#### <a name="authentication-phase"></a>認証フェーズ

フェデレーション ドメイン アカウントにのみ適用されます。

エラーの原因:

- DRS リソースのアクセス トークンをサイレントに取得できません。
   - Windows 10 デバイスは、アクティブな WS-Trust エンドポイントに対する統合 Windows 認証を使用して、フェデレーション サービスから認証トークンを取得します。 詳細:[フェデレーション サービスの構成](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**一般的なエラー コード:**

イベント ビューアーのログで、エラー コード、サブエラー コード、サーバー エラー コード、およびサーバー エラー メッセージを確認します。

1. イベント ビューアーで **[User Device Registration]\(ユーザー デバイス登録\)** イベントのログを開きます。 **[Applications and Services Log]\(アプリケーションとサービスのログ\)**  >  **[Microsoft]**  >  **[Windows]**  >  **[User Device Registration]\(ユーザー デバイス登録\)** の下にあります。
2. イベント ID が 305 のイベントを探します。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="イベント ビューア―のスクリーンショット。ID 305 のイベントが表示されている。その情報が表示され、ADAL エラー コードと状態が強調表示されている。" border="false":::

##### <a name="configuration-errors"></a>構成エラー

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - 理由:認証プロトコルが WS-Trust ではありません。
   - 解決策:オンプレミスの ID プロバイダーが WS-Trust をサポートしている必要があります。
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - 理由:オンプレミスのフェデレーション サービスから XML 応答が返されませんでした。
   - 解決策:MEX エンドポイントから有効な XML が返されることを確認します。 プロキシが妨害を行ったり XML 以外の応答を返したりしていないことを確認します。
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - 理由:ユーザー名/パスワード認証のエンドポイントを検出できませんでした。
   - 解決策:オンプレミスの ID プロバイダーの設定を確認します。 WS-Trust エンドポイントが有効になっていることを確認し、MEX 応答にこれらの正しいエンドポイントが含まれていることを確認します。

##### <a name="network-errors"></a>ネットワーク エラー

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - 理由:一般的なネットワーク タイムアウト。
   - 解決策:システム コンテキストで `https://login.microsoftonline.com` にアクセスできることを確認します。 システム コンテキストでオンプレミスの ID プロバイダーにアクセスできることを確認します。 詳細については、[ネットワーク接続の要件](hybrid-azuread-join-managed-domains.md#prerequisites)に関するページを参照してください。
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - 理由:認証エンドポイントとの接続が中止されました。
   - 解決策:しばらくしてからやり直すか、または別の安定したネットワークの場所から参加を試みます。
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - 理由:以前は SSL (Secure Sockets Layer) として知られていた、サーバーによって送信されたトランスポート層セキュリティ (TLS) 証明書を検証できませんでした。
   - 解決策:クライアント時間のずれを確認します。 しばらくしてからやり直すか、または別の安定したネットワークの場所から参加を試みます。
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - 理由:`https://login.microsoftonline.com` への接続に失敗しました。
   - 解決策:`https://login.microsoftonline.com` とのネットワーク接続を確認します。

##### <a name="other-errors"></a>その他のエラー

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - 理由:オンプレミスの ID プロバイダーからの SAML トークンが Azure AD で受け付けられませんでした。
   - 解決策:フェデレーション サーバーの設定を確認します。 認証ログでサーバー エラー コードを探します。
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - 理由:サーバーの WS-Trust 応答でエラー例外が報告され、アサーションの取得に失敗しました。
   - 解決策:フェデレーション サーバーの設定を確認します。 認証ログでサーバー エラー コードを探します。
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - 理由:トークン エンドポイントからアクセス トークンを取得するときにエラーが発生しました。
   - 解決策:ADAL ログで根本的なエラーを探します。
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - 理由:一般的な ADAL エラー
   - 解決策:認証ログで、サブエラー コードまたはサーバー エラー コードを探します。

#### <a name="join-phase"></a>参加フェーズ

エラーの原因:

登録の種類を確認し、以下の一覧でエラー コードを探します。

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 以降

参加状態の出力の [Diagnostic Data] セクションで [Previous Registration] サブセクションを探します。 このセクションは、デバイスがドメイン参加済みで、ハイブリッド Azure AD 参加ができない場合にのみ表示されます。
[Registration Type] フィールドは、実行された参加の種類を表します。

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

#### <a name="older-windows-10-versions"></a>Windows 10 の古いバージョン

イベント ビューアーのログで、参加エラーのフェーズとエラー コードを確認します。

1. イベント ビューアーで **[User Device Registration]\(ユーザー デバイス登録\)** イベントのログを開きます。 **[Applications and Services Log]\(アプリケーションとサービスのログ\)**  >  **[Microsoft]**  >  **[Windows]**  >  **[User Device Registration]\(ユーザー デバイス登録\)** の下にあります。
2. イベント ID が 204 のイベントを探します。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="イベント ビューア―のスクリーンショット。ID 204 のイベントに関する情報が表示され、エラー コード、HTTP 状態、およびメッセージが強調表示されている。" border="false":::

##### <a name="http-errors-returned-from-drs-server"></a>DRS サーバーから返される HTTP エラー

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - 理由:DRS から次のエラー コードのエラー応答を受け取りました。"DirectoryError"
   - 解決策:考えられる原因と解決策については、サーバー エラー コードを参照してください。
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - 理由:DRS から次のエラー コードのエラー応答を受け取りました。"AuthenticationError" (エラー サブコードは "DeviceNotFound" 以外)
   - 解決策:考えられる原因と解決策については、サーバー エラー コードを参照してください。
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - 理由:DRS から次のエラー コードのエラー応答を受け取りました。"DirectoryError"
   - 解決策:考えられる原因と解決策については、サーバー エラー コードを参照してください。

##### <a name="tpm-errors"></a>TPM エラー

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - 理由:TPM 操作が失敗したか、または無効でした。
   - 解決策:無効な sysprep イメージが原因と考えられます。 sysprep イメージの作成元であるマシンが、Azure AD に参加していない、ハイブリッド Azure AD に参加していない、あるいは Azure AD に登録されていないことを確認します。
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - 理由:一般的な TPM エラー。
   - 解決策:このエラーが発生したデバイスで TPM を無効にします。 Windows 10 バージョン1809 以降では、TPM エラーが自動的に検知され、TPM を使用することなくハイブリッド Azure AD への参加が完了します。
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - 理由:FIPS モードの TPM は現在サポートされていません。
   - 解決策:このエラーが発生したデバイスで TPM を無効にします。 Windows 1809 では、TPM エラーが自動的に検知され、TPM を使用することなくハイブリッド Azure AD への参加が完了します。
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - 理由:TPM がロックアウトされました。
   - 解決策:一時的なエラーです。 クールダウン時間が終わるまで待ちます。 しばらくしてから参加を試みると成功します。 詳細については、[TPM の基礎](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)に関する記事を参照してください。

##### <a name="network-errors"></a>ネットワーク エラー

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - 理由:DRS でデバイスを登録するときの一般的なネットワーク タイムアウトです。
   - 解決策:`https://enterpriseregistration.windows.net` とのネットワーク接続を確認します。
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - 理由:サーバーの名前またはアドレスを解決できませんでした。
   - 解決策:`https://enterpriseregistration.windows.net` とのネットワーク接続を確認します。 ネットワーク内およびデバイスでのホスト名の DNS 解決が正確であることを確認します。
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - 理由:サーバーとの接続が異常終了しました。
   - 解決策:しばらくしてからやり直すか、または別の安定したネットワークの場所から参加を試みます。

##### <a name="other-errors"></a>その他のエラー

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - 理由: [User Device Registration]\(ユーザー デバイス登録\) イベント ログに EventID 220 が存在します。 Windows で、Active Directory 内のコンピューター オブジェクトにアクセスできません。 イベントに Windows エラー コードが含まれている可能性があります。 エラー コード ERROR_NO_SUCH_LOGON_SESSION (1312) および ERROR_NO_SUCH_USER (1317) の場合、これらのエラー コードはオンプレミス AD のレプリケーションの問題に関連しています。
   - 解決策: AD のレプリケーションの問題をトラブルシューティングします。 レプリケーションの問題は一時的なものの場合があり、一定期間後に解消される可能性があります。

##### <a name="federated-join-server-errors"></a>サーバーのフェデレーション参加エラー

| サーバー エラー コード | サーバー エラー メッセージ | 考えられる原因 | 解像度 |
| --- | --- | --- | --- |
| DirectoryError | Your request is throttled temporarily.\(要求は一時的に調整されました。\) Please try after 300 seconds.\(300 秒後にもう一度お試しください。\) | 予期されるエラーです。 複数の登録要求が立て続けに行われたことが原因と考えられます。 | クールダウン時間が終わったら参加を再試行してください。 |

##### <a name="sync-join-server-errors"></a>サーバーの同期参加エラー

| サーバー エラー コード | サーバー エラー メッセージ | 考えられる原因 | 解像度 |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002:Tenant <UUID> not found.\(テナントが見つかりません\)。 This error may happen if there are no active subscriptions for the tenant.\(このエラーは、テナントにアクティブなサブスクリプションがない場合に発生することがあります。\) Check with your subscription administrator.\(サブスクリプション管理者にご確認ください。\) | SCP オブジェクト内のテナント ID が正しくありません。 | SCP オブジェクトが正しい Azure AD テナント ID で構成されていることと、アクティブなサブスクリプションがテナントに存在することを確認します。 |
| DirectoryError | The device object by the given ID is not found.\(指定された ID のデバイス オブジェクトが見つかりませんでした。\) | 同期参加で予期されるエラーです。 デバイス オブジェクトが AD から Azure AD に同期されていません。 | Azure AD Connect の同期が完了するのを待ち、同期完了後に次の参加を試みると問題が解決します。 |
| AuthenticationError | The verification of the target computer's SID\(ターゲット コンピューターの SID の検証\) | Azure AD デバイスの証明書が、同期参加時に BLOB に署名するために使用された証明書と一致しません。 通常このエラーは、同期がまだ完了していないことを示します。 |  Azure AD Connect の同期が完了するのを待ち、同期完了後に次の参加を試みると問題が解決します。 |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>手順 5:ログを収集して Microsoft サポートに問い合わせる

[https://cesdiagtools.blob.core.windows.net/windows/Auth.zip](https://cesdiagtools.blob.core.windows.net/windows/Auth.zip) から Auth.zip ファイルをダウンロードします。

1. ファイルを c:\temp などのフォルダーに解凍し、そのフォルダーに移動します。
1. 管理者特権の PowerShell セッションから **.\start-auth.ps1 -v -accepteula** を実行します。
1. アカウントの切り替えを使用して、問題のあるユーザーとの別のセッションに切り替えます。
1. 問題を再現します。
1. アカウントの切り替えを使用して、トレースを実行している管理者セッションに戻ります。
1. 管理者特権の PowerShell セッションから **.\stop-auth.ps1** を実行します。
1. スクリプトが実行されたフォルダーから **Authlogs** フォルダーを Zip 形式で送信します。
    
## <a name="troubleshoot-post-join-authentication-issues"></a>参加後の認証の問題のトラブルシューティング

### <a name="step-1-retrieve-prt-status-using-dsregcmd-status"></a>手順 1: dsregcmd /status を使用して PRT の状態を取得する

**PRT の状態を取得するには:**

1. コマンド プロンプトを開きます。 
   > [!NOTE] 
   > PRT の状態を取得するには、コマンド プロンプトをログイン ユーザーのコンテキストで実行する必要があります 

2. 「dsregcmd /status」と入力します 

3. 現在の PRT の状態が [SSO state]\(SSO の状態\) セクションに表示されます。 

4. AzureAdPrt フィールドが "NO" に設定されている場合、Azure AD からの PRT の取得中にエラーが発生しています。 

5. AzureAdPrtUpdateTime が 4 時間を超える場合、おそらく PRT の更新中に問題が発生しています。 デバイスをロックしてからロックを解除して、PRT の更新を強制的に実行し、時間が更新されているかどうかを確認します。

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

### <a name="from-dsregcmd-output"></a>dsregcmd の出力から

> [!NOTE]
>  **Windows 10 May 2021 Update (バージョン 21H1)** から利用できます。

[AzureAdPrt] フィールドの下の [Attempt Status]\(試行状態\) フィールドに、前回の PRT 試行の状態がその他必要なデバッグ情報と共に表示されます。 以前のバージョンの Windows では、この情報を AAD の分析ログと操作ログから抽出する必要があります。

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

### <a name="from-aad-analytic-and-operational-logs"></a>AAD の分析ログと操作ログから

PRT の取得時に AAD CloudAP プラグインによって記録されたログ エントリを、イベント ビューアーを使用して検索します 

1. AAD のイベント ログをイベント ビューアーで開きます。 これは、[Application and Services Log]\(アプリケーションとサービスのログ \) > [Microsoft] > [Windows] > [AAD] にあります 

   > [!NOTE]
   > CloudAP プラグインでは、エラー イベントが操作ログに、情報イベントが分析ログに記録されます。 問題をトラブルシューティングするには、分析ログと操作ログの両方のイベントが必要です。 

2. 分析ログのイベント 1006 は PRT 取得フローの開始を表し、分析ログのイベント 1007 は PRT 取得フローの終了を表します。 AAD ログ (分析ログと操作ログ) のイベント 1006 からイベント 1007 の間に記録されているイベントはすべて、PRT 取得フローの過程で記録されたものです。 

3. 最終的なエラー コードはイベント 1007 に記録されます。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/event-viewer-prt-acquire.png" alt-text="イベント ビューアーのスクリーンショット。ID 1006 と ID 1007 のイベントが赤枠で囲まれ、最終的なエラー コードが強調表示されている。" border="false":::

### <a name="step-3-follow-additional-troubleshooting-based-on-the-found-error-code-from-the-list-below"></a>手順 3: 見つかったエラー コードに応じてさらなるトラブルシューティング (下記) を実施する

**STATUS_LOGON_FAILURE** (-1073741715/ 0xc000006d)

**STATUS_WRONG_PASSWORD** (-1073741718/ 0xc000006a)

理由: 
-  デバイスが AAD 認証サービスに接続できません
-  AAD 認証サービスまたは WS-Trust エンドポイントからエラー応答 (HTTP 400) を受信しました。
> [!NOTE]
> フェデレーション認証には WS-Trust が必要です

解決策: 
-  オンプレミス環境に送信プロキシが必要である場合は、デバイスのコンピューター アカウントが送信プロキシに対する検出とサイレント認証を実行できることを IT 管理者が確認する必要があります。
-  AAD 認証サービスと WS-Trust エンドポイントから発生したエラーでは、それぞれイベント 1081 とイベント 1088 (AAD 操作ログ) に、サーバー エラー コードとエラーの説明が含まれています。 一般的なサーバー エラー コードとその解決策は、次のセクションに記載されています。 アクセス中の URL は、イベント 1081 またはイベント 1088 に先行する最初のイベント 1022 (AAD 分析ログ) のインスタンスに含まれています。

---

**STATUS_REQUEST_NOT_ACCEPTED** (-1073741616/ 0xc00000d0)

理由:
-  AAD 認証サービスまたは WS-Trust エンドポイントからエラー応答 (HTTP 400) を受信しました。
> [!NOTE]
> フェデレーション認証には WS-Trust が必要です

解決策:
-  AAD 認証サービスと WS-Trust エンドポイントから発生したエラーでは、それぞれイベント 1081 とイベント 1088 (AAD 操作ログ) に、サーバー エラー コードとエラーの説明が含まれています。 一般的なサーバー エラー コードとその解決策は、次のセクションに記載されています。 アクセス中の URL は、イベント 1081 またはイベント 1088 に先行する最初のイベント 1022 (AAD 分析ログ) のインスタンスに含まれています。

---

**STATUS_NETWORK_UNREACHABLE** (-1073741252/ 0xc000023c)

**STATUS_BAD_NETWORK_PATH** (-1073741634/ 0xc00000be)

**STATUS_UNEXPECTED_NETWORK_ERROR** (-1073741628/ 0xc00000c4)

理由:
-  AAD 認証サービスまたは WS-Trust エンドポイントからエラー応答 (HTTP 400 を超えるもの) を受信しました。
> [!NOTE]
> フェデレーション認証には WS-Trust が必要です
-  必要なエンドポイントへのネットワーク接続の問題

解決策: 
-  サーバー エラーでは、AAD 認証サービスと WS-Trust エンドポイントからのエラー コードとエラーの説明がそれぞれイベント 1081 とイベント 1088 (AAD 操作ログ) に含まれています。 一般的なサーバー エラー コードとその解決策は、次のセクションに記載されています。
-  接続の問題の場合、イベント 1022 (AAD 分析ログ) とイベント 1084 (AAD 操作ログ) にそれぞれ、アクセス中の URL とネットワーク スタックからのサブエラー コードが含まれています。

---
**STATUS_NO_SUCH_LOGON_SESSION**    (-1073741729/ 0xc000005f)

理由: 
-  AAD 認証サービスがユーザーのドメインを見つけることができなかったため、ユーザー領域の検出に失敗しました

解決策:
-  AAD には、ユーザーの UPN のドメインがカスタムドメインとして追加されている必要があります。 指定された UPN は、イベント 1144 (AAD 分析ログ) に含まれています。
-  オンプレミス ドメイン名がルーティング不可能である場合 (不明なユーザー jdoe@contoso.local など)、代替ログイン ID (AltID: Alternate Login ID) を構成します。 参照: [前提条件](hybrid-azuread-join-plan.md) [configuring-alternate-login-id](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) 

---

**AAD_CLOUDAP_E_OAUTH_USERNAME_IS_MALFORMED**   (-1073445812/ 0xc004844c)

理由: 
-  ユーザーの UPN の形式が正しくありません。 
> [!NOTE] 
> - Azure AD 参加済みデバイスの場合、UPN は LoginUI でユーザーが入力したテキストになります。
> - Hybrid Azure AD 参加済みデバイスの場合は、ログイン プロセス中にドメイン コントローラーから UPN が返されます。

解決策:
-  ユーザーの UPN は、インターネット標準 [RFC 822](https://www.ietf.org/rfc/rfc0822.txt) に基づくインターネット形式のログイン名である必要があります。 指定された UPN は、イベント 1144 (AAD 分析ログ) に含まれています。
-  Hybrid 参加済みデバイスの場合は、正しい形式で UPN を返すようにドメイン コントローラーが構成されていることを確認します。 ドメイン コントローラーにおける構成済みの UPN は、whoami/upn で表示されます。
-  オンプレミス ドメイン名がルーティング不可能である場合 (不明なユーザー jdoe@contoso.local など)、代替ログイン ID (AltID: Alternate Login ID) を構成します。 参照: [前提条件](hybrid-azuread-join-plan.md) [configuring-alternate-login-id](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) 

---

**AAD_CLOUDAP_E_OAUTH_USER_SID_IS_EMPTY** (-1073445822/ 0xc0048442)

理由:
-  AAD 認証サービスから返された ID トークンにユーザーの SID が欠落しています

解決策: 
-  ネットワーク プロキシがサーバーの応答を妨害したり変更したりしていないことを確認します。 

---

**AAD_CLOUDAP_E_WSTRUST_SAML_TOKENS_ARE_EMPTY** (--1073445695/ 0xc00484c1)

理由:
-  WS-Trust エンドポイントからエラーが返されました。
> [!NOTE]
> フェデレーション認証には WS-Trust が必要です

解決策: 
-  ネットワーク プロキシが WS-Trust の応答を妨害したり変更したりしていないことを確認します。
-  イベント 1088 (AAD 操作ログ) には、WS-Trust エンドポイントからのサーバー エラー コードとエラーの説明が含まれています。 一般的なサーバー エラー コードとその解決策は、次のセクションに記載されています

---

**AAD_CLOUDAP_E_HTTP_PASSWORD_URI_IS_EMPTY** (-1073445749/ 0xc004848b)

理由:
-  MEX エンドポイントの構成に誤りがあります。 MEX 応答に、パスワードの URL が含まれていません

解決策: 
-  ネットワーク プロキシがサーバーの応答を妨害したり変更したりしていないことを確認します
-  有効な URL が応答で返されるように、MEX の構成を修正してください。    

---

**WC_E_DTDPROHIBITED** (-1072894385/ 0xc00cee4f)

理由: 
-  WS-TRUST エンドポイントからの XML 応答に DTD が含まれています。 XML 応答に DTD は想定されておらず、DTD が含まれていると応答の解析に失敗します。
> [!NOTE]
> フェデレーション認証には WS-Trust が必要です

解決策:
-  XML 応答で DTD が送信されないように ID プロバイダーの構成を修正します。 
-   DTD を含んだ XML 応答を返す URL は、イベント 1022 (AAD 分析ログ) に記録されます。

---

**一般的なサーバー エラー コード:**

**AADSTS50155: デバイス認証に失敗しました**

理由: 
-  PRT を発行するデバイスを AAD が認証できません
-  デバイスが削除されたり無効化されたりしていないことを Azure portal で確認します。 [詳細情報](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-devices)

解決策:
-  [こちら](faq.yml#i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell--but-the-local-state-on-the-device-says-it-s-still-registered--what-should-i-do)に記載されている手順に従い、デバイスの参加の種類に基づいてデバイスを再登録します。

---

**AADSTS50034: ユーザー アカウント <Account> が <tenant id> ディレクトリに存在しない**

理由: 
-  テナント内のユーザー アカウントを AAD が検出できません。

解決策:
-  ユーザーが正しい UPN を入力していることを確認します。
-  オンプレミスのユーザー アカウントが AAD と同期されていることを確認します。
-  指定された UPN は、イベント 1144 (AAD 分析ログ) に含まれています。

---

**AADSTS50126: 無効なユーザー名またはパスワードにより、資格情報の検証でエラーが発生しました。**

理由: 
-  Windows の LoginUI でユーザーが入力したユーザー名とパスワードが正しくありません。
-  テナントでパスワード ハッシュの同期が有効になっており、なおかつ、デバイスに Hybrid Join が使用されていて、ユーザーがパスワードを変更した直後である場合、おそらく新しいパスワードが AAD と同期されていません。 

解決策:
-  新しい資格情報を含んだ新しい PRT を AAD Sync が取得するまで待ちます。 

---

**一般的なネットワーク エラー コード:**

**ERROR_WINHTTP_TIMEOUT** (12002)

**ERROR_WINHTTP_NAME_NOT_RESOLVED** (12007)

**ERROR_WINHTTP_CANNOT_CONNECT** (12029)

**ERROR_WINHTTP_CONNECTION_ERROR** (12030)

理由: 
-  ネットワーク全般に関連した一般的な問題です。 

解決策: 
-  アクセス中の URL は、イベント 1022 (AAD 分析ログ) と 1084 (AAD 操作ログ) に含まれています
-  オンプレミス環境に送信プロキシが必要である場合は、デバイスのコンピューター アカウントが送信プロキシに対する検出とサイレント認証を実行できることを IT 管理者が確認する必要があります

> [!NOTE]
> その他のネットワーク エラー コードについては、[こちら](/windows/win32/winhttp/error-messages)を参照してください。

---

### <a name="step-4-collect-logs"></a>手順 4: ログを収集する ###

**通常のログ**

1. https://aka.ms/icesdptool にアクセスします。診断ツールを含んだ .cab ファイルが自動的にダウンロードされます。
2. 再現が完了したら、このツールを実行してシナリオを再現します。 プロセスを終了します。
3. Fiddler トレースの場合、ポップアップ表示される証明書の要求を受け入れます。
4. トレース ファイルを保護するためのパスワードの入力が、ウィザードによって求められます。 パスワードを指定します。
5. 最後に、収集したすべてのログが格納されているフォルダーを開きます。 通常、%LOCALAPPDATA%\ElevatedDiagnostics\<numbers> のようなフォルダーにあります
7. 収集されたログはすべて latest.cab に含まれています。サポートに連絡して、その内容を伝えます。

**ネットワーク トレース**

> [!NOTE]
> ネットワーク トレースの収集について: 再現中は Fiddler を使用しないことが大切です。

1.  netsh trace start scenario=InternetClient_dbg capture=yes persistent=yes
2.  デバイスをロックしてからロックを解除します。 Hybrid Join が使用されているデバイスの場合は、1 分以上待って PRT 取得タスクを完了させてください。
3.  netsh trace stop
4.  nettrace.cab を共有します

---

## <a name="known-issues"></a>既知の問題
- [設定] -> [アカウント] -> [職場または学校にアクセスする] で、Hybrid Azure AD 参加済みデバイスには、モバイル ホットスポットまたは外部 WiFi ネットワークに接続されているときに、Azure AD 用に 1 つとオンプレミス AD 用に 1 つという 2 つの異なるアカウントが表示される場合があります。 これは UI のみの問題であり、機能には影響しません。

## <a name="next-steps"></a>次のステップ

- [dsregcmd コマンドを使用したデバイスのトラブルシューティング](troubleshoot-device-dsregcmd.md)に進みます。

- [Microsoft エラー ルックアップ ツール](/windows/win32/debug/system-error-code-lookup-tool)
