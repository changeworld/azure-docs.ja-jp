---
title: Azure AD パスワード保護のプレビューをデプロイする
description: Azure AD パスワード保護のプレビューを展開して、オンプレミスでの間違ったパスワードの使用を禁止します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 824bedf782d6d227f2fa3adcf52492bb5a3eb478
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696865"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>更新:Azure AD のパスワード保護をデプロイする

|     |
| --- |
| Azure AD パスワード保護は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

[Windows Server Active Directory に対して Azure AD パスワード保護を適用する方法](concept-password-ban-bad-on-premises.md)が理解できたので、次の手順は、その展開を計画して実行することです。

## <a name="deployment-strategy"></a>展開戦略

Microsoft では、すべての展開を監査モードで開始することをお勧めしています。 監査モードは既定の初期設定であり、パスワードの設定は継続でき、ブロックされた場合は、イベント ログにエントリが作成されます。 プロキシ サーバーと DC エージェントが監査モードで完全に配置されたら、パスワード ポリシーが適用された場合にその適用がユーザーと環境にどのような影響を与えるかを見極めるために、通常の監視を実行する必要があります。

この監査段階中に、多くの組織は、以下に気付きます。

* より安全なパスワードを使用するように存の運用プロセスを改善する必要がある。
* ユーザーは、安全でないパスワードを定期的に選択することに慣れている。
* セキュリティの適用の今後の変更と、それによってもたらされる可能性がある影響をユーザーに通知し、より安全なパスワードを選択できる方法をユーザーが理解するための手助けをする必要がある。

この機能を妥当な時間にわたって監査モードで実行した後、適用構成を **[監査]** から **[適用]** に変更することで、より安全なパスワードを要求できます。 この期間中に、集中的な監視を行うことをお勧めします。

## <a name="deployment-requirements"></a>デプロイ要件

* Azure AD パスワード保護 DC エージェントがインストールされているドメイン コントローラーではすべて、Windows Server 2012 以降を実行している必要があります。
* Azure AD パスワード保護プロキシ サービスがインストールされているマシンではすべて、Windows Server 2012 R2 以降を実行している必要があります。
* ドメイン コントローラーを含む Azure AD パスワード保護コンポーネントがインストールされているすべてのマシンに、ユニバーサル C ランタイムがインストールされている必要があります。
これは、Windows Update 経由でマシンに対する修正プログラムの適用を完全に行うことで実現することをお勧めします。 それ以外の場合は、適切な OS 固有の更新パッケージをインストールできます。「[Windows での汎用の C ランタイムの更新プログラム](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)」を参照してください。
* 各ドメイン内の少なくとも 1 つのドメイン コントローラーと、Azure AD パスワード保護プロキシ サービスをホストする少なくとも 1 つのサーバーとの間に、ネットワーク接続が存在する必要があります。 この接続では、ドメイン コントローラーがプロキシ サービス上の RPC エンドポイント マッパー (135) および RPC サーバー ポートにアクセスできるようにする必要があります。 RPC サーバー ポートは、既定では動的 RPC ポートですが、静的ポートを使用するように構成することができます (下記参照)。
* Azure AD のパスワード保護プロキシ サービスをホストしているすべてのマシンに、次のエンドポイントへのネットワーク アクセスが必要です。

    |エンドポイント |目的|
    | --- | --- |
    |`https://login.microsoftonline.com`|認証要求|
    |`https://enterpriseregistration.windows.net`|Azure AD パスワード保護機能|

* Azure AD で Azure AD パスワード保護プロキシ サービスとフォレストを登録するグローバル管理者アカウント。
* Azure AD で Windows Server Active Directory フォレストを登録するために、フォレスト ルート ドメインの Active Directory ドメイン管理者特権を持つアカウント。
* DC エージェント サービス ソフトウェアを実行しているすべての Active Directory ドメインは、sysvol レプリケーションに DFSR を使用する必要があります。

## <a name="single-forest-deployment"></a>1 つのフォレストへの展開

次の図は、Azure AD パスワード保護の基本コンポーネントが、オンプレミスの Active Directory 環境でどのように連携し、機能するかを示したものです。  

![Azure AD パスワード保護コンポーネントの連携方法](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>ソフトウェアをダウンロードする

Azure AD パスワード保護のために必要なインストーラーが 2 つあり、これらは [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=57071)からダウンロードできます

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Azure AD パスワード保護プロキシ サービスをインストールして構成する

1. Azure AD パスワード保護プロキシ サービスをホストする 1 つまたは複数のサーバーを選択します。
   * 各サービスは 1 つのフォレストにのみパスワード ポリシーを提供でき、ホスト コンピューターは、フォレスト内のドメインに対してドメイン参加済みである (ルート ドメインと子ドメインがどちらもサポートされている) 必要があります。 Azure AD パスワード保護プロキシ サービスがその機能を発揮するには、フォレストの各ドメイン内の少なくとも 1 つの DC と Azure AD パスワード保護プロキシ コンピューターとの間にネットワーク接続が存在する必要があります。
   * Azure AD パスワード保護プロキシ サービスをテスト目的でドメイン コントローラーにインストールし、実行することはサポートされています。ただし、ドメイン コントローラーにインターネット接続が必要となるため、セキュリティ上の懸念事項になります。 この構成はテスト目的にのみ使用することをお勧めします。
   * 冗長性を確保するには、少なくとも 2 つのプロキシ サーバーを使用することをお勧めします。 [高可用性に関する記事をご覧ください](howto-password-ban-bad-on-premises-deploy.md#high-availability)

2. AzureADPasswordProtectionProxySetup.msi MSI パッケージを使用して、Azure AD パスワード保護プロキシ サービスをインストールします。
   * このソフトウェアのインストールでは、再起動は必要ありません。 ソフトウェアのインストールは、標準 MSI プロシージャ (例: `msiexec.exe /i AzureADPasswordProtectionProxySetup.msi /quiet /qn`) を使用して自動化できます。

      > [!NOTE]
      > Windows Firewall サービスは必ず、AzureADPasswordProtectionProxySetup.msi の MSI パッケージをインストールする前に実行されている必要があります。そうでない場合は、インストール エラーが発生します。 Windows Firewall が実行されないように構成されている場合は、回避策として、インストール プロセスの間、一時的に Windows Firewall サービスを有効化して開始します。 インストール後は、プロキシ ソフトウェアでは Windows Firewall 上に特定の依存関係を保持しません。 サードパーティのファイアウォールを使用している場合は、やはりデプロイの要件を満たすように構成する必要があります (動的または静的を問わず、ポート 135 とプロキシ RPC サーバー ポートへの受信アクセスを許可します)。 「[デプロイ要件](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)」を確認してください。

3. 管理者として PowerShell ウィンドウを開きます。
   * Azure AD パスワード保護プロキシ ソフトウェアには、AzureADPasswordProtection という名前の新しい PowerShell モジュールが含まれています。 次の手順は、この PowerShell モジュールのさまざまなコマンドレットの実行に基づいており、新しい PowerShell ウィンドウを開いて、新しいモジュールを次のようにインポートしていることを前提としています。

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * 次の PowerShell コマンドを使用して、サービスが実行されていることを確認します。`Get-Service AzureADPasswordProtectionProxy | fl`
     [実行中] という**状態**を返す結果が生成されます。

4. プロキシを登録します。
   * 手順 3 が完了すると、Azure AD パスワード保護プロキシ サービスがコンピューター上で実行されますが、Azure AD と通信するために必要な資格情報がまだ用意されていません。 `Register-AzureADPasswordProtectionProxy` PowerShell コマンドレットを使用してその機能を有効にするには、Azure AD の登録が必要です。 このコマンドレットは、Azure テナントのグローバル管理者資格情報と、フォレストのルート ドメイン内にオンプレミスの Active Directory ドメインの管理者特権を必要とします。 `Register-AzureADPasswordProtectionProxy` の呼び出しは、特定のプロキシ サービスで成功すれば、次回以降の呼び出しも成功しますが、これ以上の呼び出しは必要ありません。

      Register-AzureADPasswordProtectionProxy コマンドレットでは、次の 3 つの認証モードがサポートされています。

      * 対話型認証モード:

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > このモードは、Server Core オペレーティング システムでは機能しません。 代わりに、以下に示すいずれかの代替認証モードを使用してください。

         > [!NOTE]
         > Internet Explorer の強化されたセキュリティ構成が有効になっている場合、このモードは失敗する可能性があります。 回避策として、IESC を無効にし、プロキシを登録して、もう一度 IESC を有効にします。

      * デバイスコード認証モード:

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         この後、別のデバイスに表示された手順に従って認証を完了することができます。

      * サイレント (パスワードベース) 認証モード:

         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > このモードは、何らかの理由で認証に MFA が必要となる場合には失敗します。 その場合は、上記 2 つのモードのいずれか使用して、MFA ベースの認証を完了してください。

      現時点では、今後提供される機能用に予約された ForestCredential パラメーターを指定する必要はありません。

   > [!NOTE]
   > Azure AD パスワード保護プロキシ サービスの登録は、サービスの有効期間中に 1 回限り実行される手順であると想定されています。 この時点以降、他の必要なメンテナンスは、プロキシ サービスによって自動的に実行されます。 'Register-AzureADPasswordProtectionProxy' の呼び出しは、特定のプロキシで成功すれば、次回以降の呼び出しも成功しますが、これ以上の呼び出しは必要ありません。

   > [!TIP]
   > 特定の Azure テナントに対するこのコマンドレットの初回の実行では、コマンドレットの実行が完了するまで、かなりの遅延 (数十秒) が発生することがあります。 エラーが報告される場合を除き、この遅延について心配する必要はありません。

5. フォレストを登録します。
   * `Register-AzureADPasswordProtectionForest` PowerShell コマンドレットを使用して、Azure と通信するために必要な資格情報で、オンプレミスの Active Directory フォレストを初期化する必要があります。 このコマンドレットは、Azure テナントのグローバル管理者資格情報と、フォレストのルート ドメイン内にオンプレミスの Active Directory ドメインの管理者特権を必要とします。 この手順は、フォレストごとに 1 回実行されます。

      Register-AzureADPasswordProtectionForest コマンドレットでは、次の 3 つの認証モードがサポートされています。

      * 対話型認証モード:

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > このモードは、Server Core オペレーティング システムでは機能しません。 代わりに、以下に示すいずれかの代替認証モードを使用してください。

         > [!NOTE]
         > Internet Explorer の強化されたセキュリティ構成が有効になっている場合、このモードは失敗する可能性があります。 回避策として、IESC を無効にし、プロキシを登録して、もう一度 IESC を有効にします。  

      * デバイスコード認証モード:

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         この後、別のデバイスに表示された手順に従って認証を完了することができます。

      * サイレント (パスワードベース) 認証モード:
         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > このモードは、認証に MFA が必要となる場合には失敗します。 その場合は、上記 2 つのモードのいずれか使用して、MFA ベースの認証を完了してください。

      上記の例は、現在ログインしているユーザーがルート ドメインの Active Directory ドメイン管理者でもある場合にのみ正常に機能します。 これに該当しない場合は、-ForestCredential パラメーターを使用して、代替のドメイン資格情報を指定できます。

   > [!NOTE]
   > 環境に複数のプロキシ サーバーがインストールされている場合、フォレストの登録にどのプロキシ サーバーが使用されても問題はありません。

   > [!TIP]
   > 特定の Azure テナントに対するこのコマンドレットの初回の実行では、コマンドレットの実行が完了するまで、かなりの遅延 (数十秒) が発生することがあります。 エラーが報告される場合を除き、この遅延について心配する必要はありません。

   > [!NOTE]
   > Active Directory フォレストの登録は、フォレストの有効期間中に 1 回のみ実行される手順であると想定されています。 この時点以降、他の必要なメンテナンスは、フォレストで実行されるドメイン コントローラー エージェントによって自動的に実行されます。 `Register-AzureADPasswordProtectionForest` の呼び出しは、特定のフォレストで成功すれば、次回以降の呼び出しも成功しますが、これ以上の呼び出しは必要ありません。

   > [!NOTE]
   > `Register-AzureADPasswordProtectionForest` を少なくとも 1 台の Windows Server 2012 以降で成功させるには、プロキシ サーバーのドメイン内でドメイン コントローラーを使用できる必要があります。 ただし、この手順に先立って、任意のドメイン コントローラーに DC エージェント ソフトウェアをインストールするという要件はありません。

6. HTTP プロキシを通じて通信するように Azure AD パスワード保護プロキシ サービスを構成します

   ご使用の環境で、Azure との通信に特定の HTTP プロキシを使用する必要がある場合は、次の方法で対処できます。

   `%ProgramFiles%\Azure AD Password Protection Proxy\Service` フォルダー内に `proxyservice.exe.config` というファイルを作成し、内容を次のようにします。

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   HTTP プロキシで認証が必要な場合は、次のように useDefaultCredentials タグを追加します。

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   いずれの場合も、`http://yourhttpproxy.com:8080` を特定の HTTP プロキシ サーバーのアドレスとポートに置き換えます。

   HTTP プロキシが承認ポリシーを使用して構成されている場合は、Azure AD パスワード保護プロキシ サービスをホストしているコンピューターの Active Directory コンピューター アカウントに対するアクセス許可を取得する必要があります。

   `proxyservice.exe.config` ファイルを作成または更新した後には、Azure AD パスワード保護プロキシ サービスを停止して再起動する必要があります。

   Azure AD パスワード保護プロキシ サービスでは、HTTP プロキシへの接続に特定の資格情報を使用することはサポートされていません。

7. 省略可能:特定のポートでリッスンするように Azure AD パスワード保護プロキシ サービスを構成します。
   * Azure AD パスワード保護プロキシ サービスと通信するために、ドメイン コントローラー上の Azure AD パスワード保護 DC エージェント ソフトウェアによって、TCP 経由で RPC が使用されます。 既定では、Azure AD パスワード保護プロキシ サービスは、使用可能な動的 RPC エンドポイントでリッスンします。 ネットワーク トポロジまたはファイアウォールの要件応じて、必要であれば、特定の TCP ポートでリッスンするようにサービスを構成できます。
      * 静的ポートで実行するようにサービスを構成するには、`Set-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用します。
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > これらの変更を有効にするには、サービスを停止して、再起動する必要があります。

      * 動的ポートで実行するようにサービスを構成するには、同じ手順を使用しますが、次のように StaticPort の設定を 0 に戻します。
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > これらの変更を有効にするには、サービスを停止して、再起動する必要があります。

   > [!NOTE]
   > Azure AD パスワード保護プロキシ サービスは、ポート構成の変更後に、手動で再起動する必要があります。 この種の構成の変更を行った後、ドメイン コントローラーで実行されている DC エージェント サービス ソフトウェアを再起動する必要はありません。

   * サービスの現在の構成は、次の例に示すように `Get-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用して確認できます。

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Azure AD パスワード保護 DC エージェント サービスをインストールする

   `AzureADPasswordProtectionDCAgent.msi` MSI パッケージを使用して、Azure AD パスワード 保護 DC エージェント ソフトウェア サービスをインストールします

   パスワード フィルター DLL のロードとアンロードは再起動時にのみ実行されるというオペレーティング システムの要件により、このソフトウェアのインストールでは、インストールとアンインストールの実行時に再起動する必要があります。

   ドメイン コントローラーではないコンピューターへの DC エージェント サービスのインストールがサポートされています。 この場合、サービスは開始して実行されますが、コンピューターがドメイン コントローラーにレベル上げされるまで、サービスはアクティブになりません。

   ソフトウェアのインストールは、標準 MSI プロシージャを使用して自動化できます。次に例を示します。

   `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > 上記の msiexec コマンドの例を使用すると、すぐに再起動します。これは、`/norestart` フラグを指定することで回避できます。

ドメイン コントローラーにインストールして再起動されたら、Azure AD パスワード保護 DC エージェント ソフトウェアのインストールは完了です。 これ以外の構成は必要ないか、可能ではありません。

## <a name="multiple-forest-deployments"></a>複数のフォレストへの展開

Azure AD パスワード保護を複数のフォレストに展開するための追加要件はありません。 各フォレストは、「1 つのフォレストへの展開」セクションで説明したように、個別に構成されます。 各 Azure AD パスワード保護プロキシは、参加先のフォレストのドメイン コントローラーのみをサポートします。 特定のフォレスト内の Azure AD パスワード保護ソフトウェアは、Active Directory 信頼の構成に関係なく、別のフォレストに展開されている Azure AD パスワード保護ソフトウェアを認識しません。

## <a name="read-only-domain-controllers"></a>読み取り専用ドメイン コントローラー

パスワードの変更/設定は、読み取り専用ドメイン コントローラー (RODC) で処理と保持が行われることはありません。それらは書き込み可能ドメイン コントローラーに転送されます。 そのため、RODC に DC エージェント ソフトウェアをインストールする必要はありません。

## <a name="high-availability"></a>高可用性

Azure AD パスワード保護の高可用性の確保に関する主要な関心事は、フォレストのドメイン コントローラーが Azure から新しいポリシーやその他のデータをダウンロードしようとしたときのプロキシ サーバーの可用性です。 各 DC エージェントは、どのプロキシ サーバーを呼び出すかを決定するときに、単純なラウンドロビン方式のアルゴリズムを使用し、応答しないプロキシ サーバーをスキップします。 (ディレクトリおよび sysvol 状態の両方の) 正常なレプリケーションを使用して完全に接続された Active Directory デプロイでは、ほとんどの場合、2 つのプロキシ サーバーで十分な可用性を確保できるため、新しいポリシーやその他のデータをタイムリーにダウンロードできます。 必要に応じて、追加のプロキシ サーバーをデプロイすることもできます。

高可用性に関連する一般的な問題は、DC エージェント ソフトウェアの設計によって軽減されています。 DC エージェントは、ごく最近ダウンロードされたパスワード ポリシーのローカル キャッシュを保持します。 登録されているすべてのプロキシ サーバーが何らかの理由で使用できなくなった場合でも、DC エージェントは、キャッシュされたパスワード ポリシーを引き続き適用します。 大規模な展開でのパスワード ポリシーの妥当な更新頻度は、通常は日単位であり、時間やそれ以下の単位ではありません。 そのため、プロキシ サーバーの短時間の停止によって、Azure AD パスワード保護機能の運用やセキュリティ上の利点に大きな影響が出ることはありません。

## <a name="next-steps"></a>次の手順

Azure AD パスワード保護に必要なサービスがオンプレミス サーバーにインストールされたので、[インストール後の構成とレポート情報の収集](howto-password-ban-bad-on-premises-operations.md)を実行して、デプロイを完了します。

[Azure AD パスワード保護の概念の概要](concept-password-ban-bad-on-premises.md)
