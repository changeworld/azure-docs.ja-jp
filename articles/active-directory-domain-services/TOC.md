# [Azure AD Domain Services のドキュメント](index.yml)

# 概要
## [Azure AD Domain Services とは](active-directory-ds-overview.md)
## 適しているかの判断
### [Windows Server AD との比較](active-directory-ds-comparison.md)
### [Azure AD Join との比較](active-directory-ds-compare-with-azure-ad-join.md)
## [新機能](https://azure.microsoft.com/updates/?product=active-directory-ds)
## [機能](active-directory-ds-features.md)
## [シナリオ](active-directory-ds-scenarios.md)
## [同期のしくみ](active-directory-ds-synchronization.md)
## [互換性のあるサードパーティ製ソフトウェア](active-directory-ds-compatible-software.md)

# 作業開始
## [タスク 1: 基本設定を構成する](active-directory-ds-getting-started.md)
## [タスク 2: ネットワーク設定を構成する](active-directory-ds-getting-started-network.md)
## [タスク 3: 管理者グループを構成して Azure AD Domain Services を有効にする](active-directory-ds-getting-started-admingroup.md)
## [タスク 4: 仮想ネットワークの DNS 設定を更新する](active-directory-ds-getting-started-dns.md)
## [タスク 5: パスワード ハッシュ同期を有効にする](active-directory-ds-getting-started-password-sync.md)

# 方法
## [管理対象ドメインの正常性のチェック](active-directory-ds-check-health.md)
## [Azure CSP サブスクリプションで Azure AD Domain Services を使用する](active-directory-ds-csp.md)
## [PowerShell を使用して Azure AD Domain Services を有効にする](active-directory-ds-enable-using-powershell.md)
## 管理対象ドメインに参加する
### [Windows Server VM](active-directory-ds-admin-guide-join-windows-vm-portal.md)
### [テンプレートによる Windows Server VM](active-directory-ds-join-windows-vm-template.md)
### [CentOS](active-directory-ds-join-centos-linux-vm.md)
### [CoreOS](active-directory-ds-join-coreos-linux-vm.md)
### [Red Hat Enterprise Linux](active-directory-ds-join-rhel-linux-vm.md)
### [Ubuntu Server](active-directory-ds-join-ubuntu-linux-vm.md)
## 管理対象ドメインを管理する
### [管理対象ドメインを管理する](active-directory-ds-admin-guide-administer-domain.md)
### [管理対象ドメインで DNS を管理する](active-directory-ds-admin-guide-administer-dns.md)
### 管理対象ドメインのセキュリティで保護された LDAP を構成する
#### [タスク 1: セキュリティで保護された LDAP 用の証明書を取得する](active-directory-ds-admin-guide-configure-secure-ldap.md)
#### [タスク 2: セキュリティで保護された LDAP 証明書をエクスポートする](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
#### [タスク 3: Azure Portal を使用して、管理対象ドメインに対してセキュリティで保護された LDAP を有効にする](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)

### [管理されたドメインで OU を作成する](active-directory-ds-admin-guide-create-ou.md)
### [管理対象ドメインでグループ ポリシーを管理する](active-directory-ds-admin-guide-administer-group-policy.md)
## [仮想ネットワークを選択する](active-directory-ds-networking.md)
## アプリケーションをデプロイする
### [SharePoint Server のプロファイルの同期のサポートを構成する](active-directory-ds-enable-sharepoint-profile-sync.md)
### [Kerberos の制約付き委任を構成する](active-directory-ds-enable-kcd.md)
### [Azure AD アプリケーション プロキシをデプロイする](active-directory-ds-deploy-azure-app-proxy.md)
## [管理対象ドメインを削除する](active-directory-ds-disable-aadds.md)
## トラブルシューティング
### [FAQ](active-directory-ds-faqs.md)
### [トラブルシューティング ガイド](active-directory-ds-troubleshooting.md)
### [アラートのトラブルシューティング](active-directory-ds-troubleshoot-alerts.md)
#### [壊れた NSG 構成の修正](active-directory-ds-troubleshoot-nsg.md)
#### [不足しているサービス プリンシパルの復元](active-directory-ds-troubleshoot-service-principals.md)
#### [Secure LDAP のエラー](active-directory-ds-troubleshoot-ldaps.md)
### [一致しないテナントのエラーを解決する](active-directory-ds-mismatched-tenant-error.md)


# リファレンス
## [コード サンプル](https://azure.microsoft.com/resources/samples/?service=active-directory)

# 関連項目
## [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
## [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)
## [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)

# リソース
## [Azure AD フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=security-identity)
## [お問い合わせ](active-directory-ds-contact-us.md)
## [料金](https://azure.microsoft.com/pricing/details/active-directory-ds/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=active-directory-ds)
