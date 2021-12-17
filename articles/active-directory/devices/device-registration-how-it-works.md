---
title: Azure AD デバイス登録のしくみ
description: マネージドおよびフェデレーション ドメインのための Azure AD デバイス登録フロー
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bc24ecd387c011b5a8a36f380916ab815c1f0ab
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254282"
---
# <a name="how-it-works-device-registration"></a>動作のしくみ: デバイス登録

デバイス登録は、クラウドベース認証の前提条件です。 デバイスは通常、Azure AD または Hybrid Azure AD Join を使用してデバイス登録を完了します。 この記事では、マネージドおよびフェデレーションの環境で Azure AD 参加およびハイブリッド Azure AD 参加がどのように機能するのかについて説明します。これらのデバイスでの Azure AD 認証の詳細については、[プライマリ更新トークン](concept-primary-refresh-token.md#detailed-flows)に関する記事を参照してください。

## <a name="azure-ad-joined-in-managed-environments"></a>マネージド環境での Azure AD 参加

:::image type="content" source="media/device-registration-how-it-works/device-registration-azure-ad-managed.png" alt-text="マネージド環境での Azure AD 参加デバイス フロー" lightbox="media/device-registration-how-it-works/device-registration-azure-ad-managed.png":::

| フェーズ | 説明 |
| :----: | :----------- |
| A | Azure AD 参加デバイスの登録の最も一般的な方法は、OOBE (Out-Of-Box Experience) 中に、クラウド エクスペリエンス ホスト (CXH) アプリケーションに Azure AD 参加 Web アプリケーションを読み込む方法です。 承認エンドポイントを検出するために、アプリケーションから Azure AD OpenID 構成エンドポイントに GET 要求が送信されます。 承認エンドポイントを含む OpenID 構成が、JSON ドキュメントとして Azure AD からアプリケーションに返されます。 |
| B | アプリケーションで承認エンドポイント用のサインイン要求が作成され、ユーザーの資格情報が収集されます。 |
| C | ユーザーがユーザー名を (UPN 形式で) 指定すると、アプリケーションはユーザーの対応する領域情報を検出するために GET 要求を Azure AD に送信します。 この情報により、環境がマネージドまたはフェデレーションのいずれであるかが判明します。 Azure AD は、情報を JSON オブジェクトで返します。 アプリケーションでは、環境がマネージド (非フェデレーション) であると判断します。<br><br>このフェーズの最後の手順では、アプリケーションで認証バッファーが作成され、OOBE 中の場合、OOBE の最後に自動サインインするために一時的にキャッシュされます。 アプリケーションが 資格情報を Azure AD に POST し、そこで検証されます。 Azure AD は、要求を含む ID トークンを返します。 |
| D | アプリケーションは、MDM の使用条件 (mdm_tou_url 要求) を検索します。 存在する場合、アプリケーションは要求の値から使用条件を取得して内容をユーザーに提示します。ユーザーが使用条件に同意するまで待機します。 この手順は省略可能であり、要求が存在しない場合、または要求値が空の場合はスキップされます。 |
| E | アプリケーションは、デバイス登録検出要求を Azure Device Registration Service (ADRS) に送信します。 Azure DRS は、デバイス登録を完了するためのテナント固有の URI を返す探索データ ドキュメントを返します。 |
| F | アプリケーションでは、デバイス キー (dkpub/dkpriv) と呼ばれる TPM にバインドされた (推奨) RSA 2048 ビットのキー ペアが作成されます。 アプリケーションは、dkpub と公開キーを使用して証明書要求を作成し、dkpriv を使用して証明書要求に署名します。 次に、アプリケーションでは TPM のストレージ ルート キーから 2 番目のキー ペアが派生されます。 このキーはトランスポート キー (tkpub/tkpriv) です。 |
| G | アプリケーションは、ID トークン、証明書要求、tkpub、構成証明データを含むデバイス登録要求を Azure DRS に送信します。 Azure DRS では、ID トークンが検証され、デバイス ID が作成され、含まれている証明書要求に基づいて証明書が作成されます。 その後、Azure DRS はデバイス オブジェクトを Azure AD に書き込み、デバイス ID とデバイス証明書をクライアントに送信します。 |
| H | Azure DRS からデバイス ID とデバイス証明書を受け取ることによってデバイス登録が完了します。 デバイス ID は将来の参照用に保存され (`dsregcmd.exe /status` から参照できます)、デバイス証明書はコンピューターの個人用ストアにインストールされます。 デバイスの登録が完了すると、プロセスは MDM 登録に進みます。 |

## <a name="azure-ad-joined-in-federated-environments"></a>フェデレーション環境での Azure AD 参加

:::image type="content" source="media/device-registration-how-it-works/device-registration-azure-ad-federated.png" alt-text="フェデレーション環境での Azure AD 参加デバイス フロー" lightbox="media/device-registration-how-it-works/device-registration-azure-ad-federated.png":::

| フェーズ | 説明 |
| :----: | :----------- |
| A | Azure AD 参加デバイスの登録の最も一般的な方法は、OOBE (Out-Of-Box Experience) 中に、クラウド エクスペリエンス ホスト (CXH) アプリケーションに Azure AD 参加 Web アプリケーションを読み込む方法です。 承認エンドポイントを検出するために、アプリケーションから Azure AD OpenID 構成エンドポイントに GET 要求が送信されます。 承認エンドポイントを含む OpenID 構成が、JSON ドキュメントとして Azure AD からアプリケーションに返されます。 |
| B | アプリケーションで承認エンドポイント用のサインイン要求が作成され、ユーザーの資格情報が収集されます。 |
| C | ユーザーがユーザー名を (UPN 形式で) 指定すると、アプリケーションはユーザーの対応する領域情報を検出するために GET 要求を Azure AD に送信します。 この情報により、環境がマネージドまたはフェデレーションのいずれであるかが判明します。 Azure AD は、情報を JSON オブジェクトで返します。 アプリケーションでは、環境がフェデレーションであると判断されます。<br><br>アプリケーションは、返された JSON 領域オブジェクトの AuthURL 値 (オンプレミスの STS サインイン ページ) にリダイレクトします。 アプリケーションでは、STS Web ページにより資格情報が収集されます。 |
| D | アプリケーションは、資格情報をオンプレミスの STS に POST します。この場合、追加の認証要素が必要な場合があります。 オンプレミスの STS でユーザーが認証され、トークンが返されます。 アプリケーションは、認証のためにトークンを Azure AD に POST します。 Azure AD でトークンが検証され、要求を含む ID トークンが返されます。 |
| E | アプリケーションは、MDM の使用条件 (mdm_tou_url 要求) を検索します。 存在する場合、アプリケーションは要求の値から使用条件を取得して内容をユーザーに提示します。ユーザーが使用条件に同意するまで待機します。 この手順は省略可能であり、要求が存在しない場合、または要求値が空の場合はスキップされます。 |
| F | アプリケーションは、デバイス登録検出要求を Azure Device Registration Service (ADRS) に送信します。 Azure DRS は、デバイス登録を完了するためのテナント固有の URI を返す探索データ ドキュメントを返します。 |
| G | アプリケーションでは、デバイス キー (dkpub/dkpriv) と呼ばれる TPM にバインドされた (推奨) RSA 2048 ビットのキー ペアが作成されます。 アプリケーションは、dkpub と公開キーを使用して証明書要求を作成し、dkpriv を使用して証明書要求に署名します。 次に、アプリケーションでは TPM のストレージ ルート キーから 2 番目のキー ペアが派生されます。 このキーはトランスポート キー (tkpub/tkpriv) です。 |
| H | アプリケーションは、ID トークン、証明書要求、tkpub、構成証明データを含むデバイス登録要求を Azure DRS に送信します。 Azure DRS では、ID トークンが検証され、デバイス ID が作成され、含まれている証明書要求に基づいて証明書が作成されます。 その後、Azure DRS はデバイス オブジェクトを Azure AD に書き込み、デバイス ID とデバイス証明書をクライアントに送信します。 |
| I | Azure DRS からデバイス ID とデバイス証明書を受け取ることによってデバイス登録が完了します。 デバイス ID は将来の参照用に保存され (`dsregcmd.exe /status` から参照できます)、デバイス証明書はコンピューターの個人用ストアにインストールされます。 デバイスの登録が完了すると、プロセスは MDM 登録に進みます。 |

## <a name="hybrid-azure-ad-joined-in-managed-environments"></a>マネージド環境での Hybrid Azure AD 参加

:::image type="content" source="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-managed.png" alt-text="マネージド環境での Hybrid Azure AD 参加デバイス フロー" lightbox="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-managed.png":::

| フェーズ | 説明 |
| :----: | ----------- |
| A | ユーザーはドメイン資格情報を使用して、ドメイン参加済みの Windows 10 コンピューターにサインインします。 この資格情報には、ユーザー名とパスワード、またはスマート カード認証を使用できます。 ユーザー サインインによって、デバイスの自動参加タスクがトリガーされます。 デバイスの自動参加タスクはドメイン参加時にトリガーされ、1 時間ごとに再試行されます。 これはユーザー サインインのみには依存しません。 |
| B | タスクは、Active Directory の構成パーティションに格納されているサービス接続ポイントのキーワード属性に対して LDAP プロトコルを使用して Active Directory にクエリを実行します (`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=corp,DC=contoso,DC=com`)。 キーワード属性で返される値によって、デバイス登録の宛先が Azure Device Registration Service (ADRS) かまたはオンプレミスでホストされているエンタープライズ デバイス登録サービスかが決定します。 |
| C | マネージド環境では、タスクにより自己署名証明書の形式で初期認証資格情報が作成されます。 このタスクでは、LDAP を使用して Active Directory の computer オブジェクトの userCertificate 属性に証明書を書き込みます。 |
| D | userCertificate 属性に証明書が含まれているコンピューターを表すデバイス オブジェクトが Azure AD に作成されるまで、コンピューターは Azure DRS に対して認証を行うことができません。 Azure AD Connect で、属性の変更が検出されます。 次の同期サイクルで、Azure AD Connect は userCcertificate、オブジェクト GUID、およびコンピューター SID を Azure DRS に送信します。 Azure DRS では、属性情報を使用して Azure AD にデバイス オブジェクトが作成されます。 |
| E | デバイスの自動参加タスクは、ユーザーのサインインごと、または 1 時間ごとにトリガーされ、userCertificate 属性内の公開キーの対応する秘密キーを使用して Azure AD でコンピューターの認証を試みます。 Azure AD ではコンピューターが認証され、コンピューターに ID トークンが発行されます。 |
| F | タスクでは、デバイス キー (dkpub/dkpriv) と呼ばれる TPM にバインドされた (推奨) RSA 2048 ビットのキー ペアが作成されます。 アプリケーションは、dkpub と公開キーを使用して証明書要求を作成し、dkpriv を使用して証明書要求に署名します。 次に、アプリケーションでは TPM のストレージ ルート キーから 2 番目のキー ペアが派生されます。 このキーはトランスポート キー (tkpub/tkpriv) です。 |
| G | タスクでは、ID トークン、証明書要求、tkpub、構成証明データを含むデバイス登録要求が Azure DRS に送信されます。 Azure DRS では、ID トークンが検証され、デバイス ID が作成され、含まれている証明書要求に基づいて証明書が作成されます。 その後、Azure DRS により Azure AD のデバイス オブジェクトが更新され、デバイス ID とデバイス証明書がクライアントに送信されます。 |
| H | Azure DRS からデバイス ID とデバイス証明書を受け取ることによってデバイス登録が完了します。 デバイス ID は将来の参照用に保存され (`dsregcmd.exe /status` から参照できます)、デバイス証明書はコンピューターの個人用ストアにインストールされます。 デバイスの登録が完了すると、タスクは終了します。 |

## <a name="hybrid-azure-ad-joined-in-federated-environments"></a>フェデレーション環境での Hybrid Azure AD 参加

:::image type="content" source="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-federated.png" alt-text="マネージド環境での Hybrid Azure AD 参加デバイス フロー" lightbox="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-federated.png":::

| フェーズ | 説明 |
| :----: | :----------- |
| A | ユーザーはドメイン資格情報を使用して、ドメイン参加済みの Windows 10 コンピューターにサインインします。 この資格情報には、ユーザー名とパスワード、またはスマート カード認証を使用できます。 ユーザー サインインによって、デバイスの自動参加タスクがトリガーされます。 デバイスの自動参加タスクはドメイン参加時にトリガーされ、1 時間ごとに再試行されます。 これはユーザー サインインのみには依存しません。 |
| B | タスクは、Active Directory の構成パーティションに格納されているサービス接続ポイントのキーワード属性に対して LDAP プロトコルを使用して Active Directory にクエリを実行します (`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=corp,DC=contoso,DC=com`)。 キーワード属性で返される値によって、デバイス登録の宛先が Azure Device Registration Service (ADRS) かまたはオンプレミスでホストされているエンタープライズ デバイス登録サービスかが決定します。 |
| C | フェデレーション環境の場合、コンピューターでは Windows 統合認証を使用してエンタープライズ デバイス登録エンドポイントが認証されます。 エンタープライズ デバイス登録サービスは、オブジェクト GUID、コンピューター SID、およびドメイン参加状態の要求を含むトークンを作成して返します。 タスクはトークンと要求を Azure AD に送信し、そこで検証されます。 Azure AD は実行中のタスクに ID トークンを返します。 |
| D | アプリケーションでは、デバイス キー (dkpub/dkpriv) と呼ばれる TPM にバインドされた (推奨) RSA 2048 ビットのキー ペアが作成されます。 アプリケーションは、dkpub と公開キーを使用して証明書要求を作成し、dkpriv を使用して証明書要求に署名します。 次に、アプリケーションでは TPM のストレージ ルート キーから 2 番目のキー ペアが派生されます。 このキーはトランスポート キー (tkpub/tkpriv) です。 |
| E | オンプレミスのフェデレーション アプリケーションに SSO を提供するために、タスクは、オンプレミス STS からエンタープライズ PRT を要求します。 Active Directory フェデレーション サービス (AD FS) ロールを実行する Windows Server 2016 で要求が検証され、実行中のタスクに返されます。 |
| F | タスクでは、ID トークン、証明書要求、tkpub、構成証明データを含むデバイス登録要求が Azure DRS に送信されます。 Azure DRS では、ID トークンが検証され、デバイス ID が作成され、含まれている証明書要求に基づいて証明書が作成されます。 その後、Azure DRS はデバイス オブジェクトを Azure AD に書き込み、デバイス ID とデバイス証明書をクライアントに送信します。 Azure DRS からデバイス ID とデバイス証明書を受け取ることによってデバイス登録が完了します。 デバイス ID は将来の参照用に保存され (`dsregcmd.exe /status` から参照できます)、デバイス証明書はコンピューターの個人用ストアにインストールされます。 デバイスの登録が完了すると、タスクは終了します。 |
| G | Azure AD Connect のデバイス ライトバックが有効になっている場合、Azure AD Connect では次の同期サイクルで Azure AD からの更新を要求します (証明書の信頼を使用するハイブリッド デプロイではデバイス ライトバックが必須です)。 Azure AD では、デバイス オブジェクトを一致する同期されたコンピューター オブジェクトに関連付けます。 Azure AD Connect は、オブジェクト GUID とコンピューター SID を含むデバイス オブジェクトを受信し、Active Directory にデバイス オブジェクトを書き込みます。 |

## <a name="next-steps"></a>次のステップ

- [Azure AD 参加済みデバイス](concept-azure-ad-join.md)
- [Azure AD 登録済みデバイス](concept-azure-ad-register.md)
- [ハイブリッド Azure AD 参加済みデバイス](concept-azure-ad-join-hybrid.md)
- [プライマリ更新トークンとは](concept-primary-refresh-token.md)
- [Azure AD Connect: デバイス オプション](../hybrid/how-to-connect-device-options.md)
