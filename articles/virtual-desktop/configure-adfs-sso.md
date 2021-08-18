---
title: Azure Virtual Desktop の AD FS シングル サインオンを構成する - Azure
description: Azure Virtual Desktop 環境用に AD FS シングル サインオンを構成する方法。
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/30/2021
ms.author: helohr
ms.openlocfilehash: 3c6e61754d9332cbdfbea6b971363c1b0d6cb4fe
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289423"
---
# <a name="configure-ad-fs-single-sign-on-for-azure-virtual-desktop"></a>Azure Virtual Desktop 用に AD FS シングル サインオンを構成する

この記事では、Azure Virtual Desktop 用に Active Directory フェデレーション サービス (AD FS) シングル サインオン (SSO) を構成するプロセスについて説明します。

> [!NOTE]
> Azure Virtual Desktop (クラシック) では、この機能はサポートされていません。

## <a name="requirements"></a>必要条件

AD FS シングルサインオンを構成する前に、ご自分の環境で次のセットアップを実行しておく必要があります。

* **Active Directory 証明書サービス (CA)** 役割をデプロイする必要があります。 この役割を実行しているすべてのサーバーは、ドメインに参加しており、最新の Windows 更新プログラムをインストールしており、[エンタープライズ証明機関](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731183%28v%3dws.10%29)として構成されている必要があります。
* **Active Directory フェデレーション サービス (AD FS)** 役割をデプロイする必要があります。 この役割を実行しているすべてのサーバーは、ドメインに参加しており、最新の Windows 更新プログラムをインストールしており、Windows Server 2016 以降を実行している必要があります。 この役割の設定を開始するには、[フェデレーションのチュートリアル](../active-directory/hybrid/tutorial-federation.md)に関するページを参照してください。
* ご使用環境から AD FS サーバーへの接続をセキュリティで保護するために、**Web アプリケーション プロキシ** 役割を設定することをお勧めします。 この役割を実行しているすべてのサーバーでは、最新の Windows 更新プログラムがインストールされており、Windows Server 2016 以降が実行されている必要があります。 この役割の設定を開始するには、こちらの [Web アプリケーション プロキシ ガイド](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383662(v=ws.11))に関するページを参照してください。
* Azure AD にユーザーを同期するには、**Azure AD Connect** をデプロイする必要があります。 Azure AD Connect は、[フェデレーション モード](../active-directory/hybrid/how-to-connect-install-custom.md)で構成する必要があります。
* AD FS サーバー上で Azure Virtual Desktop 用に [PowerShell 環境を設定](powershell-module.md)します。
* Windows 10 20H1 または 20H2 を使用して Azure Virtual Desktop に接続する場合、シングル サインオンを正常に機能させるには、**Windows 10 の 2021-04 Cumulative Update (累積的な更新プログラム) (KB5001330)** またはそれ以降をインストールする必要があります。

> [!NOTE]
> このソリューションは、Azure AD Domain Services ではサポートされていません。 Active Directory ドメイン コントローラーを使用する必要があります。

## <a name="supported-clients"></a>サポートされるクライアント

次の Azure Virtual Desktop クライアントでは、この機能がサポートされています。

* [Windows デスクトップ クライアント](./user-documentation/connect-windows-7-10.md)
* [Web クライアント](./user-documentation/connect-web.md)

## <a name="configure-the-certificate-authority-to-issue-certificates"></a>証明書を発行するように証明機関を構成する

AD FS で SSO を使用できるようにするためには、次の証明書テンプレートを適切に作成する必要があります。

* まず、**Exchange 登録エージェント (オフライン要求)** 証明書テンプレートを作成する必要があります。 AD FS では、Exchange 登録エージェント証明書テンプレートを使用して、ユーザーの代わりに証明書が要求されます。
* また、**スマートカード ログオン** 証明書テンプレートも作成する必要があります。AD FS では、これを使用してサインイン証明書が作成されます。

これらの証明書テンプレートを作成したら、AD FS が要求できるように、証明機関でテンプレートを有効にする必要があります。

> [!NOTE]
> このソリューションでは、ユーザーのログオンごとに新しい短期間の証明書が生成されます。そのため、ユーザーが多数いる場合は、時間が経つにつれて証明機関データベースがいっぱいになる可能性があります。 これは、[非永続的な証明書の処理用に CA を設定](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ff934598(v=ws.10))することによって回避できます。

### <a name="create-the-enrollment-agent-certificate-template"></a>登録エージェント証明書テンプレートを作成する

環境によっては、Windows Hello for Business、ログオン証明書、VPN 証明書などの他の目的のために、登録エージェント証明書テンプレートが既に構成されている場合があります。 その場合は、SSO をサポートするように変更する必要があります。 それ以外の場合は、新しいテンプレートを作成できます。

登録エージェント証明書テンプレートを既に使用しているかどうかを判断するには、AD FS サーバーで次の PowerShell コマンドを実行し、値が返されるかどうかを確認します。 空の場合は、新しい登録エージェント証明書テンプレートを作成します。 それ以外の場合は、その名前を記憶し、既存の登録エージェント証明書テンプレートを更新します。

```powershell
Import-Module adfs
(Get-AdfsCertificateAuthority).EnrollmentAgentCertificateTemplateName
```

新しい登録エージェント証明書テンプレートを作成するには、次のようにします。

1. 証明機関で、[スタート] メニューから **mmc.exe** を実行して **Microsoft 管理コンソール** を起動します。
2. **[ファイル...]**  >  **[スナップインの追加と削除...]**  >  **[証明書テンプレート]**  >  **[追加] >**  >  **[OK]** を選択して、証明書テンプレートの一覧を表示します。
3. **[証明書テンプレート]** を展開し、 **[Exchange 登録エージェント (オフライン要求)]** を右クリックし、 **[テンプレートの複製]** を選択します。
4. **[全般]** タブを選択し、 **[テンプレートの表示名]** フィールドに、"ADFS Enrollment Agent" と入力します。 これにより、テンプレート名が自動的に "ADFSEnrollmentAgent" に設定されます。
5. **[セキュリティ]** タブを選択してから、 **[追加...]** を選択します。
6. 次に、 **[オブジェクトの種類...]** 、 **[サービス アカウント]** 、 **[OK]** の順に選択します。
7. AD FS のサービス アカウント名を入力し、 **[OK]** を選択します。
   * AD FS の分離セットアップの場合、サービス アカウント名は "adfssvc$" になります。
   * Azure AD Connect を使用して AD FS を設定した場合、サービス アカウントの名前は "aadcsvc$" になります。
8. サービス アカウントが追加されて、 **[セキュリティ]** タブに表示されたら、 **[グループまたはユーザー名]** ペインでそれを選択し、 **[AD FS サービス アカウントのアクセス権]** ペインの "登録" と "自動登録" の両方に対して **[許可]** を選択し、 **[OK]** を選択して保存します。

   :::image type="content" source="media/adfs-enrollment-properties-security.png" alt-text="正しく構成された後の登録エージェント証明書テンプレートの [セキュリティ] タブを示すスクリーンショット。":::

既存の登録エージェント証明書テンプレートを更新するには、次のようにします。

1. 証明機関で、[スタート] メニューから **mmc.exe** を実行して **Microsoft 管理コンソール** を起動します。
2. **[ファイル...]**  >  **[スナップインの追加と削除...]**  >  **[証明書テンプレート]**  >  **[追加] >**  >  **[OK]** を選択して、証明書テンプレートの一覧を表示します。
3. **[証明書テンプレート]** を展開し、AD FS サーバーに構成されているものに対応するテンプレートをダブルクリックします。 **[全般]** タブのテンプレート名は、前述の名前と一致している必要があります。
4. **[セキュリティ]** タブを選択してから、 **[追加...]** を選択します。
5. 次に、 **[オブジェクトの種類...]** 、 **[サービス アカウント]** 、 **[OK]** の順に選択します。
6. AD FS のサービス アカウント名を入力し、 **[OK]** を選択します。
   * AD FS の分離セットアップの場合、サービス アカウント名は "adfssvc$" になります。
   * Azure AD Connect を使用して AD FS を設定した場合、サービス アカウントの名前は "aadcsvc$" になります。
7. サービス アカウントが追加されて、 **[セキュリティ]** タブに表示されたら、 **[グループまたはユーザー名]** ペインでそれを選択し、 **[AD FS サービス アカウントのアクセス権]** ペインの "登録" と "自動登録" の両方に対して **[許可]** を選択し、 **[OK]** を選択して保存します。

### <a name="create-the-smartcard-logon-certificate-template"></a>スマートカード ログオン証明書テンプレートを作成する

スマートカード ログオン証明書テンプレートを作成するには、次のようにします。

1. 証明機関で、[スタート] メニューから **mmc.exe** を実行して **Microsoft 管理コンソール** を起動します。
2. **[ファイル...]**  >  **[スナップインの追加と削除...]**  >  **[証明書テンプレート]**  >  **[追加]**  >  **[OK]** を選択して、証明書テンプレートの一覧を表示します。
3. **[証明書テンプレート]** を展開し、 **[スマートカード ログオン]** を右クリックして、 **[テンプレートの複製]** を選択します。
4. **[全般]** タブを選択し、 **[テンプレートの表示名]** フィールドに、"ADFS SSO" と入力します。 これにより、テンプレート名が自動的に "ADFSSSO" に設定されます。
   > [!NOTE]
   > この証明書はオンデマンドで要求されるため、有効期間は 8 時間、更新期間は 1 時間に短縮することをお勧めします。

5. **[サブジェクト名]** タブを選択し、 **[要求に含まれる]** を選択します。 警告メッセージが表示されたら、 **[OK]** を選択します。

   :::image type="content" source="media/adfs-sso-properties-subject-inline.png" alt-text="SSO 証明書テンプレートの [サブジェクト名] タブと、適切に構成された場合にそれがどのように表示されるかを示すスクリーンショット。" lightbox="media/adfs-sso-properties-subject-expanded.png":::

6. **[発行の要件]** タブを選択します。
7. **[次の数の認証署名]** を選択し、値 **1** を入力します。

   :::image type="content" source="media/adfs-sso-properties-issuance-inline.png" alt-text="SSO 証明書テンプレートの [発行の要件] タブと、適切に構成された場合にそれがどのように表示されるかを示すスクリーンショット。" lightbox="media/adfs-sso-properties-issuance-expanded.png":::

8. **[アプリケーション ポリシー]** には、 **[証明書の要求エージェント]** を選択します。
9.  **[セキュリティ]** タブを選択してから、 **[追加...]** を選択します。
10. **[オブジェクトの種類]** 、 **[サービス アカウント]** 、 **[OK]** を選択します。
11. 「[登録エージェント証明書テンプレートを作成する](#create-the-enrollment-agent-certificate-template)」セクションで行ったのと同じように、AD FS のサービス アカウント名を入力します。
    * AD FS の分離セットアップの場合、サービス アカウント名は "adfssvc$" になります。
    * Azure AD Connect を使用して AD FS を設定した場合、サービス アカウントの名前は "aadcsvc$" になります。
12. サービス アカウントが追加されて、 **[セキュリティ]** タブに表示されたら、 **[グループまたはユーザー名]** ペインでそれを選択し、"登録" と "自動登録" の両方に対して **[許可]** を選択し、 **[OK]** を選択して保存します。

   :::image type="content" source="media/adfs-sso-properties-security.png" alt-text="正しく構成された後の SSO 証明書テンプレートの [セキュリティ] タブを示すスクリーンショット。":::

### <a name="enable-the-new-certificate-templates"></a>新しい証明書テンプレートを有効にする

新しい証明書テンプレートを有効にするには、次のようにします。

1. 証明機関で、[スタート] メニューから **mmc.exe** を実行して **Microsoft 管理コンソール** を起動します。
2. **[ファイル...]**  >  **[スナップインの追加と削除...]**  >  **[証明機関]**  >  **[追加] >**  >  **[終了]** > **[OK]** の順に選択して、証明機関を表示します。
3. 左側のペインで証明機関を展開し、 **[証明書テンプレート]** を開きます。
4. 中央のペインを右クリックして証明書テンプレートの一覧を表示し、 **[新規]** を選択し、 **[発行する証明書テンプレート]** を選択します。
5. **[ADFS 登録エージェント]** と **[ADFS SSO]** の両方を選択し、 **[OK]** を選択します。 中央のペインに両方のテンプレートが表示されます。

   :::image type="content" source="media/adfs-certificate-templates.png" alt-text="新しい ADFS 登録エージェントと ADFS SSO を含む、発行できる証明書テンプレートの一覧を示すスクリーンショット。":::

   > [!NOTE]
   > 登録エージェント証明書テンプレートが既に構成されている場合は、ADFS SSO テンプレートを追加するだけです。

## <a name="configure-the-ad-fs-servers"></a>AD FS サーバーを構成する

新しい証明書テンプレートを使用するように Active Directory フェデレーション サービス (AD FS) サーバーを構成し、SSO をサポートするように証明書利用者の信頼を設定する必要があります。

AD FS サーバーと Azure Virtual Desktop サービス間の証明書利用者の信頼によって、シングル サインオン証明書要求をドメイン環境に正しく転送できます。

AD FS シングル サインオンを構成するときに、共有キーまたは証明書を選択する必要があります。

* AD FS サーバーが 1 つの場合は、共有キーまたは証明書を選択できます。
* 複数の AD FS サーバーがある場合は、証明書を選択する必要があります。

Windows にサインインするためのトークンの生成に使用される共有キーまたは証明書は、[Azure Key Vault](../key-vault/general/overview.md) に安全に格納されている必要があります。 シークレットは既存の Key Vault に格納することも、新しいものをデプロイすることもできます。 どちらの場合も、Azure Virtual Desktop サービスがアクセスできるように、適切なアクセス ポリシーを設定する必要があります。

証明書を使用するときは、任意の汎用証明書を使用できます。サブジェクト名やサブジェクトの別名 (SAN) に対する要件はありません。 必須ではありませんが、有効な証明機関によって発行された証明書を作成することをお勧めします。 この証明書は Azure Key Vault で直接作成でき、エクスポート可能な秘密キーを持っている必要があります。 下のスクリプトを使用して、公開キーをエクスポートし、AD FS サーバーを構成するために使用できます。 この証明書は、適切なサブジェクト名と有効な証明機関を持っている必要がある AD FS の SSL 証明書とは異なることに注意してください。

[PowerShell ギャラリー](https://www.powershellgallery.com/packages/ConfigureWVDSSO)にある PowerShell スクリプト **ConfigureWVDSSO.ps1** では、証明書利用者の信頼用に AD FS サーバーが構成され、必要に応じて証明書がインストールされます。

このスクリプトには、1 つだけ必須パラメーター *ADFSAuthority* があります。これは、AD FS に解決され、"/adfs" をサフィックスとして使用する URL です。 たとえば、「 `https://adfs.contoso.com/adfs` 」のように入力します。

1. AD FS VM で、次の PowerShell コマンドレットを実行して、前のセクションの証明書テンプレートを使用するように AD FS を構成します。
  
   ```powershell
   Set-AdfsCertificateAuthority -EnrollmentAgentCertificateTemplate "ADFSEnrollmentAgent" -LogonCertificateTemplate "ADFSSSO" -EnrollmentAgent
   ```
 
   > [!NOTE]
   > EnrollmentAgentCertificateTemplate が既に構成されている場合は、ADFSEnrollmentAgent ではなく、既存のテンプレート名を必ず使用してください。

2. ConfigureWVDSSO.ps1 スクリプトを実行します。
   > [!NOTE]
   > 指示の次の部分を完了するには、`$config` 変数の値が必要です。したがって、前の指示を完了するために使用した PowerShell ウィンドウを閉じないでください。 同じ PowerShell ウィンドウを引き続き使用するか、新しい PowerShell セッションの起動中、それを開いたままにしておくことができます。
   
   * Key Vault で共有キーを使用している場合は、AD FS サーバーで次の PowerShell コマンドレットを実行します。この場合、ADFSServiceUrl は、AD FS サービスにアクセスするための完全な URL に置き換えてください。

     ```powershell
     Install-Script ConfigureWVDSSO
     $config = ConfigureWVDSSO.ps1 -ADFSAuthority "<ADFSServiceUrl>" [-WvdWebAppAppIDUri "<WVD Web App URI>"] [-RdWebURL "<RDWeb URL>"]
     ```

     > [!NOTE]
     > Azure Government のようなソブリン クラウド内に環境を構成するには、WvdWebAppAppIDUri と RdWebURL プロパティが必要です。 Azure 商用クラウドでは、これらのプロパティは、それぞれ `https://www.wvd.microsoft.com` と `https://rdweb.wvd.microsoft.com` に自動的に設定されます。

   * Key Vault で証明書を使用している場合は、AD FS サーバーで次の PowerShell コマンドレットを実行します。この場合、ADFSServiceUrl は、AD FS サービスにアクセスするための完全な URL に置き換えてください。

     ```powershell
     Install-Script ConfigureWVDSSO
     $config = ConfigureWVDSSO.ps1 -ADFSAuthority "<ADFSServiceUrl>" -UseCert -CertPath "<Path to the pfx file>" -CertPassword <Password to the pfx file> [-WvdWebAppAppIDUri "<WVD Web App URI>"] [-RdWebURL "<RDWeb URL>"]
     ```

     > [!NOTE]
     > Azure Government のようなソブリン クラウド内に環境を構成するには、WvdWebAppAppIDUri と RdWebURL プロパティが必要です。 Azure 商用クラウドでは、これらのプロパティは、それぞれ `https://www.wvd.microsoft.com` と `https://rdweb.wvd.microsoft.com` に自動的に設定されます。

3. 次の PowerShell コマンドレットを実行して、Azure Key Vault のアクセス ポリシーを設定します。

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName "<Key Vault Name>" -ServicePrincipalName 9cdead84-a844-4324-93f2-b2e6bb768d07 -PermissionsToSecrets get -PermissionsToKeys sign
   ```

4. シークレットの使用を許可されているサブスクリプション ID のコンマ区切りリストが含まれたタグを使用して、Azure Key Vault に共有キーまたは証明書を格納します。

   * Key Vault で共有キーを使用している場合は、次の PowerShell コマンドレットを実行して共有キーを格納し、タグを設定します。

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" 
     $secret = Set-AzKeyVaultSecret -VaultName "<Key Vault Name>" -Name "adfsssosecret" -SecretValue (ConvertTo-SecureString -String $config.SSOClientSecret  -AsPlainText -Force) -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]}
     ```

   * 証明書が既に Key Vault にある場合は、次の PowerShell コマンドレットを実行してタグを設定します。

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>"
     $secret = Update-AzKeyVaultCertificate -VaultName "<Key Vault Name>" -Name "<Certificate Name>" -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]} -PassThru
     ```

   * ローカル証明書がある場合は、次の PowerShell コマンドレットを実行して、Key Vault に証明書をインポートし、タグを設定します。

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" 
     $secret = Import-AzKeyVaultCertificate -VaultName "<Key Vault Name>" -Name "adfsssosecret" -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]} -FilePath "<Path to pfx>" -Password (ConvertTo-SecureString -String "<pfx password>"  -AsPlainText -Force)
     ```

> [!NOTE]
> 必要に応じて、[AD FS シングル サインオン設定](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#keep-me-signed-in-for-unauthenticated-devices)を変更することにより、ユーザーに資格情報の入力を求める頻度を構成できます。 既定では、登録されていないデバイスでは 8 時間ごとにユーザーに入力が求められます。

## <a name="configure-your-azure-virtual-desktop-host-pool"></a>Azure Virtual Desktop ホスト プールを構成する

ここでは、Azure Virtual Desktop ホスト プールに AD FS SSO パラメーターを構成します。 これを行うには、まだアカウントに接続していない場合は、Azure Virtual Desktop 用に [PowerShell 環境を設定](powershell-module.md)します。

その後、AD FS VM の同じ PowerShell ウィンドウで次の 2 つのコマンドレットのいずれかを実行して、ホスト プールの SSO 情報を更新します。

* Key Vault で共有キーを使用している場合は、次の PowerShell コマンドレットを実行します。

  ```powershell
  Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority "<ADFSServiceUrl>" -SsoClientId "<WVD Web App URI>" -SsoSecretType SharedKeyInKeyVault -SsoClientSecretKeyVaultPath $secret.Id
  ```

  > [!NOTE]
  > SSO をデプロイしている Azure クラウドに一致するように、SsoClientId プロパティを設定する必要があります。 Azure 商用クラウドでは、このプロパティは `https://www.wvd.microsoft.com` に設定されます。 ただし、このプロパティに必要な設定は、Azure Government クラウドなどの他のクラウドでは異なります。

* Key Vault で証明書を使用している場合は、次の PowerShell コマンドレットを実行します。

  ```powershell
  Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority "<ADFSServiceUrl>" -SsoClientId "<WVD Web App URI>" -SsoSecretType CertificateInKeyVault -SsoClientSecretKeyVaultPath $secret.Id
  ```

  > [!NOTE]
  > SSO をデプロイしている Azure クラウドに一致するように、SsoClientId プロパティを設定する必要があります。 Azure 商用クラウドでは、このプロパティは `https://www.wvd.microsoft.com` に設定されます。 ただし、このプロパティに必要な設定は、Azure Government クラウドなどの他のクラウドでは異なります。

### <a name="configure-additional-host-pools"></a>追加のホスト プールを構成する

追加のホスト プールを構成する必要がある場合は、既存のホスト プールを構成するために使用した設定を取得して、新しいものを設定できます。

既存のホスト プールから設定を取得するには、PowerShell ウィンドウを開き、次のコマンドレットを実行します。

```powershell
Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" | fl *
```

同じ *SsoClientId*、*SsoClientSecretKeyVaultPath*、*SsoSecretType*、および *SsoadfsAuthority* の値を使用して、手順に従って [Azure Virtual Desktop ホスト プールを構成する](#configure-your-azure-virtual-desktop-host-pool)ことができます。

## <a name="removing-sso"></a>SSO の削除

ホスト プールで SSO を無効にするには、次のコマンドレットを実行します。

```powershell
Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority ''
```

さらに AD FS サーバーで SSO を無効にする場合は、次のコマンドレットを実行します。

```powershell
Install-Script UnConfigureWVDSSO
UnConfigureWVDSSO.ps1 -WvdWebAppAppIDUri "<WVD Web App URI>" -WvdClientAppApplicationID "a85cf173-4192-42f8-81fa-777a763e6e2c"
```

> [!NOTE]
> WvdWebAppAppIDUri プロパティは、デプロイ先の Azure クラウドと一致している必要があります。 Azure 商用クラウドでは、このプロパティは `https://www.wvd.microsoft.com` です。 これは、Azure Government クラウドのような他のクラウドでは異なります。

## <a name="next-steps"></a>次のステップ

シングル サインオンを構成したので、サポートされている Azure Virtual Desktop クライアントにサインインし、ユーザー セッションの一部としてテストできます。 新しい資格情報を使用してセッションに接続する方法については、こちらの記事を参照してください。

* [Windows デスクトップ クライアントを使用して接続する](./user-documentation/connect-windows-7-10.md)
* [Web クライアントに接続する](./user-documentation/connect-web.md)