<properties
	pageTitle="Azure AD ドメイン サービス - Azure AD ドメイン サービスを有効にする | Microsoft Azure"
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
	ms.date="09/21/2016"
	ms.author="maheshu"/>

# Azure AD Domain Services を有効にする

## タスク 3: Azure AD ドメイン サービスを有効にする
このタスクではディレクトリに対して Azure AD Domain Services を有効にします。ディレクトリに対して Azure AD Domain Services を有効にするには、次の構成手順を実行します。

1. **Azure クラシック ポータル** ([https://manage.windowsazure.com](https://manage.windowsazure.com)) に移動します。

2. 左ウィンドウで、**[Active Directory]** を選択します。

3. Azure AD Domain Services を有効にする Azure AD テナント (ディレクトリ) を選択します。

    ![Azure AD ディレクトリの選択](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. [**構成**] タブをクリックします。

    ![ディレクトリの [構成] タブ](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. **[domain services]** というタイトルのセクションまで下にスクロールします。

    ![Domain Services 構成セクション](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. **[このディレクトリのドメイン サービスを有効にします]** というタイトルのオプションを **[はい]** に切り替えます。ページに Azure AD Domain Services 用の構成オプションがさらにいくつかページに表示されます。

    ![Domain Services の有効化](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] テナントに対して Azure AD Domain Services を有効にすると、ユーザーを認証するために必要な Kerberos と NTLM の資格情報のハッシュが Azure AD によって生成され、保存されます。

7. **[ドメイン サービスの DNS ドメイン名]** を指定します。

   - ディレクトリの既定のドメイン名 (**.onmicrosoft.com** ドメイン サフィックスで終わる名前) が既定で選択されます。

   - 一覧には、Azure AD ディレクトリに対して構成されたすべてのドメインが含まれます。検証対象のドメインおよび [ドメイン] タブで構成する検証対象外のドメインなどがあります。

   - さらに、この一覧には、カスタム ドメイン名を入力して追加することもできます。この例では、'contoso100.com' というカスタム ドメイン名を入力しました

     > [AZURE.WARNING] 指定したドメイン名のドメイン プレフィックス (たとえば、"contoso100.com" ドメイン名の "contoso100") が 15 文字未満であることを確認します。ドメイン プレフィックスが 15 文字より長いと、Azure AD Domain Services ドメインを作成することはできません。

8. 次の手順は、Azure AD Domain Services を利用できる仮想ネットワークを選択することです。作成した仮想ネットワークを、**[ドメイン サービスをこの仮想ネットワークに接続します]** というタイトルのボックスの一覧から選択します。

   - 指定した仮想ネットワークが Azure AD Domain Services でサポートされている Azure リージョンに属することを確認します。

   - Azure AD Domain Services を使用できる Azure リージョンを確認するには、[リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services/)に関するページを参照してください。

   - Azure AD Domain Services がサポートされていないリージョンに属している仮想ネットワークはドロップダウン リストに表示されません。

   - 同様に、Azure Resource Manager を使用して作成された仮想ネットワークは、ドロップダウン リストに表示されません。現在、Resource Manager ベースの仮想ネットワークは Azure AD Domain Services でサポートされていません。

9. 管理対象ドメイン用に選択した DNS ドメイン名がまだ仮想ネットワークに存在しないことを確認します。具体的には、以下のことを確認します。

   - 同じ DNS ドメイン名のドメインが仮想ネットワーク上に既にあるかどうか。

   - 選択した仮想ネットワークにオンプレミス ネットワークとの VPN 接続があり、オンプレミス ネットワーク上に同じ DNS ドメイン名のドメインがあるかどうか。

   - 仮想ネットワーク上に、その名前の付いたクラウド サービスが既にあるかどうか。

10. Azure AD Domain Services を有効にするには、ページ下部のタスク ウィンドウで **[保存]** をクリックします。

11. ディレクトリに対して Azure AD Domain Services を有効にしている間、ページは保留状態になり、[保留中] と表示されます。

    ![Domain Services の有効化 - 保留中状態](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Azure AD Domain Services は、管理対象ドメインに対して高可用性を提供します。Azure AD Domain Services を有効にすると、仮想ネットワーク上で Domain Services を利用できる IP アドレスが 1 つずつ表示されます。2 番目の IP アドレスは、このサービスでドメインに対する高可用性が有効になるとすぐに表示されます。ドメインに対する高可用性が構成され、アクティブになると、**[構成]** タブの **[domain services]** セクションに 2 つの IP アドレスが表示されます。

12. 約 20 ～ 30 分後に、仮想ネットワーク上で Domain Services を利用できる最初の IP アドレスが、**[構成]** ページの **[IP アドレス]** フィールドに表示されます。

    ![Domain Services が有効化された - 最初の IP がプロビジョニングされた](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. 高可用性がドメインで運用されると、2 つの IP アドレスがページに表示されます。これらは、選択した仮想ネットワーク上で Azure AD Domain Services を利用できる IP アドレスです。仮想ネットワークの DNS 設定を更新できるように、これらの IP アドレスをメモしておきます。この手順は、仮想ネットワーク上の仮想マシンが、ドメインへの参加などの操作のためにドメインに接続できるようにします。

    ![Domain Services が有効化された - 2 つの IP がプロビジョニングされた](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] Azure AD テナントのサイズ (ユーザーやグループの数など) によっては、Azure AD Domain Services でテナントの内容が利用できるようになるまで、多少時間がかかります。この同期処理は、バック グラウンドで発生します。数万のオブジェクトがある大きなテナントの場合、すべてのユーザー、グループ メンバーシップ、資格情報が Azure AD Domain Services で利用できるようになるまで、1 ～ 2 日かかることがあります。

<br>

## タスク 4: Azure 仮想ネットワークの DNS 設定を更新する
次に、[Azure 仮想ネットワークの DNS 設定を更新](active-directory-ds-getting-started-dns.md)します。

<!---HONumber=AcomDC_0928_2016-->