<properties
	pageTitle="Azure App Service での Java API アプリの構築とデプロイ"
	description="Java API アプリ パッケージを作成して Azure App Service にデプロイする方法について説明します。"
	services="app-service\api"
	documentationCenter="java"
	authors="bradygaster"
	manager="mohisri"
	editor="tdykstra"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="08/31/2016"
	ms.author="rachelap"/>

# Azure App Service での Java API アプリの構築とデプロイ

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

このチュートリアルでは、Java アプリケーションを作成し、[Git] を使用して Azure App Service API Apps にデプロイする方法について説明します。このチュートリアルの手順は、Java を実行できる任意のオペレーティング システムで使用できます。このチュートリアルのコードは、[Maven] を使用して作成されています。[Jax-RS] は RESTful サービスの作成に使用され、[Swagger Editor] を使用して [Swagger] メタデータの仕様に基づいて生成されます。

## 前提条件

1. [Java Development Kit 8] \(以降)
1. 開発用コンピューターにインストールされた [Maven]
1. 開発用コンピューターにインストールされた [Git]
1. [Microsoft Azure] の有料または[無料試用版]サブスクリプション
1. [Postman] などの HTTP テスト アプリケーション

## Swagger.IO を使用して API をスキャフォールディングする

swagger.io オンライン エディターを使用して、API の構造を表す Swagger JSON または YAML コードを入力できます。API のセキュリティを設計した後は、さまざまなプラットフォームやフレームワークのコードをエクスポートできます。次のセクションでは、スキャフォールディングされたコードに変更を加え、モック機能を含めます。

このデモでは、初めに Swagger JSON 本文を swagger.io エディターに貼り付け、それと JAX-RS を使って、REST API エンドポイントにアクセスするコードを生成します。次に、スキャフォールディングされたコードを編集してモック データが返されるようにし、データの永続化メカニズムに基づいて構築された REST API をシミュレートします。

1. 次の Swagger JSON コードをクリップボードにコピーします。

        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }

1. [オンライン Swagger Editor] に移動します。**[File]、[Paste JSON]** の順にクリックします。

    ![Paste JSON menu item][paste-json]

1. 前にコピーした Contacts List API Swagger JSON を貼り付けます。

    ![Pasting JSON code into Swagger][pasted-swagger]

1. エディターに表示されるドキュメントのページと API の概要を確認します。

    ![View Swagger Generated Docs][view-swagger-generated-docs]

1. **[Generate Server]、[JAX-RS]** メニュー オプションの順に選択し、後で編集してモック実装を追加するサーバー側のコードをスキャフォールディングします。

    ![Generate Code Menu Item][generate-code-menu-item]

    コードが生成されると、ダウンロード用の zip ファイルが提供されます。このファイルには、Swagger コード ジェネレーターによってスキャフォールディングされたコードと、関連するすべてのビルド スクリプトが含まれています。ライブラリ全体を開発用ワークステーション上のディレクトリに解凍します。

## コードを編集して API 実装を追加する

このセクションでは、Swagger によって生成されたコードのサーバー側の実装をカスタム コードに置き換えます。新しいコードは、Contact エンティティの ArrayList を呼び出し元のクライアントに返します。

1. [Visual Studio Code] または使い慣れたテキスト エディターを使用して、*Contact.java* モデル ファイルを開きます。このファイルは、*src/gen/java/io/swagger/model* フォルダーにあります。

    ![Open Contact Model File][open-contact-model-file]

1. **Contact** クラスに次のコンストラクターを追加します。

        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }

1. [Visual Studio Code] または使い慣れたテキスト エディターを使用して、*ContactsApiServiceImpl.java* サービス実装ファイルを開きます。このファイルは、*src/main/java/io/swagger/api/impl* フォルダーにあります。

    ![Open Contact Service Code File][open-contact-service-code-file]

1. この新しいコードでファイル内のコードを上書きし、サービス コードにモック実装を追加します。

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. コマンド プロンプトを開き、ディレクトリをアプリケーションのルート フォルダーに変更します。

1. 次の Maven コマンドを実行してコードをビルドし、Jetty アプリケーション サーバーを使用してローカルで実行します。

        mvn package jetty:run

1. Jetty がポート 8080 でコードを開始したことがコマンド ウィンドウに表示されます。

    ![Open Contact Service Code File][run-jetty-war]

1. [Postman] を使用して、http://localhost:8080/api/contacts にある "get all contacts" API メソッドを要求します。

    ![Call the Contacts API][calling-contacts-api]

1. [Postman] を使用して、http://localhost:8080/api/contacts/2 にある "get specific contact" API メソッドを要求します。

    ![Call the Contacts API][calling-specific-contact-api]

1. 最後に、コンソールで次の Maven コマンドを実行することにより、Java WAR (Web ARchive) ファイルをビルドします。

        mvn package war:war

1. WAR ファイルをビルドしたら、**target** フォルダーに配置します。**target** フォルダーに移動し、WAR ファイルの名前を **ROOT.war** に変更します (大文字と小文字もこの形式に一致していることを確認してください)。

         rename swagger-jaxrs-server-1.0.0.war ROOT.war

1. 最後に、アプリケーションのルート フォルダーから次のコマンドを実行して、WAR ファイルを Azure にデプロイするために使用する **deploy** フォルダーを作成します。

         mkdir deploy
         mkdir deploy\webapps
         copy target\ROOT.war deploy\webapps
         cd deploy

## 出力を Azure App Service に発行する

このセクションでは、Azure ポータルを使用して新しい API アプリを作成する方法、Java アプリケーションをホストするためにこの API アプリを準備する方法、新しく作成した WAR ファイルを Azure App Service にデプロイして新しい API アプリを実行する方法について説明します。

1. [Azure ポータル]で新しい API アプリを作成します。手順として、**[新規]、[Web + モバイル]、[API アプリ]** の順にクリックし、アプリの詳細を入力して、**[作成]** をクリックします。

    ![Create a new API App][create-api-app]

1. API アプリを作成したら、アプリの **[設定]** ブレードを開き、**[アプリケーションの設定]** をクリックします。使用可能なオプションから最新の Java バージョンを選択し、**[Web コンテナー]** メニューから最新の Tomcat を選択して、**[保存]** をクリックします。

    ![Set up Java in the API App blade][set-up-java]

1. **[デプロイ資格情報]** 設定メニュー項目をクリックし、API アプリへのファイル発行に使用するユーザー名とパスワードを指定します。

    ![デプロイメント資格情報の設定][deployment-credentials]

1. **[展開元]** 設定メニュー項目をクリックします。**[ソースの選択]** をクリックし、**[ローカル Git リポジトリ]** を選択して、**[OK]**をクリックします。これにより、Azure で実行される、API アプリに関連付けられた Git リポジトリが作成されます。Git リポジトリの*マスター*分岐に対してコードをコミットするたびに、コードは実行中の API アプリ インスタンスに発行されます。

    ![新しいローカル Git リポジトリの設定][select-git-repo]

1. 新しい Git リポジトリの URL をクリップボードにコピーします。後で重要になるため、これを保存します。

    ![Set up a new Git repository for your app][copy-git-repo-url]

1. Git は WAR ファイルをオンラインのリポジトリにプッシュします。そのためには、前に作成した **deploy** フォルダーに移動して、App Service で実行されているリポジトリに簡単にコードをコミットできるようにします。コンソール ウィンドウから webapps フォルダーのあるフォルダーに移動したら、次の Git コマンドを発行してプロセスを起動し、デプロイメントを実行します。

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master

    **プッシュ**要求を発行すると、前にデプロイ資格情報用に作成したパスワードの入力を求められます。資格情報を入力すると、更新がデプロイされたことがポータルに表示されます。

1. 再度 Postman を使用して Azure App Service で動作している新しくデプロイされた API アプリにヒットすると、一貫した動作であること、期待どおりの連絡先データが返されていること、Swagger.io でスキャフォールディングされた Java コードへの単純なコード変更が使用されていることを確認できます。

    ![Using your Java Contacts REST API live in Azure][postman-calling-azure-contacts]

## 次のステップ

この記事では、初めに Swagger JSON ファイルを使用し、Swagger.io エディターでスキャフォールディングされた Java コードを作成しました。そこから、単純な変更と Git デプロイ プロセスにより、Java で記述された機能的な API アプリを作成しました。次のチュートリアルでは、[CORS を利用し、JavaScript クライアントから API アプリを使用する][App Service API CORS]方法について説明します。それ以降のチュートリアルでは、認証と承認を実装する方法について説明します。

[Storage SDK for Java] についての理解を深めれば、このサンプルを応用して JSON BLOB を永続化することができます。または、[Document DB Java SDK] を使用して、Azure Document DB に Contact データを保存することもできます。

Azure での Java の使用に関する詳細については、「[Java デベロッパー センター]」を参照してください。

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Azure ポータル]: https://portal.azure.com/
[Document DB Java SDK]: ../documentdb/documentdb-java-application.md
[無料試用版]: https://azure.microsoft.com/pricing/free-trial/
[Git]: http://www.git-scm.com/
[Java デベロッパー センター]: /develop/java/
[Java Development Kit 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[Jax-RS]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[オンライン Swagger Editor]: http://editor.swagger.io/
[Postman]: https://www.getpostman.com/
[Storage SDK for Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Swagger Editor]: http://editor.swagger.io/
[Visual Studio Code]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png

<!---HONumber=AcomDC_1005_2016-->