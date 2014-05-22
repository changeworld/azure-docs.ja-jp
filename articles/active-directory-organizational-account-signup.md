<properties umbracoNaviHide="0" pageTitle="クラウド サービスの構成方法" metaKeywords="Azure クラウド サービス, クラウド サービス, 構成 クラウド サービス" description="Azure のクラウド サービスの構成方法について説明します。" linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="方法: ストレージ アカウント" headerExpose="" footerExpose="" disqusComments="1" title="Azure への組織としてのサインアップ" authors="" />



#Azure への組織としてのサインアップ

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

最近まで、新しい Azure サブスクリプションにサインアップするには、必ず Microsoft アカウント (Windows Live ID) を使用する必要がありました。現在、Azure では、次に示す 2 つのアカウントのいずれかを使用してサインアップすることができます。

* **Microsoft アカウント** (個人ユーザー向け) - Outlook (Hotmail)、Messenger、SkyDrive、MSN、Xbox LIVE、Office Live など、すべてのコンシューマー向け Microsoft 製品およびクラウド サービスへのアクセスを可能にします。Outlook にサインアップすると、@Outlook.com アドレスの Microsoft アカウントが自動的に作成されます。一度作成されら、その Microsoft アカウントを使用して、コンシューマー関連の Microsoft クラウド サービスや Azure にアクセスできます。[詳細情報](http://windows.microsoft.com/ja-jp/windows-live/sign-in-what-is-microsoft-account)

* **組織アカウント** (ビジネス/教育機関用に管理者が発行) - あらゆるレベルのビジネスを対象とした、Azure、Windows Intune、Office 365 など、すべての中小およびエンタープライズ ビジネス レベルの Microsoft クラウド サービスに対するアクセスを可能にします。これらのサービスのいずれかに組織としてサインアップすると、クラウド ベースのテナントが、Azure の Active Directory で組織を表すように自動的にプロビジョニングされます。[詳細情報](http://technet.microsoft.com/ja-jp/library/jj573650)

	このテナントが作成されたら、管理者が各従業員/学生に組織アカウントを発行し、それぞれがアクセスする必要があるクラウド サービス (Azure など) のサブスクリプションに基づいて各アカウントにライセンスを付与できます。

	*組織として Azure にサインアップする必要がある場合は、***今すぐサインアップ**

<h2>Azure の Active Directory とは</h2>

Active Directory サービスは、顧客が Windows Server オペレーティング システムを介して利用し、それぞれの内部設置型ディレクトリを管理します。Azure の Active Directory (Azure AD) サービスもしくみはこれとほぼ同じです。つまり、Azure を介して利用し、組織のクラウド ディレクトリを管理できます。[詳細情報](http://technet.microsoft.com/ja-jp/library/hh967619)

Active Directory は組織のクラウド ディレクトリであるため、そのディレクトリのユーザー、クラウドに保存する情報、その情報の使用者または管理者、およびその情報にアクセスできるようにするアプリケーションまたはサービスを決定します。Azure AD を使用するときに、高い拡張性と可用性、および統合障害復旧を備えたクラウドで Active Directory を実行し続けながら、組織の情報のプライバシーおよびセキュリティに関する要件に十分に対応できるようにすることが Microsoft の務めです。

<h3>内部設置型 Active Directory との統合</h3>

組織が既に内部設置型 Active Directory を使用している場合は、ディレクトリ同期、シングル サインオンなどの Azure AD のディレクトリ統合機能を使用し、既存の内部設置型の ID をクラウドに拡張して、管理およびエンド ユーザー エクスペリエンスを強化できます。[詳細情報](http://technet.microsoft.com/ja-jp/library/jj573653)

