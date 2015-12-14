<properties
	pageTitle="Azure AD の用語 | Microsoft Azure"
	description="Azure Active Directory に関する用語と定義"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="curtand"/>

# Azure AD の用語

Microsoft Azure Active Directory (Azure AD) には、クラウド、ハイブリッド、オンプレミスでの使用に関する独自の用語がいくつかあります。次の表ではこれらの用語が定義されているので、使用されている用語の基礎情報としてご利用ください。

 用語 | 定義
------------- | -------------
追加のセキュリティ確認 | グローバル管理者が組織のディレクトリのユーザー アカウントに設定できるセキュリティ設定で、ユーザーのパスワードおよび電話の応答の両方を使用して Azure Active Directory 認証システムで ID 検証することが求められます。
Azure Active Directory | Azure の ID サービスで、REST ベースの API によって ID 管理およびアクセス制御の機能を提供します。
Azure Active Directory Access Control | REST Web サービスに対して、フェデレーション認証およびルール主導でクレーム ベースの承認を実行する Azure サービスです。
Azure Active Directory 認証システム | Microsoft のクラウドにおける ID サービスで、会社または学校のアカウントの認証と承認に使用されます。
Azure Active Directory Graph | Azure Active Directory の機能で、ソーシャル エンタープライズ グラフ内のユーザー、グループ、およびロールのオブジェクトにアクセスして、ユーザーの情報および関係を簡単に表示します。
Windows PowerShell 用 の Azure Active Directory モジュール | Azure Active Directory の管理に使用されるコマンドレットのグループです。これらのコマンドレットを使用すると、ユーザー、グループ、ドメイン、クラウド サービスのサブスクリプション、ライセンス、ディレクトリ同期、シングル サインオンなどを管理できます。
Azure Active Directory Connect | Azure Active Directory Connect ウィザードは、オンプレミスのディレクトリと Azure Active Directory を簡単な操作で連携できるツールです。このウィザードでは、同期サービス、パスワードの同期や Active Directory フェデレーション サービス (AD FS)、および Azure AD PowerShell モジュールなどの前提条件といった、ディレクトリ統合を開始するのに必要なすべてのコンポーネントをデプロイして構成できます。
Azure Active Directory 同期ツール | 企業のオンプレミス Active Directory サービスから Azure Active Directory への一方向のディレクトリ オブジェクト同期を実行するアプリケーションです。
ディレクトリ統合 | オンプレミス ディレクトリとクラウド ディレクトリの両方での ID 管理について、管理者エクスペリエンスを向上させるために設定できる Azure Active Directory の機能です。ディレクトリ統合のシナリオには、ディレクトリ同期およびシングル サインオンとのディレクトリ同期が含まれます。
ディレクトリ同期 | オンプレミス ディレクトリ オブジェクト (ユーザー、グループ、連絡先) をクラウドに同期させるのに使用され、管理オーバーヘッドの削減に役立ちます。ディレクトリ同期 (directory synchronization) は Azure AD ポータルおよび Azure クラシック ポータルでは directory sync とも呼ばれます。ディレクトリ同期を設定すると、管理者は Active Directory のオンプレミス ディレクトリ オブジェクトを Azure AD インスタンスにプロビジョニングできます。
Microsoft Online Services サインイン アシスタント | サインイン アシスタントはクライアント コンピューター上にインストールされているアプリケーションで、サインイン アシスタントを使用すると、ユーザーはそのコンピューターに 1 回サインインするだけで、サインイン セッションの間に何回でもサービスにアクセスできます。サインイン アシスタントがインストールされていないと、エンド ユーザーはサービスにアクセスを試みるたびに名前とパスワードを入力する必要があります。サインイン アシスタントとシングル サインオンは異なるのでご注意ください。シングル サインオンは Azure Active Directory のディレクトリ統合機能で、ユーザーの既存のオンプレミス企業資格情報を使用した Microsoft クラウド サービスへのシームレスなアクセスを可能にします。
Multi-factor authentication (2 要素認証または 2FA とも呼ばれます) | Multi-Factor Authentication を使用すると、ユーザーのサインインおよびトランザクションに重要な第 2 のセキュリティ層が追加されます。Azure AD のユーザー アカウントで Multi-Factor Authentication を有効にすると、そのユーザーは、組織がサブスクライブしている Microsoft クラウド サービスにサインインして使用するたびに、標準のパスワード資格情報だけでなく、追加のセキュリティ検証方法として、電話を使用する必要があります。
シングル サインオン | シングル サインオンを使用すると、ユーザーが企業ネットワークにログオンした状態で Microsoft クラウド サービスにアクセスする場合に、よりシームレスな認証が実行されます。シングル サインオンをセットアップするには、組織内に Security Token Service をデプロイする必要があります。シングル サインオンをセットアップすると、ユーザーは Active Directory の企業資格情報 (ユーザー名とパスワード) を使用して、クラウド内のサービスおよび既存のオンプレミス リソースにアクセスできます。
ユーザー ID | ユーザー ID は、組織がサブスクライブしている Microsoft のクラウド サービスにアクセスするために、ユーザーがサインイン ページで入力する一意識別子です。
会社または学校のアカウント | 組織 (企業、学校、非営利) によってその構成員 (従業員、学生、顧客) に割り当てられるユーザー アカウントであり、組織がサブスクライブしている 1 つ以上の Microsoft クラウド サービス (Office 365 や Azure など) にサインインしてアクセスできます。これらのアカウントは組織の Azure AD のディレクトリに格納され、通常、ユーザーが組織に在籍しなくなると削除されます。Microsoft アカウントとは異なり、会社または学校のアカウントは、ユーザーではなく組織の管理者によって作成および管理されます。

## 参照トピック
- [Azure への組織としてのサインアップ](sign-up-organization.md)
- [Azure サブスクリプションを Azure AD に関連付ける方法](active-directory-how-subscriptions-associated-directory.md)
- [Azure AD サービスの制限と制約](active-directory-service-limits-restrictions.md)

<!---HONumber=AcomDC_1203_2015-->