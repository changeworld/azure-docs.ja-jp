<properties
   pageTitle="Azure AD Connect のトポロジ | Microsoft Azure"
   description="このトピックでは、Azure AD Connect のサポートされているトポロジとサポートされていないトポロジについて詳しく説明します。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/08/2015"
   ms.author="andkjell"/>

# Azure AD Connect のトポロジ

このトピックでは、主要な統合ソリューションとして Azure AD Connect Sync を使用した、さまざまなオンプレミスと Azure AD のトポロジについて説明します。サポートされている構成とサポートされていない構成の両方について説明します。

ドキュメント内の図の凡例:

| 説明 | アイコン |
|-----|-----|
| オンプレミスの Active Directory フォレスト | ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)|
| Active Directory とフィルター処理されたインポート | ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)|
| Azure AD Connect 同期サーバー | ![同期](./media/active-directory-aadconnect-topologies/LegendSync1.png)|
| Azure AD Connect 同期サーバー "ステージング モード" | ![同期](./media/active-directory-aadconnect-topologies/LegendSync2.png)|
| GALSync と FIM2010 または MIM2016 | ![同期](./media/active-directory-aadconnect-topologies/LegendSync3.png)|
| Azure AD Connect 同期サーバー、詳細 |![同期](./media/active-directory-aadconnect-topologies/LegendSync4.png)|
| Azure AD ディレクトリ |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)|
| サポートされていないシナリオ | ![サポートされていません](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)



## 単一のフォレスト、単一の Azure AD ディレクトリ
![SingleForestSingleDirectory](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

最も一般的なトポロジは、オンプレミスの 1 つのフォレスト (1 つまたは複数のドメイン) と、1 つの Azure AD ディレクトリ ("テナント" と呼ばれます) です。Azure AD 認証は、パスワード同期で行われます。これは、Azure AD Connect の高速インストールでサポートされているトポロジです。

### 単一のフォレスト、1 つの Azure AD ディレクトリに対する複数の同期サーバー
![SingleForestFilteredUnsupported](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

同じ Azure AD ディレクトリに対する複数の Azure AD Connect 同期サーバーの接続は、同期サーバーが相互に排他的な一連のオブジェクトを同期するように構成されている場合でもサポートされません ([ステージング サーバー](#staging-server)は除きます)。一般的なネットワークの場所からフォレスト内の 1 つのドメインにアクセスできないために失敗するか、複数のサーバーに同期の負荷を分散しようとして失敗します。

## 複数のフォレスト、単一の Azure AD ディレクトリ
![MultiForestSingleDirectory](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

多くの組織が、複数のオンプレミス Active Directory フォレストを含む環境を使用しています。複数のオンプレミス Active Directory フォレストがデプロイされる理由はさまざまです。典型的な例は、アカウント リソース フォレスト、合併や買収に関連したフォレスト、データの外注に使用されるフォレストなどの目的です。

複数のフォレストがある場合は、1 つの Azure AD Connect 同期サーバーがすべてのフォレストにアクセスできる必要があります。サーバーはドメインに参加する必要がなく、すべてのフォレストにアクセスできる必要がある場合は、ネットワーク DMZ に配置できます。

Azure AD Connect ウィザードには、ユーザーを統合する方法として、いくつかのオプションが用意されています。そのため、同じユーザーがいくつかのフォレストにわたって複数回表されている場合でも、ユーザーは Azure AD で 1 回だけ表されます。次に示すように、いくつかの一般的なトポロジがあります。インストール ウィザードのカスタム インストール パスで、どのトポロジを使用するかを構成し、[ユーザーを一意に識別] ページで対応するオプションを選択します。統合は、ユーザーに対してだけ行われます。グループが重複している場合、それらは既定の構成に統合されません。

一般的なトポロジである[分離トポロジ](#multiple-forests-separate-topologies)、[フル メッシュ](#multiple-forests-full-mesh-with-optional-galsync)、および[アカウント リソース](#multiple-forests-account-resource-forest)については、次のセクションで説明します。

Azure AD Connect Sync で提供される既定の構成は、次の内容を前提としています。1.ユーザーが持つ有効なアカウントは 1 つのみで、このアカウントが配置されているフォレストがユーザーの認証に使用されます。これには、パスワード同期とフェデレーションの両方が該当します。userPrincipalName と sourceAnchor/immutableID は、このフォレストから取得されます。2.ユーザーのメールボックスは 1 つのみです。3.ユーザーのメールボックスをホストするフォレストは、Exchange のグローバル アドレス一覧 (GAL) で確認できる属性に対して最適なデータ品質を備えています。ユーザーにメールボックスがない場合、どのフォレストを使用してもこれらの属性値を提供できます。4.リンクされたメールボックスがある場合は、ログインに使用される別のアカウントも別のフォレストにあります。

環境がこれらの前提と一致しない場合は、次のようになります。複数のアクティブなアカウントまたは複数のメールボックスがある場合、同期エンジンは 1 つを選び、他は無視します。リンクされたメールボックスがあり、他にはアカウントがない場合、これらのアカウントは Azure AD にエクスポートされず、ユーザーはどのグループのメンバーにもなりません。DirSync では、リンクされたメールボックスは通常のメールボックスとして表されるので、これはマルチ フォレスト シナリオをよりよくサポートするための意図的に異なる動作です。

### 複数のフォレスト、1 つの Azure AD ディレクトリに対する複数の同期サーバー
![MultiForestMultiSyncUnsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

1 つの Azure AD ディレクトリへの複数の Azure AD Connect 同期サーバーの接続はサポートされません ([ステージング サーバー](#staging-server)は除きます)。

### 複数のフォレスト – 分離トポロジ
[ユーザーは、すべてのディレクトリで 1 度だけ示されます。]

![MultiForestUsersOnce](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![MultiForestSeperateTopologies](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

この環境では、すべてのオンプレミス フォレストは個別のエンティティと見なされ、その他のフォレストにユーザーは存在しません。各フォレストには独自の Exchange 組織があり、フォレスト間に GALSync はありません。これは、合併や買収の後の組織や各部署が互いに独立して運用されている組織に見受けられます。Azure AD では、これらのフォレストは同じ組織内にあり、統合された GAL で表示されます。この図では、すべてのフォレスト内の各オブジェクトが、一度メタバースに表され、ターゲットの Azure AD ディレクトリに集約されます。

### 複数のフォレスト - ユーザーの一致
[複数のディレクトリにユーザー ID が存在します] のいずれかのオプションを選択した場合に、すべてのマルチ フォレスト シナリオに共通するのは、配布とセキュリティ グループがすべてのフォレストで見つかり、ユーザー、連絡先、および FSP (外部セキュリティ プリンシパル) の組み合わせを含めることができるということです。

FSP は、セキュリティ グループ内の他のフォレストのメンバーを表すために、ADDS で使用されます。同期エンジンは FSP を実際のユーザーに解決し、Azure AD 内のセキュリティ グループを、実際のオブジェクトに解決されるすべての FSP で表します。

### 複数のフォレスト - オプションの GALSync を使用したフル メッシュ
[複数のディレクトリにユーザー ID が存在します。次を使用した一致: メール属性]

![MultiForestUsersMail](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![MultiForestFullMesh](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

フル メッシュ トポロジでは、ユーザーとリソースを任意のフォレストに置くことができ、一般的にフォレスト間には双方向の信頼があります。

複数のフォレストに Exchange が存在する場合、必要に応じてオンプレミス GALSync ソリューションが存在し、1 つのフォレスト内のユーザーが他のすべてのフォレストにおける連絡先として表されることがあります。GALSync は、通常、Forefront Identity Manager 2010 または Microsoft Identity Manager 2016 を使用して実装されます。オンプレミス GALSync では、Azure AD Connect は使用できません。

このシナリオでは、ID オブジェクトはメール属性を使用して結合されます。この結果、あるフォレストでメールボックスを使用するユーザーは、他のフォレストの連絡先と結合されます。

### 複数のフォレスト – アカウント リソース フォレスト
[複数のディレクトリにユーザー ID が存在します。次を使用した一致: ObjectSID および msExchMasterAccountSID 属性]

![MultiForestUsersObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![MultiForestAccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

アカウント リソース フォレスト トポロジでは、アクティブなユーザー アカウントを使用する 1 つ以上のアカウント フォレストが存在します。

このシナリオは、すべてのアカウント フォレストを信頼する 1 つのフォレストを含んでいます。このフォレストには、通常、Exchange および Lync を使用する拡張 AD スキーマがあります。すべての Exchange および Lync サービスと、他の共有サービスは、このフォレストに配置されます。ユーザーのユーザー アカウントはこのフォレストで無効になり、メールボックスはアカウント フォレストにリンクされます。

## Office 365 とトポロジの考慮事項
Office 365 の一部のワークロードでは、サポートされるトポロジに一定の制限が生じます。これらのいずれかを使用する場合は、各ワークロードのサポートされているトポロジのページを参照してください。

| ワークロード | |
| --------- | --------- |
| Exchange Online |	オンプレミスに複数の Exchange 組織がある場合 (つまり、Exchange が複数のフォレストにデプロイされている場合) は、Exchange 2013 SP1 以降を使用する必要があります。詳細については「[複数の Active Directory フォレストを伴うハイブリッド展開](https://technet.microsoft.com/ja-JP/library/jj873754.aspx)」を参照してください。 |
| Skype for Business | オンプレミスで複数のフォレストを使用する場合は、アカウント リソース フォレスト トポロジのみがサポートされます。サポートされているトポロジの詳細については、「[Skype for Business Server 2015 の環境要件](https://technet.microsoft.com/ja-JP/library/dn933910.aspx)」を参照してください。 |

## ステージング サーバー
![StagingServer](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect では、"ステージング モード" でのセカンド サーバーのインストールがサポートされています。このモードのサーバーは、すべての接続されているディレクトリからデータの読み取りだけを行い、ID データの更新されたコピーを持ちます。障害が発生し、プライマリ サーバーがエラーになった場合は、Azure AD Connect ウィザードを使用して、セカンド サーバーに手動で簡単にフェールオーバーできます。このセカンド サーバーは、別のデータ センターに配置することをお勧めします。そうすれば、インフラストラクチャをプライマリ サーバーと共有することがありません。プライマリ サーバーでの構成の変更は、ユーザーがセカンド サーバーにコピーする必要があります。

ステージング サーバーは、新しいカスタム構成と、それがデータに与える影響をテストする場合にも使用できます。変化をプレビューし、構成を調整できます。新しい構成に問題がなければ、ステージング サーバーをアクティブ サーバーにし、元のアクティブ サーバーをステージング モードに設定できます。

この方法は、同期サーバーを交換し、現在アクティブなサーバーをシャットダウンする前に、新しいサーバーを準備する場合にも使用できます。

異なるデータ センターに複数のバックアップを用意する場合は、複数のステージング サーバーを持つことができます。

## 複数の Azure AD ディレクトリ
組織の Azure AD には 1 つのディレクトリを置くことをお勧めします。複数の Azure AD ディレクトリの使用を計画する前に、これらのトピックでは 1 つのディレクトリを使用できる一般的なシナリオについて説明しています。

| トピック | |
| --------- | --------- |
| 管理単位を使用した委任 | [Azure AD の管理単位](active-directory-administrative-units-management.md)

![MultiForestMultiDirectory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Azure AD Connect 同期サーバーと Azure AD ディレクトリには、1:1 のリレーションシップがあります。各 Azure AD ディレクトリに、1 つの Azure AD Connect 同期サーバーをインストールする必要があります。各 Azure AD ディレクトリ インスタンスは分離されるデザインになっており、1 つのディレクトリのユーザーは他のディレクトリ内のユーザーを見ることができません。これが意図しているものであれば、サポートされている構成ですが、そうでない場合は上記の単一 Azure AD ディレクトリ モデルを使用する必要があります。

### Azure AD ディレクトリでの各オブジェクトの 1 回のみの使用
![SingleForestFiltered](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

このトポロジでは、1 つの AAD Connect 同期サーバーが各 Azure AD ディレクトリに接続されます。各 Azure AD Connect 同期サーバーは、操作対象のオブジェクトのセットが相互排他的になるようなフィルター処理を構成する必要があります。たとえば、各サーバーが特定のドメインまたは OU をスコープにするようにします。DNS ドメインは 1 つの Azure AD ディレクトリにしか登録できないため、オンプレミス AD のユーザーの UPN も個別の名前空間を使用する必要があります。たとえば、上の図では、3 つの個別の UPN サフィックスがオンプレミス AD の contoso.com、fabrikam.com、および wingtiptoys.com に登録されています。各オンプレミス AD ドメインのユーザーは、別の名前空間を使用します。

このトポロジでは、Azure AD ディレクトリ インスタンス間に GALsync がないため、Exchange Online および Skype for Business のアドレス帳は同じディレクトリ内のユーザーだけを表示します。

このトポロジでは、1 つの Azure AD ディレクトリだけが、オンプレミス Active Directory を持つ Exchange ハイブリッドを有効にできます。

オブジェクトの相互排他的なセットの要件は、書き戻しにも適用されます。そのため、単一構成のオンプレミスを前提としている一部の書き戻し機能は、このトポロジではサポートされません。そうした機能として、既定の構成でのグループの書き戻し、デバイスの書き戻しなどがあります。

### Azure AD ディレクトリでの各オブジェクトの複数の使用
![SingleForestMultiDirectoryUnsupported](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![SingleForestMultiConnectorsUnsupported](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

複数の Azure AD ディレクトリへの同じユーザーの同期は、サポートされていません。1 つの Azure AD のユーザーを他の Azure AD ディレクトリで連絡先として表示するような構成の変更もサポートされていません。複数の Azure AD ディレクトリに接続するような Azure AD Connect Sync の変更もサポートされていません。

### 書き戻しの使用による GALsync
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD のディレクトリは、分離するように設計されています。ディレクトリ間に共通の統合された GAL を構築しようとする際に他の Azure AD ディレクトリからデータを読み取るような、Azure AD Connect Sync の構成の変更はサポートされていません。Azure AD Connect Sync を使用している別のオンプレミス AD の連絡先としてユーザーをエクスポートすることもサポートされていません。

### GALsync とオンプレミスの同期サーバー
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

2 つの Exchange 組織間でユーザーを GALsync するための、オンプレミスでの FIM2010/MIM2016 の使用はサポートされています。1 つの組織内のユーザーは、他の組織では外部ユーザーおよび連絡先として表示されます。これらの異なるオンプレミス AD は、独自の Azure AD ディレクトリと同期できます。

## 次のステップ
これらのシナリオのために Azure AD Connect をインストールする方法については、「[Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」を参照してください。Azure AD Connect Sync の構成の詳細については、「[Azure AD Connect Sync: 同期オプションのカスタマイズ](active-directory-aadconnectsync-whatis.md)」を参照してください。

<!---HONumber=Sept15_HO4-->