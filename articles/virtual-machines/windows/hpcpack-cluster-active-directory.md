---
title: HPC Pack クラスターと Azure Active Directory | Microsoft Docs
description: Azure の Microsoft HPC Pack 2016 クラスターを Azure Active Directory と統合する方法について説明します
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/16/2017
ms.author: danlep
ms.openlocfilehash: bb0e878c4e987d111a535603cede25c639087ca7
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2017
ms.locfileid: "25452574"
---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Azure Active Directory を使用して Azure の HPC Pack クラスターを管理する
[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) では、Azure の HPC クラスターをデプロイする管理者向けに [Azure Active Directory](../../active-directory/index.md) (Azure AD) との統合をサポートしています。



次のタスクの概要については、この記事の手順に従ってください。 
* HPC Pack クラスターを Azure AD テナントに手動で統合する
* Azure の HPC Pack クラスターでジョブのスケジュールを設定して管理する 

HPC Pack クラスター ソリューションを Azure AD に統合するには、他のアプリケーションやサービスを統合する標準的な手順に従います。 この記事は、Azure AD での基本的なユーザー管理について理解していることを前提としています。 詳細と背景については、「[Azure Active Directory のドキュメント](../../active-directory/index.md)」と以降のセクションをご覧ください。

## <a name="benefits-of-integration"></a>統合の利点


Azure Active Directory (Azure AD) は、マルチテナントに対応したクラウド ベースのディレクトリと ID の管理サービスで、クラウド ソリューションへのシングル サインオン (SSO) アクセスを提供します。

HPC Pack クラスターを Azure AD と統合することで、次の目標の達成をサポートします。

* HPC Pack クラスターから従来の Active Directory ドメイン コント ローラーを削除します。 これにより、ユーザーのビジネスに必要ないクラスターの維持にかかるコストを削減し、デプロイ プロセスを高速化するのに役立ちます。
* Azure AD との統合により、次の利点が得られます。
    *   シングル サインオン 
    *   Azure の HPC Pack クラスターに AD のローカル ID を使用する 

    ![Azure Active Directory の環境](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>前提条件
* **Azure Virtual Machines にデプロイされている HPC Pack 2016 クラスター** - 手順については、「[Azure に HPC Pack 2016 クラスターをデプロイする](hpcpack-2016-cluster.md)」をご覧ください。 この記事の手順を完了するには、ヘッド ノードの DNS 名とクラスター管理者の資格情報が必要です。

  > [!NOTE]
  > Azure Active Directory との統合は、HPC Pack 2016 より前のバージョンではサポートされていません。



* **クライアント コンピューター** - HPC Pack クライアント ユーティリティを実行する Windows または Windows Server クライアント コンピューターが必要です。 ジョブを送信する目的だけで HPC Pack Web ポータルまたは REST API を使用する場合、自分で選んだクライアント コンピューターを利用できます。

* **HPC Pack クライアント ユーティリティ** - HPC Pack クライアント ユーティリティをクライアント コンピューターにインストールします。インストール パッケージは、Microsoft ダウンロード センターから無料で入手できます。


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>手順 1: HPC クラスター サーバーを Azure AD テナントに登録する
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. お使いのアカウントで複数の Azure AD テナントにアクセスできる場合は、右上隅でアカウントをクリックします。 その後、ポータル セッションを目的のテナントに設定します。 ディレクトリ内のリソースにアクセスするには、そのディレクトリへのアクセス許可が必要です。 
3. 左側のサービス ナビゲーション ウィンドウで **[Azure Active Directory]** をクリックし、**[ユーザーとグループ]** をクリックして、ユーザー アカウントが既に作成または構成されていることを確認します。
4. **[Azure Active Directory]** で、**[アプリの登録]** > **[新しいアプリケーションの登録]** の順にクリックします。 次の情報を入力します。
    * **[名前]** - HPCPackClusterServer
    * **[アプリケーションの種類]** - **[Web アプリ/API]** を選びます
    * **[サインイン URL]** - サンプルのベース URL (既定では `https://hpcserver`)
    * **Create** をクリックしてください。
5. アプリが追加された後、**[アプリの登録]** の一覧でそのアプリを選びます。 次に、**[設定]** > **[プロパティ]** の順にクリックします。 次の情報を入力します。
    * **[マルチテナント]** で **[はい]** を選びます。
    * **[アプリケーション ID/URI]** を「`https://<Directory_name>/<application_name>`」に変更します。 `<Directory_name`> を Azure AD テナントのフルネーム (`hpclocal.onmicrosoft.com` など) に置き換え、`<application_name>` を前に選択した名前に置き換えます。
6. **[Save]** をクリックします。 保存が完了したら、アプリ ページで **[マニフェスト]** をクリックします。 マニフェストを編集します。`appRoles` を探し、次のアプリケーション ロールを追加します。その後、**[保存]** をクリックします。

  ```json
  "appRoles": [
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "displayName": "HpcAdminMirror",
     "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "description": "HpcAdminMirror",
     "value": "HpcAdminMirror"
     },
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "description": "HpcUsers",
     "displayName": "HpcUsers",
     "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "value": "HpcUsers"
     }
  ],
  ```
7. **[Azure Active Directory]** で、**[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順にクリックします。 一覧から **HPCPackClusterServer** を選びます。
8. **[プロパティ]** をクリックし、**[ユーザーの割り当てが必要]** を **[はい]** に変更します。 **[Save]** をクリックします。
9. **[ユーザーとグループ]** > **[ユーザーの追加]** の順にクリックします。 ユーザーとロールを選び、**[割り当て]** をクリックします。 利用可能な役割 (HpcUsers または HpcAdminMirror) のいずれかをユーザーに割り当てます。 ディレクトリ内の他のユーザーでこの手順を繰り返します。 クラスター ユーザーの背景情報については、「[クラスター ユーザーの管理](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx)」をご覧ください。


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>手順 2: HPC クラスター クライアントを Azure AD テナントに登録する

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. お使いのアカウントで複数の Azure AD テナントにアクセスできる場合は、右上隅でアカウントをクリックします。 その後、ポータル セッションを目的のテナントに設定します。 ディレクトリ内のリソースにアクセスするには、そのディレクトリへのアクセス許可が必要です。 
3. **[Azure Active Directory]** で、**[アプリの登録]** > **[新しいアプリケーションの登録]** の順にクリックします。 次の情報を入力します。

    * **[名前]** - HPCPackClusterClient    
    * **[アプリケーションの種類]** - **[ネイティブ]** を選びます
    * **[リダイレクト URI]** - `http://hpcclient`
    * **[作成]**

4. アプリが追加された後、**[アプリの登録]** の一覧でそのアプリを選びます。 **[アプリケーション ID]** の値をコピーして保存します。 この値は後でアプリケーションを構成するときに必要です。

5. **[設定]** > **[必要なアクセス許可]** > **[追加]** > **[API を選択します]** の順にクリックします。 (手順 1 で作成した) HpcPackClusterServer アプリケーションを探して選びます。

6. **[アクセスの有効化]** ページで、**[Access HpcClusterServer]\(HpcClusterServer へのアクセス\)** を選びます。 次に、 **[Done]** をクリックします。


## <a name="step-3-configure-the-hpc-cluster"></a>手順 3: HPC クラスターを構成する

1. Azure で HPC Pack 2016 ヘッド ノードに接続します。

2. HPC PowerShell を起動します。

3. 次のコマンドを実行します。

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcPackClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    各値の説明:

    * `AADTenant` は Azure AD のテナント名を指定します (`hpclocal.onmicrosoft.com` など)。
    * `AADClientAppId` では、手順 2 で作成したアプリのアプリケーション ID を指定します。

4. ヘッド ノードの構成に応じて、次のいずれかを行います。

    * 単一ヘッド ノードの HPC Pack クラスターでは、HpcScheduler サービスを再起動します。

    * 複数ヘッド ノードの HPC Pack クラスターでは、ヘッド ノードで次の PowerShell コマンドを実行して、HpcSchedulerStateful サービスを再起動します。

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName "fabric:/HpcApplication/SchedulerStatefulService"

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>手順 4: ジョブを管理してクライアントからジョブを送信する

HPC Pack クライアント ユーティリティを自分のコンピューターにインストールするには、Microsoft ダウンロード センターから HPC Pack 2016 のセットアップ ファイル (完全インストール) をダウンロードします。 インストールを開始するとき、**HPC Pack クライアント ユーティリティ**のセットアップ オプションを選択します。

クライアント コンピューターを準備するには、クライアント コンピューターで [HPC クラスターをセットアップ](hpcpack-2016-cluster.md)する間に使用する証明書をインストールします。 Windows 標準の証明書管理手順を使用して、公開証明書を **[証明書 - 現在のユーザー]** >  の **[信頼されたルート証明機関]** ストアにインストールします。 

これで HPC Pack のコマンドを実行するか、HPC Pack のジョブ マネージャー GUI を使用して、Azure AD アカウントでクラスターのジョブを送信および管理できます。 ジョブの送信オプションについては、[Azure の HPC Pack クラスターに HPC のジョブを送信する方法](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster)に関する記事をご覧ください。

> [!NOTE]
> 初めて Azure の HPC Pack クラスターに接続しようとすると、ポップアップ ウィンドウが表示されます。 Azure ADの資格情報を入力してログインします。 トークンがキャッシュされます。 その後 Azure のクラスターに接続するときは、認証情報が変更されるかキャッシュがクリアされない限り、キャッシュされたトークンが使われます。
>
  
たとえば、前の手順を完了すると、次のようにしてオンプレミスのクライアントからジョブをクエリできます。

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Azure AD との統合により使用できるジョブ送信用の便利なコマンドレット 

### <a name="manage-the-local-token-cache"></a>ローカルのトークン キャッシュを管理する

HPC Pack 2016 には、ローカルのトークン キャッシュを管理する次の HPC PowerShell コマンドレットが用意されています。 これらのコマンドレットは、非対話形式でジョブを送信するのに便利です。 次の例を参照してください。

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Azure AD アカウントを使用してジョブを送信するための資格情報を設定する 

場合によっては、HPC クラスター ユーザーでのジョブの実行が必要になることがあります (ドメインに参加している HPC クラスターではドメイン ユーザーとして実行。ドメインに参加していない HPC クラスターではローカル ユーザーとしてヘッド ノードで実行)。

1. 次のコマンドを使用して、資格情報を設定します。

    ```powershell
    $localUser = "<username>"

    $localUserPassword="<password>"

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. その後、次のようにジョブを送信します。 ジョブとタスクは、$localUser 計算ノードで実行されます。

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   `–Credential` が `Submit-HpcJob` で指定されていない場合、ジョブとタスクはローカルで Azure AD アカウントとしてマッピングされているユーザーの下で実行されます  (HPC クラスターが Azure AD アカウントと同じ名前のローカル ユーザーを作成してタスクを実行します)。
    
3. Azure AD アカウントの拡張データを設定します。 これは、Azure AD アカウントを使用して Linux ノードで MPI ジョブを実行する際に便利です。

   * Azure AD アカウント自体の拡張データを設定する

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * 拡張データを設定し HPC クラスター ユーザーとして実行する
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```

