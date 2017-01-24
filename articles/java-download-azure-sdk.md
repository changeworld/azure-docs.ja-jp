---
title: "Azure SDK for Java のダウンロード"
description: "Azure SDK for Java をダウンロードする方法を Maven プロジェクト用に用意されたサンプル コードと共に紹介し、また、Azure Tookit for Eclipse の基本的なインストール手順を説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 4b8f8fe6-1b26-4bb4-9be9-6ae757a59e66
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9b2d456d8dba33af224ea147f5f8ec49ba7397f9
ms.openlocfilehash: 8d4042a270af2ba143b45d23c3f0c01f836c4592


---
# <a name="download-the-azure-sdk-for-java"></a>Azure SDK for Java のダウンロード
この記事には、Azure Libraries for Java をダウンロードしてインストールための手順が記載されています。

**注:** Azure Libraries for Java は、[Apache License Version 2.0][license] に準拠して配布されます。

## <a name="azure-libraries-for-java---manual-download"></a>Azure Libraries for Java - 手動ダウンロード
Azure Libraries for Java を手動でインストールするには、<http://go.microsoft.com/fwlink/?LinkId=690320> をクリックして、すべてのライブラリと依存関係を含む ZIP ファイルをダウンロードします。

zip ファイルをコンピューターにダウンロードしたら、コンテンツを抽出し、次のいずれかのオプションを使用して、JAR ファイルをプロジェクトに追加します。

* JAR ファイルを Eclipse の Java プロジェクトにインポートします。
* Eclipse の Java プロジェクトの **ビルド パス** を JAR ファイルへのパスを含むように構成します。

Eclipse でのビルド パスの設定方法については、Eclipse Web サイトの [Java ビルド パス] に関する記事を参照してください。

**注:** ライセンスおよびその他の情報については、ZIP 内の license.txt と ThirdPartyNotices.txt ファイルを参照してください。

## <a name="azure-libraries-for-java---building-with-maven"></a>Azure Libraries for Java - Maven によるビルド
### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>手順 1 - ビルドに Maven を使用するようにプロジェクトを設定する
Java 用 Azure ライブラリを使用する Maven プロジェクトを Eclipse で作成するには、「[Getting Started with Azure Management Libraries for Java (Java 用 Azure 管理ライブラリの概要)][maven-getting-started]」に記載された手順に従ってください。

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>手順 2 - 必要な依存関係を持つ Maven 設定を構成する
ビルドに Maven を使用するようにプロジェクトを構成したら、必要な依存関係を、次の例のような構文を使用する pom.xml ファイルに追加できます。 次の例に表示されたすべての依存関係を追加する必要はありません。追加する必要があるのは、プロジェクトに必要な特定の依存関係のみです。

> [!NOTE]
> 次のサンプルの各 `<version>` 要素内で、この例の "n.n.n" プレースホルダーを有効なバージョン番号に置き換えてください。有効なバージョン番号は、[Maven の Azure ライブラリ リポジトリ]から入手できます。
>
>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="installing-the-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse のインストール
このセクションでは、Azure Toolkit for Eclipse をインストールするための基本的な手順を示します。詳細な手順については、「[Azure Toolkit for Eclipse のインストール]」を参照してください。

### <a name="prerequisites"></a>前提条件
1. 「 [Azure Toolkit for Eclipse の新機能] 」の一覧にある Windows オペレーティング システム。
2. 「 [Azure Toolkit for Eclipse の新機能] 」の一覧にある Macintosh または Linux オペレーティング システム。
3. Eclipse IDE for Java EE Developers Indigo 以降。 <http://www.eclipse.org/downloads/> からダウンロードできます。

### <a name="basic-installation-steps"></a>基本的なインストール手順
1. Eclipse で、**[ヘルプ]** メニューの **[Install New Software (新しいソフトウェアのインストール)]** をクリックします。
2. サイトの場所である <http://dl.microsoft.com/eclipse> を入力し、**Enter** キーを押します。
3. インストールする項目を選択して、 **[Finish]**をクリックします。

Azure Toolkit for Eclipse では、最新バージョンの Azure SDK を使用します。 これは、<http://go.microsoft.com/fwlink/?LinkID=252838> で Web プラットフォーム インストーラー (WebPI) を使用してダウンロードできます。 ただし、まだインストールしないうちに、最初の Azure デプロイ プロジェクトを作成すると、Azure Toolkit for Eclipse によって適切なバージョンの Azure SDK が自動的にインストールされます。

## <a name="see-also"></a>関連項目
[Azure Toolkit for Eclipse]

[Azure Toolkit for Eclipse のインストール]

[Azure 向け Hello World アプリケーションを Eclipse で作成する]

Java での Azure の使用方法の詳細については、「 [Azure Java Developer Center]」を参照してください。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Maven の Azure ライブラリ リポジトリ]: http://go.microsoft.com/fwlink/?LinkID=286274
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure 向け Hello World アプリケーションを Eclipse で作成する]: http://go.microsoft.com/fwlink/?LinkID=699533
[Azure Toolkit for Eclipse のインストール]: http://go.microsoft.com/fwlink/?LinkId=699546
[Java ビルド パス]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Azure Toolkit for Eclipse の新機能]: http://go.microsoft.com/fwlink/?LinkId=690333



<!--HONumber=Dec16_HO2-->


