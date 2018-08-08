---
title: Azure AD パスワード保護のプレビューを展開する
description: Azure AD パスワード保護のプレビューを展開して、オンプレミスでの間違ったパスワードの使用を禁止します
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 5928896ab3c89972b7912f686be045afc988b1cd
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308877"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>プレビュー: Azure AD パスワード保護を展開する

|     |
| --- |
| Azure AD パスワード保護と禁止パスワードのカスタム リストは、Azure Active Directory のパブリック プレビュー機能です。 プレビューの詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

[Windows Server Active Directory に対して Azure AD パスワード保護を適用する方法](concept-password-ban-bad-on-premises.md)が理解できたので、次の手順は、その展開を計画して実行することです。

## <a name="deployment-strategy"></a>展開戦略

Microsoft では、すべての展開を監査モードで開始することをお勧めしています。 監査モードは既定の初期設定であり、パスワードの設定は継続でき、ブロックされた場合は、イベント ログにエントリが作成されます。 プロキシ サーバーと DC エージェントが監査モードで完全に配置されたら、パスワード ポリシーが適用された場合にその適用がユーザーと環境にどのような影響を与えるかを見極めるために、通常の監視を実行する必要があります。

この監査段階中に、多くの組織は、以下に気付きます。

* より安全なパスワードを使用するように存の運用プロセスを改善する必要がある。
* ユーザーは、安全でないパスワードを定期的に選択することに慣れている。
* セキュリティの適用の今後の変更と、それによってもたらされる可能性がある影響をユーザーに通知し、より安全なパスワードを選択できる方法をユーザーが理解するための手助けをする必要がある。

この機能を妥当な時間にわたって監査モードで実行した後、適用構成を **[監査]** から **[適用]** に変更することで、より安全なパスワードを要求できます。 この期間中に、集中的な監視を行うことをお勧めします。

## <a name="known-limitation"></a>既知の制限事項

Azure AD パスワード保護プロキシのプレビュー バージョンには、既知の制限があります。 MFA を必要とするテナント管理者アカウントの使用はサポートされません。 Azure AD パスワード保護プロキシの今後の更新で、MFA を必要とする管理者アカウントのプロキシ登録をサポートする予定です。

## <a name="single-forest-deployment"></a>1 つのフォレストへの展開

Azure AD パスワード保護のプレビューは、最大 2 台のサーバー上に展開でき、DC エージェント サービスは、Active Directory フォレスト内のすべてのドメイン コントローラーに付加的に展開できます。

![Azure AD パスワード保護コンポーネントの連携方法](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>ソフトウェアをダウンロードする

Azure AD パスワード保護のために必要なインストーラーが 2 つあり、これらは [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=57071)からダウンロードできます。

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Azure AD パスワード保護プロキシ サービスをインストールして構成する

1. Azure AD パスワード保護プロキシ サービスをホストする 1 つまたは複数のサーバーを選択します。
   * 各サービスは 1 つのフォレストにのみパスワード ポリシーを提供でき、ホスト コンピューターは、フォレスト内のドメインに対してドメイン参加済みである必要があります (ルートと子はどちらも同じようにサポートされます)。 Azure AD パスワード保護プロキシ サービスがその機能を発揮するには、フォレストの各ドメイン内の少なくとも 1 つの DC と Azure AD パスワード保護プロキシ ホスト コンピューターとの間にネットワーク接続が存在する必要があります。
   * テスト目的でのドメイン コントローラーへの Azure AD パスワード保護プロキシ サービスのインストールと実行はサポートされていますが、その場合、ドメイン コントローラーはインターネット接続を必要とします。

   > [!NOTE]
   > パブリック プレビューでは、フォレストごとに最大 2 台のプロキシ サーバーをサポートします。

2. AzureADPasswordProtectionProxy.msi MSI パッケージを使用して、パスワード ポリシー プロキシ サービス ソフトウェアをインストールします。
   * このソフトウェアのインストールでは、再起動は必要ありません。 ソフトウェアのインストールは、標準 MSI プロシージャ (例: `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`) を使用して自動化できます。

3. 管理者として PowerShell ウィンドウを開きます。
   * Azure AD パスワード保護プロキシ ソフトウェアには、AzureADPasswordProtection という名前の新しい PowerShell モジュールが含まれています。 次の手順は、この PowerShell モジュールのさまざまなコマンドレットの実行に基づいており、新しい PowerShell ウィンドウを開いて、新しいモジュールを次のようにインポートしていることを前提としています。
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > インストール ソフトウェアは、ホスト コンピューターの PSModulePath 環境変数を変更します。 この変更を有効にして、AzureADPasswordProtection powershell モジュールをインポートして使用できるようにするに、最新の PowerShell コンソール ウィンドウを開く必要がある場合があります。

   * 次の PowerShell コマンドを使用して、サービスが実行されていることを確認します。`Get-Service AzureADPasswordProtectionProxy | fl`
      * [実行中] という**状態**を返す結果が生成されます。

4. プロキシを登録します。
   * 手順 3 が完了すると、Azure AD パスワード保護プロキシ サービスがコンピューター上で実行されますが、Azure AD と通信するために必要な資格情報がまだ用意されていません。 `Register-AzureADPasswordProtectionProxy` PowerShell コマンドレットを使用してその機能を有効にするには、Azure AD の登録が必要です。 このコマンドレットは、Azure テナントのグローバル管理者資格情報と、フォレストのルート ドメイン内にオンプレミスの Active Directory ドメインの管理者特権を必要とします。 `Register-AzureADPasswordProtectionProxy` の呼び出しは、特定のプロキシ サービスで成功すれば、次回以降の呼び出しも成功しますが、これ以上の呼び出しは必要ありません。
      * コマンドレットは、次のように実行できます。

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         この例は、現在ログインしているユーザーがルート ドメインの Active Directory ドメイン管理者でもある場合にのみ有効です。 別の方法は、`-ForestCredential` パラメーターを使用して必要なドメイン資格情報を指定することです。

   > [!TIP]
   > 特定の Azure テナントに対するこのコマンドレットの初回の実行では、コマンドレットの実行が完了するまで、かなりの遅延 (数十秒) が発生することがあります。 エラーが報告される場合を除き、この遅延について心配する必要はありません。

   > [!NOTE]
   > Azure AD パスワード保護プロキシ サービスの登録は、サービスの有効期間中に 1 回限り実行される手順であると想定されています。 この時点以降、他の必要なメンテナンスは、プロキシ サービスによって自動的に実行されます。 'Register-AzureADPasswordProtectionProxy' の呼び出しは、特定のフォレストで成功すれば、次回以降の呼び出しも成功しますが、これ以上の呼び出しは必要ありません。

5. フォレストを登録します。
   * `Register-AzureADPasswordProtectionForest` Powershell コマンドレットを使用して、Azure と通信するために必要な資格情報で、オンプレミスの Active Directory フォレストを初期化する必要があります。 このコマンドレットは、Azure テナントのグローバル管理者資格情報と、フォレストのルート ドメイン内にオンプレミスの Active Directory ドメインの管理者特権を必要とします。 この手順は、フォレストごとに 1 回実行されます。
      * コマンドレットは、次のように実行できます。

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         この例は、現在ログインしているユーザーがルート ドメインの Active Directory ドメイン管理者でもある場合にのみ有効です。 別の方法は、-ForestCredential パラメーターを使用して、必要なドメイン資格情報を指定することです。

         > [!NOTE]
         > 環境に複数のプロキシ サーバーがインストールされている場合、上記の手順でどのプロキシ サーバーが指定されているかの制限はありません。

         > [!TIP]
         > 特定の Azure テナントに対するこのコマンドレットの初回の実行では、コマンドレットの実行が完了するまで、かなりの遅延 (数十秒) が発生することがあります。 エラーが報告される場合を除き、この遅延について心配する必要はありません。

   > [!NOTE]
   > Active Directory フォレストの登録は、フォレストの有効期間中に 1 回のみ実行される手順であると想定されています。 この時点以降、他の必要なメンテナンスは、フォレストで実行されるドメイン コントローラー エージェントによって自動的に実行されます。 `Register-AzureADPasswordProtectionForest` の呼び出しは、特定のフォレストで成功すれば、次回以降の呼び出しも成功しますが、これ以上の呼び出しは必要ありません。

   > [!NOTE]
   > `Register-AzureADPasswordProtectionForest` を少なくとも 1 台の Windows Server 2012 以降で成功させるには、プロキシ サーバーのドメイン内でドメイン コントローラーを使用できる必要があります。 ただし、この手順に先立って、任意のドメイン コントローラーに DC エージェント ソフトウェアをインストールするという要件はありません。

6. 省略可能: 特定のポートでリッスンするように Azure AD パスワード保護プロキシ サービスを構成します。
   * Azure AD パスワード保護プロキシ サービスと通信するために、ドメイン コントローラー上の Azure AD パスワード保護 DC エージェント ソフトウェアによって、TCP 経由で RPC が使用されます。 既定では、Azure AD パスワード保護パスワード ポリシー プロキシ サービスは、使用可能な動的 RPC エンドポイントでリッスンします。 ネットワーク トポロジまたはファイアウォールの要件応じて、必要であれば、特定の TCP ポートでリッスンするようにサービスを構成できます。
      * 静的ポートで実行するようにサービスを構成するには、`Set-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用します。
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > これらの変更を有効にするには、サービスを停止して、再起動する必要があります。

      * 動的ポートで実行するようにサービスを構成するには、同じ手順を使用しますが、次のように StaticPort の設定を 0 に戻します。
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > これらの変更を有効にするには、サービスを停止して、再起動する必要があります。

   > [!NOTE]
   > Azure AD パスワード保護プロキシ サービスは、ポート構成の変更後に、手動で再起動する必要があります。 この種の構成の変更を行った後、ドメイン コントローラーで実行されている DC エージェント サービス ソフトウェアを再起動する必要はありません。

   * サービスの現在の構成は、次の例に示すように `Get-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用して確認できます。

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Azure AD パスワード保護 DC エージェント サービスをインストールする

* `AzureADPasswordProtectionDCAgent.msi` MSI パッケージを使用して、Azure AD パスワード 保護 DC エージェント ソフトウェア サービスをインストールします。
   * パスワード フィルター DLL のロードとアンロードは再起動時にのみ実行されるというオペレーティング システムの要件により、このソフトウェアのインストールでは、インストールとアンインストールの実行時に再起動する必要があります。
   * ドメイン コントローラーではないコンピューターへの DC エージェント サービスのインストールがサポートされています。 この場合、サービスは開始して実行されますが、コンピューターがドメイン コントローラーにレベル上げされるまで、サービスはアクティブになりません。

   ソフトウェアのインストールは、標準 MSI プロシージャ (例: `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`) を使用して自動化できます。

   > [!WARNING]
   > この msiexec コマンドの例を使用すると、すぐに再起動します。これは、`/norestart` フラグを指定することで回避できます。

ドメイン コントローラーにインストールして再起動されたら、Azure AD パスワード保護 DC エージェント ソフトウェアのインストールは完了です。 これ以外の構成は必要ないか、可能ではありません。

## <a name="multiple-forest-deployments"></a>複数のフォレストへの展開

Azure AD パスワード保護を複数のフォレストに展開するための追加要件はありません。 各フォレストは、「1 つのフォレストへの展開」セクションで説明したように、個別に構成されます。 各 Azure AD パスワード保護プロキシは、参加先のフォレストのドメイン コントローラーのみをサポートします。 特定のフォレスト内の Azure AD パスワード保護ソフトウェアは、Active Directory 信頼の構成に関係なく、別のフォレストに展開されている Azure AD パスワード保護ソフトウェアを認識しません。

## <a name="read-only-domain-controllers"></a>読み取り専用ドメイン コントローラー

パスワードの変更/設定は、読み取り専用ドメイン コントローラー (RODC) で処理と保持が行われることはありません。それらは書き込み可能ドメイン コントローラーに転送されます。 そのため、RODC に DC エージェント ソフトウェアをインストールする必要はありません。

## <a name="high-availability"></a>高可用性

Azure AD パスワード保護の高可用性の確保に関する主要な関心事は、フォレストのドメイン コントローラーが Azure から新しいポリシーやその他のデータをダウンロードしようとしたときのプロキシ サーバーの可用性です。 このパブリック プレビューでは、フォレストごとに最大 2 台のプロキシ サーバーをサポートします。 各 DC エージェントは、どのプロキシ サーバーを呼び出すかを決定するときに、単純なラウンドロビン方式のアルゴリズムを使用し、応答しないプロキシ サーバーをスキップします。
高可用性に関連する一般的な問題は、DC エージェント ソフトウェアの設計によって軽減されています。 DC エージェントは、ごく最近ダウンロードされたパスワード ポリシーのローカル キャッシュを保持します。 登録されているすべてのプロキシ サーバーが何らかの理由で使用できなくなった場合でも、DC エージェントは、キャッシュされたパスワード ポリシーを引き続き適用します。 大規模な展開でのパスワード ポリシーの妥当な更新頻度は、通常は日単位であり、時間やそれ以下の単位ではありません。   そのため、プロキシ サーバーの短時間の停止によって、Azure AD パスワード保護機能の運用やセキュリティ上の利点に大きな影響が出ることはありません。

## <a name="next-steps"></a>次の手順

Azure AD パスワード保護に必要なサービスがオンプレミス サーバーにインストールされたので、[インストール後の構成とレポート情報の収集](howto-password-ban-bad-on-premises-operations.md)を実行して、デプロイを完了します。

[Azure AD パスワード保護の概念の概要](concept-password-ban-bad-on-premises.md)
