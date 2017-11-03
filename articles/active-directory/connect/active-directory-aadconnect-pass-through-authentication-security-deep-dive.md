---
title: "Azure Active Directory パススルー認証のセキュリティの詳細 | Microsoft Docs"
description: "この記事では、Azure Active Directory (Azure AD) パススルー認証でオンプレミス アカウントをどのように保護するかについて説明します。"
services: active-directory
keywords: "Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.openlocfilehash: a5feadd851b166d0a9a77bd1d124cdd599d5d701
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory パススルー認証のセキュリティの詳細

この記事では、パススルー認証のしくみについてより詳しく説明します。 ここでは、機能のセキュリティ面について重点的に説明します。 このトピックは、セキュリティおよび IT 管理者、コンプライアンスおよびセキュリティの最高責任者、および中小規模の組織または大企業で IT セキュリティとコンプライアンスを担当するその他の IT プロフェッショナルを対象としています。

ここで扱うトピックは以下のとおりです。
- 認証エージェントのインストールおよび登録方法に関する詳細な技術情報。
- ユーザー サインイン時のパスワードの暗号化に関する詳細な技術情報。
- オンプレミスの認証エージェントと Azure Active Directory (Azure AD) 間のチャネルのセキュリティ。
- 認証エージェントの運用上のセキュリティをどのように保持するかについての詳細な技術情報。
- その他のセキュリティ関連のトピック。

## <a name="key-security-capabilities"></a>キー セキュリティ機能

ここでは、この機能のキー セキュリティ面について説明します。
- テナント間でのサインイン要求を分離する、セキュリティで保護されたマルチテナント アーキテクチャ上に構築されています。
- オンプレミス パスワードが何らかの形でクラウドに保存されることはありません。
- パスワード検証要求のリッスンおよび応答を行う、オンプレミスの認証エージェントは、ネットワーク内からの送信接続のみを行います。 境界ネットワーク (DMZ) でこれらの認証エージェントをインストールする必要はありません。
- 認証エージェントから Azure AD への送信通信で使用されるのは、標準ポート (80 と 443) のみです。 受信ポートがファイアウォールで開かれている必要はありません。 
  - 認証済みのすべての送信通信ではポート 443 が使用されます。
  - ポート 80 が使用されるのは、機能で使用されるいずれの証明書も失効していないことを確認するために、証明書失効リスト (CRL) をダウンロードする場合のみです。
  - ネットワーク要件の完全な一覧については、[こちら](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites)を参照してください。
- ユーザーがサインイン時に指定するパスワードは、Active Directory に対する検証のためにオンプレミスの認証エージェントに受け入れられる前に、クラウドで暗号化されます。
- Azure AD とオンプレミスの認証エージェント間の HTTPS チャネルは、相互認証によって保護されます。
- 条件付きアクセス ポリシー (多要素認証を含む)、Identity Protection、およびスマート ロックアウトなどの、Azure AD のクラウド保護機能とシームレスに統合されます。

## <a name="components-involved"></a>関連するコンポーネント

Azure AD の運用、サービスおよびデータのセキュリティに関する全般の詳細については、[セキュリティ センター](https://azure.microsoft.com/support/trust-center/)を参照してください。 ユーザー サインインにパススルー認証が使用される場合、次のコンポーネントが関連します。
- **Azure AD STS**: ステートレスなセキュリティ トークン サービス (STS)。サインイン要求を処理し、ユーザーのブラウザー、クライアント、または必要に応じてサービスにセキュリティ トークンを発行します。
- **Azure Service Bus**: エンタープライズ メッセージングと中継通信を使用するクラウド対応通信を提供し、オンプレミスのソリューションをクラウドに接続するのに役立ちます。
- **Azure AD Connect 認証エージェント (認証エージェント)**: パスワード検証要求のリッスンと応答を行うオンプレミス コンポーネント。
- **Azure SQL Database**: メタデータや暗号化キーを含む、テナントの認証エージェントに関する情報を保持します。
- **Active Directory (AD)**: ユーザー アカウント (およびそのパスワード) が格納される、オンプレミスの Active Directory。

## <a name="installation-and-registration-of-authentication-agents"></a>認証エージェントのインストールと登録

認証エージェントは、[Azure AD Connect を使用してパススルー認証を有効にした](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)場合、または[サインイン要求の高可用性を確保するために認証エージェントをさらに追加した](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability)場合にインストールされ、Azure AD に登録されます。 認証エージェントを機能させるには、次の 3 つのフェーズが必要です。

- 認証エージェントのインストール
- 認証エージェントの登録
- 認証エージェントの初期化

これらの各フェーズについては、以下のトピックで詳しく説明します。

### <a name="authentication-agent-installation"></a>認証エージェントのインストール

オンプレミス サーバーに (Azure AD Connect またはスタンドアロンを使用して) 認証エージェントをインストールできるのは、全体管理者のみです。 インストールでは、以下の 2 つの新しい項目が、**コントロール パネルの [プログラム] の [プログラムと機能]** 一覧に追加されます。
- 認証エージェント アプリケーション自体。 これは、[ネットワーク サービス](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx)特権で実行されます。
- 認証エージェントの自動更新で使用されるアップデーター アプリケーション。 これは、[ローカル システム](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)特権で実行されます。


### <a name="authentication-agent-registration"></a>認証エージェントの登録

認証エージェントをインストールしたら、それ自体を Azure AD に登録する必要があります。 Azure AD は、Azure AD とのセキュリティで保護された通信で使用できる一意のデジタル ID 証明書を各認証エージェントに割り当てることでこれを行います。

登録手順では、認証エージェントとテナントとのバインドも行います。これにより、Azure AD は、この特定の認証エージェントのみが、テナントのパスワード検証要求を処理する権限があることを認識します。 この手順は、登録する新しい認証エージェントごとに繰り返されます。

以下に、Azure AD に認証エージェント自体をどのように登録するかを示します。

![エージェントの登録](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD は、まず、全体管理者に自分の資格情報で Azure AD にサインインするよう要求します。 サインイン時に、認証エージェントは、全体管理者のために使用可能なアクセス トークンを取得します。
2. 次に、認証エージェントはキーのペア (公開キーと秘密キー) を生成します。
    - このキーのペアは、標準の **RSA 2048 ビット**の暗号化を使用して生成されます。
    - 秘密キーは、認証エージェントがインストールされているオンプレミス サーバーの外部に移動されることはありません。
3.  認証エージェントは要求に含まれる以下のコンポーネントを使用して、HTTPS 経由で Azure AD に "登録" 要求を行います。
    - 手順 1. で取得したアクセス トークン。
    - 手順 2. で生成した公開キー。
    - **証明書署名要求** (CSR または証明書要求)。 これは、証明機関として Azure AD を使用して、デジタル ID 証明書を申請するためのものです。
4. Azure AD は登録要求のアクセス トークンを検証し、要求が全体管理者からのものであることを確認します。
5. 次に、Azure AD はデジタル ID 証明書に署名して、認証エージェントに戻します。
    - 証明書への署名は、**Azure AD のルート証明機関 (CA)** を使用して行われます。 この CA は Windows の**信頼されたルート証明機関**ストアには_ない_ことに注意してください。
    - この CA はパススルー認証機能でのみ使用されます。 認証エージェントの登録の際の CSR の署名のためだけに使用されます。
    - この CA は、Azure AD の他のサービスでは使用されません。
    - 証明書の件名 (**識別名または DN**) は**テナント ID** に設定されます。 これは、テナントを一意に識別する GUID です。 これにより、証明書の範囲がテナントのみでの使用に限定されます。
6. Azure AD は、Azure AD のみがアクセス可能な、Azure SQL Database に認証エージェントの公開キーを格納します。
7. 証明書 (手順 5. で発行されたもの) はオンプレミス サーバーの **Windows 証明書ストア** (**[CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE)** の場所など) に格納され、認証エージェントとアップデーター アプリケーションの両方で使用されます。

### <a name="authentication-agent-initialization"></a>認証エージェントの初期化

認証エージェントの開始時 (登録後またはサーバーの再起動後、初めて開始されたとき) に、Azure AD サービスと安全に通信し、パスワード検証要求の受け入れを開始する方法が必要になります。

![エージェントの初期化](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

認証エージェントを初期化する方法を以下に示します。



1. 認証エージェントは、まず、Azure AD に送信 "ブートストラップ" 要求を行います。 
    - このブートストラップ要求はポート 443 を介して行われ、(認証エージェントの登録時に発行されるものと同じ証明書を使用して) 相互認証された HTTPS チャネルを介します。
2. Azure AD は、(テナント ID で識別される) テナントに固有の Azure Service Bus キューに**アクセス キー**を提供することで、ブートストラップ要求に応答します。
3. 認証エージェントは、キューへの (ポート 443 を介した) 永続的な送信 HTTPS 接続を行います。 
    - これで、パスワード検証要求を取得して処理する準備ができました。

テナントに複数の認証エージェントが登録されている場合は、初期化の手順で、それぞれが同じ Azure Service Bus キューに接続されていることが確認されます。 

## <a name="processing-sign-in-requests"></a>サインイン要求の処理 

次の図は、パススルー認証でユーザー サインイン要求がどのように処理されるかを示しています。

![サインインの処理](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

パススルー認証では、次のようにユーザーのサインイン要求が処理されます。 

1. ユーザーが、([https://outlook.office365.com/owa](https://outlook.office365.com/owa) の Outlook Web App などの) アプリケーションへのアクセスを試みます。
2. ユーザーがまだサインインしていない場合は、アプリケーションでブラウザーが Azure AD のサインイン ページにリダイレクトされます。
3. Azure AD STS サービスは、ユーザー サインイン ページで応答します。
4. ユーザーが Azure AD のサインイン ページにユーザー名とパスワードを入力し、[サインイン] ボタンをクリックします。
5. ユーザー名とパスワードは、HTTPS POST 要求で Azure AD STS に送信されます。
6. Azure AD STS は、Azure SQL Database からテナントで登録されたすべての認証エージェントの公開キーを取得し、それらを使用してパスワードを暗号化します。 
    - テナントで登録された ‘N’ 個の認証エージェントに対して、‘N’ 個の暗号化されたパスワード値が生成されます。
7. Azure AD STS は、テナントに固有の Azure Service Bus キューにパスワード検証要求 (ユーザー名と暗号化されたパスワードの値) を配置します。
8. 初期化された認証エージェントは Azure Service Bus キューに永続的に接続されているため、使用可能な認証エージェントのいずれかでパスワード検証要求が取得されます。
9. 認証エージェントは、(ID を使用して) 公開キーに固有の暗号化されたパスワード値を見つけ、その秘密キーを使用して暗号化します。
10. 認証エージェントは、**[Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)** (**dwLogonType** パラメーターは **LOGON32_LOGON_NETWORK** に設定) を使用して、オンプレミスの Active Directory に対するユーザー名とパスワードの検証を試みます。 
    - これは、フェデレーション サインイン シナリオでユーザーのサインインで Active Directory フェデレーション サービス (AD FS) によって使用されるものと同じ API です。
    - Windows Server の標準的な解決プロセスに従ってドメイン コントローラーを検索します。
11. 認証エージェントは Active Directory から結果 (成功、ユーザー名またはパスワードが正しくない、パスワードの期限が切れている、ユーザーがロックアウトされているなど) を受け取ります。
12. 認証エージェントは、ポート 443 を介して相互認証された送信 HTTPS チャネル経由で Azure AD STS に結果を戻します。 相互認証では、登録時に認証エージェントに以前に発行されたものと同じ証明書を使用します。
13. Azure AD STS は、この結果がテナントの特定のサインイン要求と関連していることを確認します。
14. Azure AD STS は、構成どおりにサインインの手順を続行します。 たとえば、パスワードの検証が成功した場合、ユーザーは Multi-Factor Authentication のためにチャレンジされるか、アプリケーションにリダイレクトされることがあります。

## <a name="operational-security-of-authentication-agents"></a>認証エージェントの運用上のセキュリティ

パススルー認証で運用上のセキュリティが保持されるように、Azure AD は定期的にその証明書を更新します。 これらの更新は Azure AD からトリガーされるものであり、認証エージェント自体では制御されません。

![運用上のセキュリティ](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

認証エージェントが Azure AD との信頼関係を更新する方法を以下に示します。

1. 認証エージェントは定期的 (数時間ごと) に Azure AD を ping して、その証明書を更新する時期であるかどうかを確認します。 
    - この確認は、(登録時に発行されたものと同じ証明書を使用して) 相互認証された HTTPS チャネル経由で行われます。
2. サービスが更新の時期であることを示した場合、認証エージェントは新しいキーのペア (公開キーと秘密キー) を生成します。
    - これらのキーは、標準の **RSA 2048 ビット**の暗号化を使用して生成されます。
    - 秘密キーはオンプレミス サーバーの外部に移動されることはありません。
3. 次に、認証エージェントは、要求に含まれる以下のコンポーネントを使用して、HTTPS 経由で Azure AD に "証明書の更新" 要求を行います。
    - 既存の証明書 (Windows 証明書ストアの **CERT_SYSTEM_STORE_LOCAL_MACHINE** の場所から取得したもの)。 全体管理者はこの手順に関与しないため、全体管理者のためのアクセス トークンは必要ありません。
    - 手順 2. で生成した公開キー。
    - **証明書署名要求** (CSR または証明書要求)。 これは、証明機関として Azure AD を使用して、新しいデジタル ID 証明書を申請するためのものです。
4. Azure AD は、証明書の更新要求の既存の証明書を検証します。 次に、要求がテナントに登録されている認証エージェントからのものであることを確認します。
5. 既存の証明書がまだ有効である場合、Azure AD は新しいデジタル ID 証明書に署名し、新しい証明書を認証エージェントに戻します。 
6. 既存の証明書の有効期限が切れている場合、Azure AD は登録されている認証エージェントのテナントの一覧から認証エージェントを削除します。 その後、全体管理者は手動で新しい認証エージェントをインストールして登録する必要があります。
    - 証明書への署名は、**Azure AD のルート証明機関 (CA)** を使用して行われます。
    - 証明書の件名 (**識別名または DN**) は、テナントを一意に識別する GUID である**テナント ID** に設定されます。 つまり、証明書の範囲はテナントのみに限定されます。
6. Azure AD は、Azure AD のみがアクセス可能な、Azure SQL Database に認証エージェントの "新しい" 公開キーを格納します。 また、認証エージェントに関連付けられている "古い" 公開キーを無効にします。
7. その後、新しい証明書 (手順 5. で発行されたもの) はサーバーの **Windows 証明書ストア** (**[CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER)** の場所など) に格納されます。
    - 信頼関係の更新手順は (全体管理者の関与なしで) 非対話形式に行われるため、認証エージェントは **CERT_SYSTEM_STORE_LOCAL_MACHINE** の場所の既存の証明書を更新するためにアクセスできなくなります。 この手順の実行中に、**CERT_SYSTEM_STORE_LOCAL_MACHINE** の場所の証明書自体は削除されないことに注意してください。
8. 新しい証明書は、この時点から認証に使用されます。 証明書の以降の更新のたびに、**CERT_SYSTEM_STORE_LOCAL_MACHINE** の場所の証明書が置き換えられます。

## <a name="auto-update-of-authentication-agents"></a>認証エージェントの自動更新

アップデーター アプリケーションは、新しいバージョンがリリースされたときに自動的に認証エージェントを更新します。 テナントのパスワード検証要求は処理されません。 

Azure AD は、署名済みの **Windows インストーラー パッケージ (MSI)** として、新しいバージョンのソフトウェアをホストします。 MSI への署名は、ダイジェスト アルゴリズムとして **SHA256** を指定し、[Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) を使用して行われます。 

![自動更新](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

認証エージェントがどのように自動更新されるかを以下に示します。

1. アップデーターは定期的 (1 時間ごと) に Azure AD を ping して、使用可能な新しいバージョンの認証エージェントがあるかどうかを確認します。 
    - この確認は、(登録時に発行されたものと同じ証明書を使用して) 相互認証された HTTPS チャネル経由で行われます。 認証エージェントとアップデーターは、サーバーに格納されている証明書を共有します。
2. 新しいバージョンが使用可能な場合、Azure AD はアップデーターに署名済みの MSI を戻します。
3. アップデーターは、MSI が Microsoft によって署名されていることを確認します。
4. アップデーターは MSI を実行します。 この操作では次の手順を実行します (アップデーターは[ローカル システム](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)特権で実行されることに注意してください)。
    - 認証エージェント サービスを停止します。
    - サーバーに新しいバージョンの認証エージェントをインストールします。
    - 認証エージェント サービスを再起動します。

>[!NOTE]
>テナントに複数の認証エージェントが登録されている場合、Azure AD がそれらの証明書を同時に更新することはありません。 代わりに Azure AD は段階的に更新し、サインイン要求の高可用性を確保します。


## <a name="next-steps"></a>次のステップ
- [**現在の制限**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - サポートされているシナリオと、サポートされていないシナリオを確認します。
- [**クイック スタート**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - Azure AD パススルー認証を起動および実行します。
- [**スマート ロックアウト**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) - ユーザー アカウントを保護するようにテナントのスマート ロックアウト機能を構成します。
- [**しくみ**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -Azure AD パススルー認証のしくみの基礎を確認します。
- [**よく寄せられる質問**](active-directory-aadconnect-pass-through-authentication-faq.md) - よく寄せられる質問と回答です。
- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 機能に関する一般的な問題を解決する方法を確認します。
- [**Azure AD シームレス SSO**](active-directory-aadconnect-sso.md) - この補完的な機能の詳細を確認します。

