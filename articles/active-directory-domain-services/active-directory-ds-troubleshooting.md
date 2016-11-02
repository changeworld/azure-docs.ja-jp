<properties
    pageTitle="Azure Active Directory Domain Services: トラブルシューティング ガイド | Microsoft Azure"
    description="Azure AD ドメイン サービスのトラブルシューティング ガイド"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="maheshu"/>


# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services - トラブルシューティング ガイド
この記事では、Azure Active Directory (AD) ドメイン サービスの設定や管理の際に生じる可能性のある問題をトラブルシューティングするためのヒントを提供します。


### <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Azure AD ディレクトリの Azure AD Domain Services を有効にすることはできません
ディレクトリの Azure AD Domain Services を有効にしようとしてエラーが発生したり、 '無効' と表示されたりする場合は、次のトラブルシューティング手順を実行してください。

- その仮想ネットワークで使用できるドメインと同じ名前のドメインがないことを確認します。 たとえば、選択した仮想ネットワークで既に利用可能な "contoso.com" という名前のドメインがあると仮定します。 その後、その仮想ネットワークでこれと同じドメイン名 (つまり "contoso.com") で、Azure AD Domain Services の管理対象ドメインを有効にしようとします。 Azure AD Domain Services を有効にしようとすると、エラーが発生します。 このエラーの原因は、仮想ネットワークのドメイン名で名前が競合していることにあります。 この場合、Azure AD ドメイン サービスの管理対象ドメインを設定するには、別の名前を使用する必要があります。 または、既存のドメインのプロビジョニングを解除してから、Azure AD ドメイン サービスの有効化に進みます。

- Azure AD ディレクトリに、'Azure AD ドメイン サービス同期' という名前のアプリケーションがあるかどうかを確認します。 このアプリケーションが存在する場合は、削除して Azure AD Domain Services を再度有効にする必要があります。 アプリケーションの有無を確認し、存在した場合に削除する手順は、次のとおりです。

  1. **Azure クラシック ポータル** ([https://manage.windowsazure.com](https://manage.windowsazure.com)) に移動します。
  2. 左ウィンドウで、 **[Active Directory]** を選択します。
  3. Azure AD Domain Services を有効にする Azure AD テナント (ディレクトリ) を選択します。
  4. **[アプリケーション]** タブに移動します。
  5. ドロップダウンで **[自分の会社が所有するアプリケーション]** のオプションを選択します。
  6. **[Azure AD ドメイン サービス同期]**というアプリケーションを探します。 アプリケーションが存在する場合は、削除に進みます。
  7. アプリケーションが削除されたら、もう一度 Azure AD ドメイン サービスの有効化を試します。


### <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>ユーザーが Azure AD ドメイン サービスの管理対象ドメインにサインインできない
Azure AD テナントの 1 人または複数のユーザーが、新しく作成された管理対象ドメインにサインインすることができない場合、次のトラブルシューティングの手順を実行します。

- SAMAccountName 形式 ("CONTOSO\joeuser") ではなく UPN 形式 ('joeuser@contoso.com') など) でサインインを試みます。 場合によっては、UPN プリフィックスが極端に長いか、管理対象ドメイン上の別のユーザーと同じであるユーザーに対して SAMAccountName を自動的に生成できます。 UPN 形式は、Azure AD テナント内で一意であることが保証されています。

> [AZURE.NOTE] UPN 形式を使用して、Azure AD Domain Services の管理対象ドメインにサインインすることをお勧めします。

- ファースト ステップ ガイドで説明されている手順に従って [パスワード同期が有効になっている](active-directory-ds-getting-started-password-sync.md) ことを確認します。

- **外部アカウント:** 影響を受けているユーザーのアカウントが Azure AD テナントの外部アカウントでないことを確認します。 外部アカウントには、Microsoft アカウント ('joe@live.com') など) や外部の Azure AD ディレクトリのユーザー アカウントが含まれます。 Azure AD ドメイン サービスにはこのようなユーザー アカウントの資格情報がないため、これらのユーザーは管理対象ドメインにサインインできません。

- **同期されるアカウント:** 影響を受けているユーザー アカウントがオンプレミスのディレクトリから同期されている場合は、次について確認します。
    - [Azure AD Connect の最新の推奨リリース](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect)をデプロイまたは更新している。

    - [完全同期を実行](active-directory-ds-getting-started-password-sync.md)するように Azure AD Connect を構成している。

    - ディレクトリのサイズによっては、ユーザー アカウントと資格情報のハッシュを Azure AD ドメイン サービスで使用できるまで時間がかかる可能性があります。 十分な時間 (ディレクトリのサイズに応じて数時間から 1 ～ 2 日間) を待機してから認証を再試行してください。

    - 上記の手順を確認しても問題が解決しない場合は、Microsoft Azure AD 同期サービスを再起動してみてください。 同期コンピューターから、コマンド プロンプトを起動し、次のコマンドを実行します。
      1. net stop 'Microsoft Azure AD Sync'
      2. net start 'Microsoft Azure AD Sync'

- **クラウド専用のアカウント**: 影響を受けているユーザー アカウントがクラウド専用のユーザー アカウントの場合は、Azure AD ドメイン サービスを有効にした後でユーザーが自分のパスワードを変更していることを確認します。 この手順によって、Azure AD ドメイン サービスに必要な資格情報ハッシュが生成されます。


### <a name="contact-us"></a>お問い合わせ
[フィードバックの共有およびサポート] (active-directory-ds-contact-us.md) については、Azure Active Directory Domain Services 製品チームにお問い合わせください。



<!--HONumber=Oct16_HO2-->


