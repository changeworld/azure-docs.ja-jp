<properties
	pageTitle="Azure Active Directory ドメイン サービスのプレビュー: 作業の開始 | Microsoft Azure"
	description="Azure Active Directory ドメイン サービスの概要"
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
	ms.topic="get-started-article"
	ms.date="04/11/2016"
	ms.author="maheshu"/>

# Azure AD ドメイン サービス *(プレビュー)* - 作業の開始

## 手順 4: Azure 仮想ネットワークの DNS 設定を更新する
ディレクトリの Azure AD ドメイン サービスが正常に有効化されました。次に、仮想ネットワーク内のコンピューターがこれらのサービスに接続して使用できるようにします。そのためには、仮想ネットワーク上で Azure AD ドメイン サービスを使用できる IP アドレスを指すように、仮想ネットワークの DNS サーバー設定を更新する必要があります。

> [AZURE.NOTE] ディレクトリの Azure AD ドメイン サービスを有効にしたら、ディレクトリの **[構成]** タブに表示される Azure AD ドメイン サービスの IP アドレスをメモしておきます。

Azure AD ドメイン サービスを有効にした仮想ネットワークの DNS サーバー設定を更新するには、次の構成手順を実行します。

1. **Azure 管理ポータル** ([https://manage.windowsazure.com](https://manage.windowsazure.com)) に移動します。
2. 左側のウィンドウで、**[ネットワーク]** ノードを選択します。

    ![[Virtual Networks] ノード](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. **[Virtual Networks]** タブで、Azure AD ドメイン サービスを有効にした仮想ネットワークを選択してプロパティを表示します。
4. **[構成]** タブをクリックします。

    ![[Virtual Networks] ノード](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. **[DNS サーバー]** セクションで、Azure AD ドメイン サービスの IP アドレスを入力します。
6. ディレクトリの **[構成]** タブの **[ドメイン サービス]** セクションに表示されていた両方の IP アドレスが入力されていることを確認します。
7. この仮想ネットワークの DNS サーバー設定を保存するには、ページの下部にある作業ウィンドウで **[保存]** をクリックします。

   ![仮想ネットワークの DNS サーバー設定を更新します。](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] 仮想ネットワークの DNS サーバー設定の更新後、ネットワーク上の仮想マシンが、更新された DNS 構成を取得するのにしばらく時間がかかる場合があります。仮想マシンがドメインに接続できない場合は、仮想マシンに DNS 設定の更新を適用するために、仮想マシンの DNS キャッシュをフラッシュすることができます (例: ipconfig/flushdns)。

---
[**次のステップ - Azure AD ドメイン サービスに対してパスワード同期を有効にします。**](active-directory-ds-getting-started-password-sync.md)

<!---HONumber=AcomDC_0420_2016-->