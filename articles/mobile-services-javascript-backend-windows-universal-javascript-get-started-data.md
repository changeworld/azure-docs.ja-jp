<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# モバイル サービスでのデータの使用

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

このトピックでは、Azure のモバイル サービスを使用してユニバーサル Windows アプリのデータを活用する方法について説明します。ユニバーサル Windows アプリ ソリューションには、Windows ストア 8.1 と Windows Phone ストア 8.1 の両方のアプリのプロジェクトと、共通の共有プロジェクトが含まれます。詳細については、「[Windows と Windows Phone を対象とするユニバーサル Windows アプリの構築][Windows と Windows Phone を対象とするユニバーサル Windows アプリの構築]」を参照してください。

このチュートリアルでは、メモリにデータを格納するユニバーサル Windows アプリの Visual Studio 2013 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにサインインして、アプリケーションの実行中にデータに加えられた変更を表示します。

> [WACOM.NOTE] このトピックでは、Visual Studio Express 2013 with Update 3 for Windows でツーリングを使用して、新しいモバイル サービスをユニバーサル Windows アプリに接続する方法について説明します。同じ手順を使用して、モバイル サービスを Windows ストアまたは Windows Phone ストア 8.1 アプリに接続することができます。モバイル サービスを Windows Phone 8.0 または Windows Phone Silverlight 8.1 アプリに接続するには、「[Windows Phone 向けデータの使用][Windows Phone 向けデータの使用]」を参照してください。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [Windows ストア アプリ プロジェクトのダウンロード][Windows ストア アプリ プロジェクトのダウンロード]
2.  [モバイル サービスの作成][モバイル サービスの作成]
3.  [ストレージのデータ テーブルの追加][ストレージのデータ テーブルの追加]
4.  [モバイル サービスを使用するためのアプリケーションの更新][モバイル サービスを使用するためのアプリケーションの更新]
5.  [モバイル サービスに対するアプリケーションのテスト][モバイル サービスに対するアプリケーションのテスト]

このチュートリアルを完了するには、以下が必要です。

-   アクティブな Azure アカウントアカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。
-   [Visual Studio Express 2013 for Windows][Visual Studio Express 2013 for Windows] (Update 2 以降のバージョン)。

## <a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

## <a name="create-service"></a>Visual Studio からの新しいモバイル サービスの作成

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  Solution Explorer で、**services\\mobileService\\scripts** サブフォルダーに移動し、service.js スクリプト ファイルを開き、次のような新しいグローバル変数があることを確認します。

        var todolistClient = new WindowsAzure.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

    このコードは、グローバル変数を使用して、アプリケーションの新しいモバイル サービスへのアクセスを提供します。クライアントは、新しいモバイル サービスの URI とアプリケーション キーを提供することによって作成されます。このスクリプトへの参照は default.html ファイルに追加されているため、この変数は、このページからも参照されるすべてのスクリプト ファイルで使用できます。

2.  default.html プロジェクト ファイルを開き、新しい service.js スクリプト ファイルへの参照を見つけ、参照されたパスが次のようになっていることを確認します。

        <script src="/services/mobileServices/scripts/todolist.js">

    現在、Visual Studio では、誤ったフォルダー名がパスに生成されるバグがあります。

3.  Windows Phone アプリ プロジェクトを右クリックし、**[追加]**、**[接続済みサービス...]** をクリックし、作成されたばかりのモバイル サービスを選択してから、**[OK]** をクリックします。

    これと同じ新しいコード ファイルが Windows Phone ストア アプリ プロジェクトに追加されます。default.html ファイルに追加された参照パスも必ず修正してください。

## <a name="add-table"></a>新しいテーブルをモバイル サービスに追加する

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

> [WACOM.NOTE] Id、\_\_createdAt、\_\_updatedAt、および \_\_version の列を持つ新しいテーブルが作成されます。動的スキーマが有効な場合、挿入または更新の要求に含まれる JSON オブジェクトに基づいて、モバイル サービスによって自動的に新しい列が生成されます。詳細については、「[動的スキーマ][動的スキーマ]」を参照してください。

# <a name="update-app"></a>モバイル サービスを使用するためのアプリケーションの更新

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

## <a name="test-azure-hosted"></a>Azure でホストされているモバイル サービスをテストする

これで、Azure でホストされているモバイル サービスに対して両方のバージョンのユニバーサル Windows アプリをテストできます。

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

1.  [管理ポータル][管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

    <p>
2.  **[データ]** タブをクリックし、**[参照]** をクリックします。

    **TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

![][0]

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、ユニバーサル Windows アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

-   [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更][サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
    Mobile Services でサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

-   [ページングを使用したモバイル サービス クエリの改善][ページングを使用したモバイル サービス クエリの改善]
    クエリ内でページングを使用して、1 回の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す他のチュートリアルのいずれかを行うことをお勧めします。

-   [認証の使用][認証の使用]
    アプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [モバイル サービス HTML/JavaScript の使用方法の概念リファレンス][モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]
    HTML および JavaScript でモバイル サービスを使用する方法について説明します。


  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Windows と Windows Phone を対象とするユニバーサル Windows アプリの構築]: http://msdn.microsoft.com/ja-jp/library/windows/apps/xaml/dn609832.aspx
  [Windows Phone 向けデータの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Windows ストア アプリ プロジェクトのダウンロード]: #download-app
  [モバイル サービスの作成]: #create-service
  [ストレージのデータ テーブルの追加]: #add-table
  [モバイル サービスを使用するためのアプリケーションの更新]: #update-app
  [モバイル サービスに対するアプリケーションのテスト]: #test-app
  [Azure の無料評価版サイト]: http://azure.microsoft.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-javascript-get-started-data%2F
  [Visual Studio Express 2013 for Windows]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-windows-universal-javascript-download-project]: ../includes/mobile-services-windows-universal-dotnet-download-project.md
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [動的スキーマ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj193175.aspx
  [mobile-services-windows-javascript-update-data-app]: ../includes/mobile-services-windows-javascript-update-data-app.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png
  [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
  [ページングを使用したモバイル サービス クエリの改善]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-html-how-to-use-client-library/
