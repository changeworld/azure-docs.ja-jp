<properties 
	pageTitle="Azure AD Connect の管理" 
	description="Azure AD Connect の既定の構成と運用タスクを拡張する方法について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Azure AD Connect の管理 


<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/active-directory-aadconnect/" title="概要" class="current">概要</a> <a href="/ja-jp/documentation/articles/active-directory-aadconnect-how-it-works/" title="機能">機能</a> <a href="/ja-jp/documentation/articles/active-directory-aadconnect-get-started/" title="使用">使用</a> <a href="/ja-jp/documentation/articles/active-directory-aadconnect-whats-next/" title="次のトピック">次のトピック</a> <a href="/ja-jp/documentation/articles/active-directory-aadconnect-learn-more/" title="詳細">詳細</a>
</div>

このトピックは、組織のニーズや要件を満たすよう、Azure Active Directory Connect をカスタマイズするための高度な運用に関するトピックです。

## 既定の構成の変更
ほとんどの場合、Azure AD Connect の既定の構成で、簡単にオンプレミスのディレクトリをクラウドに拡張できます。ただし、既定値を変更し、組織のビジネス ロジックに合わせる必要がある場合もあります。このような場合には、既定の構成を変更できますが、実行する前に、いくつかの点に注意する必要があります。

アップグレードまたは Azure AD Sync や DirSync から移行する場合は、次の点に注意します。

- 新しいバージョンに Azure AD Sync をアップグレードすると、ほとんどの設定がリセットされ、既定値に戻されます。
- アップグレードを適用した後は、“標準” の同期規則の変更は失われます。
- 削除された “標準" の同期規則は、新しいバージョンへのアップグレード中に再作成されます。
- 新しいバージョンへのアップグレードが適用されているときは、作成したカスタムの同期規則は変更されません。

既定の構成を変更する必要がある場合は、以下の手順を実行します。

- “標準" の同期規則の属性のフローを変更する必要がある場合は、変更しません。代わりに、必要な属性のフローを含む、優先順位の高い (小さい数値の) 新しい同期規則を作成します。
- 同期規則エディターを使用して、カスタムの同期規則をエクスポートします。これにより、障害復旧シナリオの場合に、簡単に再作成をするために使用できる PowerShell スクリプトが提供されます。
- “標準" の同期規則のスコープ設定または結合の設定を変更する必要がある場合は、このことを文書化し、Azure AD Connect の新しいバージョンにアップグレードした後、変更を適用します。






 

## 同期規則エディターの使用

Azure AD Connect では、同期規則を構成することにより、Azure AD とオンプレミスのディレクトリとの間のオブジェクトと属性のフローを構成、調整できます。同期規則エディターを使用して、同期規則を構成できます。同期規則エディターは、Azure AD Connect のインストール時にインストールされます。このエディターを使用するには、Azure AD Connect のインストール時に指定した ADSyncAdmins グループまたは管理者グループのメンバーである必要があります。

以下のスクリーン ショットでは、高速インストールを使用して Azure AD Connect をインストールするときに、使用中の構成に対して作成されるすべての同期規則を示しています。表内の各行は 1 つの同期規則です。左側の [ルールの種類] の下に、[着信] と [送信] という 2 つの異なる種類が表示されます。[着信] および [送信] は、メタバースのビューに基づきます。つまり、情報をディレクトリからメタバースに取り込みます。[送信] は、オンプレミスの Active Directory または Azure AD などのディレクトリに情報と属性を送信します。

<center>![同期規則エディター](./media/active-directory-aadconnect-whats-next/Synch_Rule.png) </center>

新しい規則を作成するには、[新しい規則の追加] を選択し、規則を構成します。たとえば、オンプレミスのディレクトリ内のすべてのユーザーが、同じ電話番号のメタバース オブジェクトと結合する結合規則を作成するとします。このことを行うには、新しい規則を作成し、接続されているシステム (この場合は contoso.com)、接続されているシステム オブジェクトの種類、ユーザー、メタバース オブジェクトの種類、人、および結合のリンクの種類を指定します。

<center>![同期規則の作成](./media/active-directory-aadconnect-whats-next/synch2.png) </center>


次に、[結合規則] 画面の [ソース属性] で [telephoneNumber] を、[ターゲット属性] で [telephoneNumber] を指定します。これで終わりです。これで、結合規則を正常に作成できました。

<center>![結合規則](./media/active-directory-aadconnect-whats-next/synch3.png) </center>

同期規則エディターを使用して、既定の構成の外部にある他のビジネス ロジックを適用し、組織のニーズに合わせることができます。同期規則エディターの詳細については、「[既定の構成の理解](https://msdn.microsoft.com/library/azure/dn800963.aspx)」を参照してください。


## 宣言型のプロビジョニングの使用 
宣言型のプロビジョニングとは、“コード不要" のプロビジョニングで、同期規則エディターを使用してセットアップし、構成できます。このエディターを使用してセットアップして、独自のプロビジョニング規則を作成できます。

宣言型のプロビジョニングの重要な部分は、属性のフローで使用される式言語です。使用される言語は、Microsoft ® Visual Basic ® for Applications (VBA) のサブセットです。この言語は、Microsoft Office で使用され、VBScript の経験を持つユーザーも理解できます。宣言型のプロビジョニングの式言語は、関数を使用するだけであり、構造化言語ではありません。メソッドやステートメントはありません。代わりに、関数を入れ子にして、プログラム フローを記述します。

この式言語の詳細については、「[宣言のプロビジョニングの式の理解](https://msdn.microsoft.com/library/azure/dn801048.aspx)」を参照してください。

## その他のドキュメント
Azure AD Sync 用に作成されたドキュメントの一部は、Azure AD Connect と引き続き関連があり、Azure AD Connect に適用されます。Azure.com にこのドキュメントを提供するためにあらゆる努力を重ねていますが、このドキュメントの一部は、現在も MSDN の範囲のライブラリに掲載されています。その他のドキュメントについては、「[MSDN の Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx)」と「[MSDN の Azure AD Sync](https://msdn.microsoft.com/library/azure/dn790204.aspx)」を参照してください。

**その他のリソース**

* [クラウド内のオンプレミスの ID インフラストラクチャの使用](active-directory-aadconnect.md)
* [Azure AD Connect の動作](active-directory-aadconnect-how-it-works.md)
* [Azure AD Connect の使用](active-directory-aadconnect-get-started.md)
* [Azure AD Connect の次のトピック](active-directory-aadconnect-whats-next.md)
* [詳細情報](active-directory-aadconnect-learn-more.md)
* [MSDN の Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->