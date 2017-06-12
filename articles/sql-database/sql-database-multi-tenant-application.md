---
title: "Azure SQL Database を使用するマルチテナント SaaS アプリケーションの実装 | Microsoft Docs"
description: "Azure SQL Database を使用してマルチテナント SaaS アプリケーションを実装します。"
services: sql-database
documentationcenter: 
author: AyoOlubeko
manager: jhubbard
editor: monicar
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-develop, mvc
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/08/2017
ms.author: AyoOlubek
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 80df7b504d13fe1b3be9806eb95e3980d7790970
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---

# <a name="implement-a-multi-tenant-saas-application-using-azure-sql-database"></a>Azure SQL Database を使用したマルチテナント SaaS アプリケーションの実装

マルチテナント アプリケーションとは、クラウド環境でホストされ、相互のデータを共有したり表示したりすることのない数百または数千のテナントにサービスの同じセットを提供するアプリケーションです。 一例として、クラウド ホスト環境でテナントにサービスを提供する SaaS アプリケーションがあります。 このモデルは各テナントのデータを分離し、コストに対するリソースの配布を最適化します。 

このチュートリアルでは、Azure SQL Database を使用してマルチテナント SaaS アプリケーションを作成する方法を示します。

このチュートリアルでは、次の事項について説明します。
> [!div class="checklist"]
> * テナントごとのデータベース パターンを使用して、マルチテナント SaaS アプリケーションをサポートするデータベース環境を設定する
> * テナント カタログを作成する
> * テナント データベースをプロビジョニングしてテナント カタログに登録する
> * サンプルの Java アプリケーションを設定する 
> * 単純な Java コンソール アプリケーションでテナント データベースにアクセスする
> * テナントを削除する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

このチュートリアルを完了するには、以下のものが必要です。
* PowerShell がコンピューターにインストールされていること、および[最新の Azure PowerShell SDK](http://azure.microsoft.com/downloads/)。

* 最新バージョンの [SQL Server Management Studio](http://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。 SQL Server Management Studio をインストールすると、最新バージョンの SQLPackage コマンド ライン ユーティリティもインストールされます。このユーティリティを使用して、さまざまなデータベース開発タスクを自動化できます。

* [Java Runtime Environment (JRE) 8](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)、および[最新の JAVA Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)がコンピューターにインストールされていること。 

* [Apache Maven](https://maven.apache.org/download.cgi) がコンピューターにインストールされていること。 Maven を使用して、サンプル Java プロジェクトの依存関係の管理、ビルド、テスト、および実行を行うことができます。

## <a name="set-up-data-environment"></a>データ環境を設定する

データベースをテナントごとにプロビジョニングします。 テナントごとのデータベース モデルは、テナント間において、少しの DevOps コストで最高水準の分離を提供します。 また、クラウド リソースのコストを最適化するために、テナント データベースをエラスティック プールにプロビジョニングします。これにより、データベースのグループの価格のパフォーマンスを最適化します。 その他のデータベース プロビジョニング モデルについては、[こちら](sql-database-design-patterns-multi-tenancy-saas-applications.md#multitenant-data-models)をご覧ください。 

次の手順に従って、すべてのテナント データベースをホストする SQL サーバーとエラスティック プールを作成します。 

1. このチュートリアルの残りの部分に使用される値を格納する変数を作成します。 IP アドレス変数を自分の IP アドレスに変更してください。 
   
   ```PowerShell 
   # Set an admin login and password for your database
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   
   # Create random unique names for logical server and tenants
   $servername = "server-$(Get-Random)"
   $tenant1 = "geolamice"
   $tenant2 = "ranplex"
   
   # Store current client IP address (modify to include your IP address)
   $startIpAddress = 0.0.0.0 
   $endIpAddress = 0.0.0.1
   ```
   
2. Azure にログインして SQL サーバーとエラスティック プールを作成します。 
   
   ```PowerShell
   # Login to Azure 
   Login-AzureRmAccount
   
   # Create resource group 
   New-AzureRmResourceGroup -Name "myResourceGroup" -Location "northcentralus"
   
   # Create logical SQL Server with firewall rules 
   New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
       -ServerName $servername `
       -Location "northcentralus" `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   
   New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
       -ServerName $servername `
       -FirewallRuleName "singleAddress" -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
   
   # Create elastic pool 
   New-AzureRmSqlElasticPool -ResourceGroupName "myResourceGroup"
       -ServerName $servername `
       -ElasticPoolName "myElasticPool" `
       -Edition "Standard" `
       -Dtu 50 `
       -DatabaseDtuMin 10 `
       -DatabaseDtuMax 20
   ```
   
## <a name="create-tenant-catalog"></a>テナント カタログを作成する 

マルチテナント SaaS アプリケーションでは、テナントの情報が格納される場所を把握する必要があります。 これは通常、カタログ データベースに格納されます。 カタログ データベースは、テナントとそのテナントのデータが格納されるデータベースの間のマッピングを保持するために使用されます。  マルチテナント データベースとシングルテナント データベースのいずれが使用されていても、基本パターンが適用されます。

次の手順に従って、サンプル SaaS アプリケーションのカタログ データベースを作成します。

```PowerShell
# Create empty database in pool
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "tenantCatalog" `
    -ElasticPoolName "myElasticPool"

# Create table to track mapping between tenants and their databases
$commandText = "
CREATE TABLE Tenants
(
   TenantId         INT IDENTITY PRIMARY KEY,
   TenantName       NVARCHAR(128) NOT NULL,
   TenantDatabase   NVARCHAR(128) NOT NULL
);

CREATE INDEX IX_TenantName ON Tenants (TenantName);"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="provision-database-for-tenant1-and-register-in-tenant-catalog"></a>'tenant1' のデータベースをプロビジョニングしてテナント カタログに登録する 
PowerShell を使用して新しいテナント 'tenant1' に対してデータベースをプロビジョニングし、このテナントをカタログに登録します。 

```PowerShell
# Create empty database in pool for 'tenant1'
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant1 `
    -ElasticPoolName "myElasticPool"

# Create table WhoAmI and insert tenant name into the table 
$commandText = "
CREATE TABLE WhoAmI (TenantName NVARCHAR(128) NOT NULL);
INSERT INTO WhoAmI VALUES ('Tenant $tenant1');"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database $tenant1 `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Register 'tenant1' in the tenant catalog 
$commandText = "
INSERT INTO Tenants VALUES ('$tenant1', '$tenant1');"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="provision-database-for-tenant2-and-register-in-tenant-catalog"></a>'tenant2' のデータベースをプロビジョニングしてテナント カタログに登録する
PowerShell を使用して新しいテナント 'tenant2' に対してデータベースをプロビジョニングし、このテナントをカタログに登録します。 

```PowerShell
# Create empty database in pool for 'tenant2'
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant2 `
    -ElasticPoolName "myElasticPool"

# Create table WhoAmI and insert tenant name into the table 
$commandText = "
CREATE TABLE WhoAmI (TenantName NVARCHAR(128) NOT NULL);
INSERT INTO WhoAmI VALUES ('Tenant $tenant2');"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database $tenant2 `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Register tenant 'tenant2' in the tenant catalog 
$commandText = "
INSERT INTO Tenants VALUES ('$tenant2', '$tenant2');"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="set-up-sample-java-application"></a>サンプルの Java アプリケーションを設定する 

1. Maven プロジェクトを作成します。 コマンド プロンプト ウィンドウに次のコードを入力します。
   
   ```
   mvn archetype:generate -DgroupId=com.microsoft.sqlserver -DartifactId=mssql-jdbc -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```
   
2. pom.xml ファイルに、この依存関係、言語レベル、および jar のマニフェスト ファイルをサポートするビルド オプションを追加します。
   
   ```XML
   <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
   </dependency>
   
   <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   
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

3. App.java ファイルに次のコードを追加します。

   ```java 
   package com.sqldbsamples;
   
   import java.util.Map;
   
   import java.util.HashMap;
   
   import java.io.BufferedReader;
   
   import java.io.InputStreamReader;
   
   import java.sql.Connection;
   
   import java.sql.Statement;
   
   import java.sql.PreparedStatement;
   
   import java.sql.ResultSet;
   
   import java.sql.DriverManager;
   
   public class App {
   
   private static final String SERVER_NAME = "your-server-name";
   
   private static final String CATALOG_DB_NAME = "tenantCatalog";
   
   private static final String USER = "ServerAdmin";
   
   private static final String PASSWORD = "ChangeYourAdminPassword1";
   
   private static final String CATALOG_DB_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SERVER_NAME, CATALOG_DB_NAME, USER, PASSWORD);
   
   private static final String CMD_LIST = "LIST";

   private static final String CMD_QUERY = "QUERY";

   private static final String CMD_QUIT = "QUIT";
   
   public static void main(String[] args) {
   
   System.out.println("\n############################");
   
   System.out.println("## SAAS DATABASE TUTORIAL ##");
   
   System.out.println("############################\n");
   
   System.out.println("OPTIONS");
   
   System.out.println(" " + CMD_LIST + " - list tenants");
   
   System.out.println(" " + CMD_QUERY + " <NAME> - connect and tenant query tenant <NAME>");
   
   System.out.println(" " + CMD_QUIT + " - quit the application\n");
   
   try (BufferedReader br = new BufferedReader(new InputStreamReader(System.in))) {
   
   while(true) {
   
   String[] input = br.readLine().split("\\s");
   
   if (null != input && input.length > 0) {
   
   if (input[0].equalsIgnoreCase(CMD_LIST)) {
   
   listTenants();
   
   } else if (input[0].toLowerCase().startsWith(CMD_QUERY.toLowerCase()) && input.length == 2) {
   
   queryTenant(input[1].trim());
   
   } else if (input[0].equalsIgnoreCase(CMD_QUIT)) {
   
   break;
   
   } else {
   
   System.out.println(" -> Command not supported");
   
   }
   
   }
   
   System.out.println("");
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   }
   
   private static void listTenants() {
   
   // List all tenants that currently exist in the system
   
   String sql = "SELECT TenantName FROM Tenants";
   
   try (Connection connection = DriverManager.getConnection(CATALOG_DB_URL);
   
   Statement stmt = connection.createStatement();
   
   ResultSet resultSet = stmt.executeQuery(sql)) {
   
   while (resultSet.next()) {
   
   System.out.println(" -> " + resultSet.getString(1));
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   }
   
   private static void queryTenant(String name) {
   
   // Query the data that was previously inserted into the primary database from the geo replicated database
   
   String url = null;
   
   String sql = "SELECT TenantDatabase FROM Tenants WHERE TenantName = ?";
   
   try (Connection connection = DriverManager.getConnection(CATALOG_DB_URL);
   
   PreparedStatement pstmt = connection.prepareStatement(sql)) {
   
   pstmt.setString(1, name);
   
   try (ResultSet resultSet = pstmt.executeQuery()) {
   
   if (resultSet.next()) {
   
   url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SERVER_NAME, resultSet.getString(1), USER, PASSWORD);
   
   }
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   if (null != url) {
   
   String tenantSql = "SELECT TenantName FROM WhoAmI";
   
   try (Connection connection = DriverManager.getConnection(url);
   
   Statement stmt = connection.createStatement();

   ResultSet resultSet = stmt.executeQuery(tenantSql)) {
   
   while (resultSet.next()) {
   
   System.out.println(" -> Entry in table WhoAmI in tenant " + name + " is: " + resultSet.getString(1));
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   } else {
   
   System.out.println(" -> Tenant " + name + " not found");
   
   }
   
   }
   
   }
   ```

4. ファイルを保存します。

5. コマンド コンソールに移動して次を実行します。

   ```bash
   mvn package
   ```

6. 完了したら、次を実行してアプリケーションを実行します。 
   
   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```
   
正常に実行された場合、出力は次のようになります。

```
############################

## SAAS DATABASE TUTORIAL ##

############################

OPTIONS

LIST - list tenants

QUERY <NAME> - connect and tenant query tenant <NAME>

QUIT - quit the application

* List the tenants

* Query tenants you created
```

## <a name="delete-first-tenant"></a>最初のテナントを削除する 
PowerShell を使用して、最初のテナントのテナント データベースとカタログ エントリを削除します。

```PowerShell
# Remove 'tenant1' from catalog 
$commandText = "DELETE FROM Tenants WHERE TenantName = '$tenant1';"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Delete database 
Remove-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant1
```

Java アプリケーションを使用して 'tenant1' に接続してみましょう。 そのテナントが存在しないことを示すエラーが表示されます。

## <a name="next-steps"></a>次のステップ 
このチュートリアルで学習した内容は次のとおりです。
> [!div class="checklist"]
> * テナントごとのデータベース パターンを使用して、マルチテナント SaaS アプリケーションをサポートするデータベース環境を設定する
> * テナント カタログを作成する
> * テナント データベースをプロビジョニングしてテナント カタログに登録する
> * サンプルの Java アプリケーションを設定する 
> * 単純な Java コンソール アプリケーションでテナント データベースにアクセスする
> * テナントを削除する

* 一般的なタスクの PowerShell サンプルについては、[Azure SQL Database 用の PowerShell サンプル](https://docs.microsoft.com/azure/sql-database/sql-database-powershell-samples)に関するページをご覧ください。

* マルチテナント SaaS アプリケーションの設計パターンについては、[設計パターン](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)に関するページをご覧ください。

* Azure の一般的なタスク用の Java サンプルについては、「[Java デベロッパー センター](https://azure.microsoft.com/develop/java/)」をご覧ください。




