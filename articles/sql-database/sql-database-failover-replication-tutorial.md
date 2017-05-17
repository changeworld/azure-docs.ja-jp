---
title: "Azure SQL Database ソリューションのレプリケートとフェールオーバー | Microsoft Docs"
description: "Azure SQL Database とアプリケーションをレプリケートされたデータベースにフェールオーバーするための構成方法と、フェールオーバーをテストする方法について説明します。"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-failover
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/18/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e5b82da402ab9d20410746c2b6c6b3aaab5b754
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---

# <a name="replicate-and-failover-an-azure-sql-database-solution"></a>Azure SQL Database ソリューションのレプリケートとフェールオーバー

このチュートリアルでは、Azure SQL Database とアプリケーションをリモート リージョンのフェールオーバーのために構成し、その後フェールオーバー計画をテストします。 

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

このチュートリアルを完了するには、以下のものが必要です。

- 最新バージョンの [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。 SSMS をインストールすると、最新バージョンの SQLPackage コマンド ライン ユーティリティもインストールされます。このユーティリティを使用して、さまざまなデータベース開発タスクを自動化できます。 
- 移行する Azure データベース。 このチュートリアルは、次のクイック スタートのいずれか 1 つからの **mySampleDatabase という名前の AdventureWorksLT サンプル データベースを使用します。

   - [DB の作成 - ポータル](sql-database-get-started-portal.md)
   - [DB の作成 - CLI](sql-database-get-started-cli.md)

## <a name="create-azure-active-directory-users-optional"></a>Azure Active Directory ユーザーを作成する (オプション)

この手順では、Azure SQL Database 論理サーバーおよびサンプル データベースのユーザーとして追加する Azure Active Directory ユーザーを作成または指定します。
- サブスクリプションが Azure Active Directory 企業環境の一部であり、既存のユーザー アカウントがある場合は、3 つのユーザー アカウントをこのチュートリアルで使用する Active Directory 管理ユーザー、アプリケーション管理者、およびアプリケーション ユーザーとして指定し、手順 3 の SQL Database のログインとユーザーの作成に進みます。 
- サブスクリプションが Azure Active Directory 企業環境の一部でなく、ユーザー アカウントが存在しない (および Azure Active Directory の新しいユーザー アカウントを作成するアクセス許可がある) 場合は、次のようにします。

1. [Azure Portal](http://portal.azure.com) にログインします。
2. 左側のメニューで、**[その他のサービス]** をクリックします。
3. フィルター テキスト ボックスで、「**Azure**」と入力して **[Azure Active Directory]** を選択します。
4. **[Azure Active Directory]** ページの **[クイック タスク]** ペインで、**[ユーザーの追加]** をクリックします。
5. **[ユーザー]** フォームで、次のユーザーを作成します。
   - 名前: **ad-admin**
   - ユーザー名: **ad-admin@yourdomain** (Yopu4708)
6. **[パスワードの表示]** チェックボックスをオンにし、後で使用できるようにこのユーザー アカウントのパスワードを記録します。
7. **[作成]**をクリックします。
8. 前の 3 つの手順を繰り返して、次の 2 つのユーザーを作成します。
   - 名前: **app-admin**
   - ユーザー名: **app-admin@yourdomain** (Buju4319)
   - 名前: **app-user**
   - ユーザー名: **app-user@yourdomain**  (Nonu4001)

9. 新しいブラウザー ウィンドウを開き、新しく作成した **ad-admin** アカウントで Azure Portal にログインします。
10. **[パスワードの更新]** ページで、**[現在のパスワード]** ボックスにシステムによって生成されたパスワードを入力します。 
11. **[新しいパスワード]** ボックスと **[パスワードの確認入力]** ボックスに、任意のパスワードを入力します。
12. **[パスワードを更新してサインイン]** をクリックします。

## <a name="configure-sql-database-integration-with-azure-active-directory"></a>Azure Active Directory で SQL Database 統合を構成する

1. 左側のメニューで **[その他のサービス]** をクリックし、フィルター テキスト ボックスに「**sql**」と入力して **[SQL サーバー]** を選択します。
2. **[SQL サーバー]** ページで、お使いの SQL Database サーバーをクリックします。
3. お使いのサーバーの **[概要]** ページの [基本] ペインで、**[Active Directory 管理者]** を **[未構成]** にします。
4. **[Active Directory 管理者]** ページで、**[管理者の設定]** をクリックします。
5. **ad-admin** Azure Active Directory アカウント (または自身のアカウントなど、その他既存のアカウント) を選択し、お使いの Azure SQL Database サーバーのサーバー管理者にします。
6. **[選択]**をクリックします。
7. [ **Save**] をクリックします。

## <a name="create-users-with-permissions-for-your-database"></a>データベースのアクセス許可を持つユーザーを作成する

<TO DO: need to change script to grant app-user sufficient permissions to perform operation in java app>

SQL Server Management Studio を使用してデータベースに接続し、ユーザー アカウントを作成します。 これらのユーザー アカウントはセカンダリ サーバーに自動的にレプリケートされます。 まだファイアウォールを構成していない IP アドレスのクライアントから接続している場合は、ファイアウォール規則を作成する必要がある場合があります。 手順については、[Azure Portal を使用した SQL DB の作成](sql-database-get-started-portal.md)に関する記事をご覧ください。

1. SQL Server Management Studio を開きます。
2. **[認証]** モードを **[Active Directory パスワード認証]** に変更します。
3. 新しく設計した Azure Active Directory サーバー管理者アカウントを使用してサーバーを接続します。 
4. オブジェクト エクスプローラーで **[システム データベース]** を展開し、**[mySampleDatabase]** を右クリックして、**[新しいクエリ]** をクリックします。
5. クエリ ウィンドウで、次のクエリを実行してデータベースにユーザー アカウントを作成し、2 つの管理アカウントに **db_owner** のアクセス許可を付与します。 ドメイン名のプレースホルダーを自分のドメインで置き換えます。

   ```tsql
   --Create Azure AD user account
   CREATE USER [app-admin@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Add Azure AD user to db_owner role
   ALTER ROLE db_owner ADD MEMBER [app-admin@<yourdomain>]; 
   --Create additional Azure AD user account
   CREATE USER [app-user@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Create SQL user account
   CREATE USER app_admin WITH PASSWORD = 'MyStrongPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'MyStrongPassword1';
   ```

## <a name="create-database-level-firewall"></a>データベース レベルのファイアウォールを作成する

SQL Server Management Studio を使用して、データベースにデータベース レベルのファイアウォール規則を作成します。 このデータベース レベルのファイアウォール ルールは、セカンダリ サーバーに自動的にレプリケートされます。 テストの目的で、すべての IP アドレス (0.0.0.0 と 255.255.255.255) のファイアウォール規則を作成したり、サーバーのファイアウォール規則の作成に使用した単一の IP アドレスのファイアウォール規則を作成したり、このチュートリアルのテストに使用するコンピューターの IP アドレスに 1 つ以上のファイアウォール規則を構成したりできます。  

- 開かれたクエリ ウィンドウで、前のクエリを次のクエリと置き換えます。IP アドレスは自分の環境の適切な IP アドレスに置き換えます。 

   ```tsql
   -- Create database-level firewall setting for your publich IP address
   EXECUTE sp_set_database_firewall_rule N'mySampleDatabase','0.0.0.1','0.0.0.1';
   ```  

## <a name="create-a-failover-group"></a>フェールオーバー グループを作成する 

フェールオーバー リージョンを選択し、そのリージョンで空のサーバーを作成して、既存のサーバーと新しい空のサーバーの間にフェールオーバー グループを作成します。

1. 変数を設定します。

   ```powershell
   $secpasswd = ConvertTo-SecureString "yourstrongpassword" -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential (“ServerAdmin”, $secpasswd)
   $myresourcegroup = "<your resource group>"
   $mylocation = "<resource group location>"
   $myserver = "<your existing server>"
   $mydatabase = "<your existing database>"
   $mydrlocation = "<your disaster recovery location>"
   $mydrserver = "<your disaster recovery server>"
   $myfailovergroup = "<your failover group"
   ```

2. フェールオーバー リージョンに空のバックアップ サーバーを作成します。

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroup -Location $mydrlocation -ServerName $mydrserver -ServerVersion "12.0" -SqlAdministratorCredentials $mycreds
   ```

3. フェールオーバー グループを作成します。

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup –ResourceGroupName $myresourcegroup -ServerName "$myserver" -PartnerServerName $mydrserver  –FailoverGroupName $myfailovergroupname –FailoverPolicy "Automatic" -GracePeriodWithDataLossHours 2
   ```

4. データベースをフェールオーバー グループに追加します。

   ```powershell
   $mydrserver | Add-AzureRMSqlDatabaseToFailoverGroup –FailoverGroupName $myfailovergroup  -Database $mydatabase
   ```

## <a name="add-empty-backup-server-to-domain"></a>空のバックアップ サーバーをドメインに追加する

1. Azure Portal の左側のメニューで **[その他のサービス]** をクリックし、フィルター テキスト ボックスに「**sql**」と入力して **[SQL サーバー]** を選択します。
2. **[SQL サーバー]** ページで、新しい SQL Database のディザスター リカバリー サーバーをクリックします。
3. お使いのサーバーの **[概要]** ページの [基本] ペインで、**[Active Directory 管理者]** を **[未構成]** にします。
4. **[Active Directory 管理者]** ページで、**[管理者の設定]** をクリックします。
5. **ad-admin** Azure Active Directory アカウント (または自身のアカウントなど、その他既存のアカウント) を選択し、新しい Azure SQL Database ディザスター リカバリー サーバーのサーバー管理者にします。
6. **[選択]**をクリックします。
7. [ **Save**] をクリックします。

## <a name="prepare-client-tier"></a>クライアント層を準備する

1. フェールオーバー プロファイル AWProfile を使用して TM プロファイルを作成します。
2. 監視の構成

   ```powershell
   $profile = New-AzureRmTrafficManagerProfile -Name AWProfile -ResourceGroupName MYRG -TrafficRoutingMethod Failover -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
   $webapp1 = Get-AzureRMWebApp -Name WebappUSWest
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
   $webapp2 = Get-AzureRMWebApp -Name WebappUSEast
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
   Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
   ```

## <a name="deploy-java-application-and-connect-to-database"></a>Java アプリケーションをデプロイしてデータベースに接続する

<In progress> [Java との接続](sql-database-connect-query-java.md)に関する記事をご覧ください。

<TO DO: change user to app-user>

1. Java 8 をインストールします。
2. Maven をインストールします。
3. Maven プロジェクトを作成します。
4. pom.xml に次のコードを追加します。 

   - 依存関係

      ```java
      <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
       </dependency>
      ```
   - 言語レベル

      ```java
      <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
      </properties>
      ```

   - jar のマニフェスト ファイルをサポートするビルド オプション

      ```java
      <build>
        <plugins>
           <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-jar-plugin</artifactId>
              <version>3.0.0</version>
              <configuration>
                 <archive>
                    <manifest>
                       <mainClass>com.sqldbsamples.App</mainClass>
                    </manifest>
                 </archive>
              </configuration>
           </plugin>
        </plugins>
      </build>
      ```
5. App.java ファイルに次のコードを追加します。

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String PRIMARY_HOST_HAME = "your_primary_server_name";
      private static final String SECONDARY_HOST_NAME = "your_secondary_server_name";
      private static final String PRIMARY_HOST_HAME = "janengsampleserver";
      private static final String SECONDARY_HOST_NAME = PRIMARY_HOST_HAME;
    
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "ServerAdmin";
      private static final String PASSWORD = "ChangeYourAdminPassword1";

      private static final String PRIMARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", PRIMARY_HOST_HAME, DB_NAME, USER, PASSWORD);
      private static final String SECONDARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SECONDARY_HOST_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         // todo: get the max id from the table and initialize INSERT COUNTER with it so that the code will always run (avoid duplicate keys)  
         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1h
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(PRIMARY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data that was previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
        
      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```
6. ファイルを保存します。

## <a name="compile-and-run"></a>コンパイルと実行

1. コンソールに移動して次を実行します。

   ```java
   mvn package
   ```
2. 完了したら、次を実行してアプリケーションを実行します (手動で停止しない場合は 1 時間ほどかかります)。

   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   正常に実行された場合、出力は次のようになります。

   #######################################
   ## <a name="geo-distributed-database-tutorial"></a>GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful

## <a name="perform-dr-drill"></a>DR ドリルを実行する

1. 強制フェールオーバーを使用して FG の手動フェールオーバーを呼び出します。 ドリル中のデータ消失を許容できない場合は、-AllowDataLoss を削除してください。

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup -AllowDataLoss
   ```

2.    (エンドポイントのフェールオーバーをトリガーするために) TM プロファイルのプライマリ エンドポイントを無効にします。 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp1ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    アプリケーションを再実行します。


## <a name="relocate-application-to-primary-region"></a>プライマリ リージョンにアプリケーションを再配置する

1.    FG のフレンドリ フェールオーバーを手動で呼び出します。 -AllowDataLoss は指定しないでください。

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup 
   ```

2.    (エンドポイントのフェールオーバーをトリガーするために) TM プロファイルのセカンダリ エンドポイント (webapp2ep) を無効にします。 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp2ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    アプリケーションを再実行します。

## <a name="troubleshoot-failover"></a>フェールオーバーをトラブルシューティングする 

どのリージョンが現在プライマリであるかを確認し、フェールオーバーが発生していることを確認します。 プライマリ セカンダリの場合はロールが表示されます。

   ```powershell
   $fg = Get-AzureRMSqlDatabaseFailoverGroup -ResourceGroupName "myrg" -ServerName "AWserver" 
   print $fg.role
   ```

## <a name="next-steps"></a>次のステップ 

- 近日中にお近くのシアターで公開予定
