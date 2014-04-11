## <a name="what-is"> </a>BLOB ストレージとは

Windows Azure BLOB ストレージは、HTTP または HTTP を使用して世界中のどこからでもアクセスできる大量の構造化されていないデータを格納するためのサービスです。1 つの BLOB に数百 GB のデータを格納でき、また、ストレージ アカウントが 2012 年 6 月 8 日以降に作成されている場合、1 つのストレージ アカウントには最大 200 TB の BLOB を格納できます。ストレージ アカウントがそれよりも前の日付で作成されている場合は、最大 100 TB の BLOB を格納できます。ストレージ アカウントの容量の詳細については、「[Windows Azure Storage Scalability and Performance Targets (Windows Azure のストレージの拡張性とパフォーマンスのターゲット)](http://msdn.microsoft.com/ja-jp/library/dn249410.aspx)」を参照してください。

BLOB ストレージの一般的な用途には、次のようなものがあります。

-   画像またはドキュメントをブラウザーに直接配信する
-   分散アクセス用にファイルを格納する
-   ビデオおよびオーディオをストリーミング配信する
-   セキュリティで保護されたバックアップおよび障害復旧を実行する
-   内部設置型サービスまたは Windows Azure ホステッド サービスで分析するデータを格納する

BLOB ストレージを使用すると、データを一般に公開することも、内部アプリケーション ストレージ用にプライベートに公開することもできます。

## <a name="concepts"> </a>概念

BLOB サービスには、次のコンポーネントが含まれます。

![BLOB1][Blob1]

-   **ストレージ アカウント:** Windows Azure のストレージにアクセスする場合には必ず、ストレージ アカウントを使用します。ストレージ アカウントの容量の詳細については、「[Windows Azure Storage Scalability and Performance Targets (Windows Azure のストレージの拡張性とパフォーマンスのターゲット)](http://msdn.microsoft.com/ja-jp/library/dn249410.aspx)」を参照してください。

-   **コンテナー:** コンテナーは、BLOB のセットをグループ化します。
    すべての BLOB はコンテナーに格納されている必要があります。1 つのアカウントに格納できるコンテナーの数は無制限です。また、1 つのコンテナーに保存できる BLOB の数も無制限です。

-   **BLOB:** 任意の種類およびサイズのファイルです。Windows Azure のストレージ サービスに格納できる BLOB には、ブロック BLOB とページ BLOB の 2 種類があります。
    ほとんどのファイルはブロック BLOB です。1 つのブロック BLOB には、最大で 200 GB までのデータを格納できます。このチュートリアルでは、ブロック BLOB を使用します。もう 1 つの種類の BLOB であるページ BLOB には、最大 1 TB までのデータを格納できます。ファイルのバイト数の範囲が頻繁に変更される場合には、こちらの方が効率的です。BLOB の詳細については、「[ブロック BLOB およびページ BLOB について][]」を参照してください。

-   **URL 形式:** BLOB は、次の URL 形式を使用してアドレスを指定し、
    アクセスできます。  
    http://`<storage
    account>`.blob.core.windows.net/`<container>`/`<blob>`  
      
    次の例の URL を使用すると、上の図のいずれかの BLOB をアドレス指定
    できます。  
    `http://sally.blob.core.windows.net/movies/MOV1.AVI`


  [ブロック BLOB およびページ BLOB について]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee691964.aspx
[Blob1]: ./media/howto-blob-storage/blob1.jpg

