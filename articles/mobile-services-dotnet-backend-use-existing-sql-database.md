<properties 
	pageTitle="Mobile Services .NET バックエンドによる既存の SQL データベースを使用するサービスの作成 - Azure Mobile Services" 
	description="既存のクラウドまたは内部設置型 SQL データベースと .NET ベースのモバイル サービスを使用する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>


# Mobile Services .NET バックエンドによる既存の SQL データベースを使用するサービスの作成

Mobile Services .NET バックエンドを使用すると、モバイル サービスの作成で簡単に既存のアセットを利用できます。特に興味深いシナリオの 1 つに、他のアプリケーションで既に使用した可能性がある (内部設置型またはクラウドに配置された) 既存の SQL データベースを使用して、既存のデータをモバイル クライアントが利用できるようにする方法があります。この場合、既存のソリューションが機能し続けるには、データベース モデル (つまり*スキーマ*) が変更されない状態のままであることが必要です。

このチュートリアルで取り上げる内容は次のとおりです。

1. [既存のデータベース モデルを調査する](#ExistingModel)
2. [モバイル サービス用にデータ転送オブジェクト (DTO) を作成する](#DTOs)
3. [DTO とモデルとの間にマッピングを確立する](#Mapping)
4. [ドメイン専用のロジックを実装する](#DomainManager)
5. [DTO を使用して TableController を実装する](#Controller)

<a name="ExistingModel"></a>
## 既存のデータベース モデルを調査する

このチュートリアルでは、モバイル サービスによって作成されたデータベースを使用しますが、作成される既定のモデルは使用しません。代わりに、手元にある既存のアプリケーションを表す任意のモデルを手動で作成します。内部設置型データベースへの接続方法の詳細については、「[ハイブリッド接続を使用して Azure のモバイル サービスから内部設置型の SQL Server に接続する](mobile-services-dotnet-backend-hybrid-connections-get-started.md)」を参照してください。

1. 開始するには、**Visual Studio 2013 Update 2** で Mobile Services サーバー プロジェクトを作成するか、または [Azure 管理ポータル](http://manage.windowsazure.com)で、サービスの [モバイル サービス] タブでダウンロード可能なクイック スタート プロジェクトを使用します。このチュートリアルでは、サーバー プロジェクト名が **ShoppingService** という名前であると仮定します。

2. **Customer.cs** ファイルを **Models** フォルダーに作成して、次の実装を使用します。**System.ComponentModel.DataAnnotations** へのアセンブリ参照をプロジェクトに追加する必要があります。

        using System.Collections.Generic;
        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Customer
            {
                [Key]
                public int CustomerId { get; set; }
                
                public string Name { get; set; }

                public virtual ICollection<Order> Orders { get; set; }

            }
        }

3. **Order.cs** ファイルを **Models** フォルダーに作成して、次の実装を使用します。
    
        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Order
            {
                [Key]
                public int OrderId { get; set; }

                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                public int CustomerId { get; set; }
              
                public virtual Customer Customer { get; set; }

            }
        }

    これら 2 つのクラス間には*関係*があることに注意します。どの **Order** も 1 つの **Customer** に関連付けられ、1 つの **Customer** は複数の **Order** に関連付けることができます。既存のデータ モデルでリレーションシップがあることは一般的です。

4. **ExistingContext.cs** ファイルを **Models** フォルダーに作成し、次のように実装します。

        using System.Data.Entity;

        namespace ShoppingService.Models
        {
            public class ExistingContext : DbContext
            {
                public ExistingContext()
                    : base("Name=MS_TableConnectionString")
                {
                }

                public DbSet<Customer> Customers { get; set; }

                public DbSet<Order> Orders { get; set; }

            }
        }

上の構造は、既存のアプリケーションに以前から使用している可能性がある既存の Entity Framework モデルに似ています。この段階では、モデルは Mobile Services をどのような方法によっても認識していないことに注意してください。

<a name="DTOs"></a>
## モバイル サービス用にデータ転送オブジェクト (DTO) を作成する

モバイル サービスで使用するデータ モデルの複雑さは任意に決めることができます。よって、互いにさまざまなリレーションシップを持つ数百個のエンティティを含めることもできます。モバイル アプリケーションを作成する場合、通常求められるのは、データ モデルの簡素化とリレーションシップの排除 (または手動でのリレーションシップの処理) です。これは、アプリケーションとサービスの間を移動するペイロードを最小限に抑えることを目的としています。このセクションでは、一連の簡素化されたオブジェクト ("データ転送オブジェクト" または "DTO" とも呼ばれます) を作成し、データベースに保存しているデータにマッピングします。ただし、これらのオブジェクトに含める一連のプロパティは、モバイル アプリケーションで最小限必要なものに留めます。

1. **MobileCustomer.cs** ファイルをサービス プロジェクトの **DataObjects** フォルダーに作成し、次の実装を使用します。

        using Microsoft.WindowsAzure.Mobile.Service;

        namespace ShoppingService.DataObjects
        {
            public class MobileCustomer : EntityData
            {
                public string Name { get; set; }
            }
        }

    このクラスは、**Order** に対するリレーションシップ プロパティが削除されていることを除き、モデルの **Customer** クラスに似ていることに注意してください。Mobile Services オフライン同期でオブジェクトが正しく機能するには、オプティミスティック同時実行用に一連の*システム プロパティ*が必要です。そこで、この DTO は、これらのプロパティを含む [**EntityData**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx) を継承します。元のモデルの整数を基にした **CustomerId** プロパティが、**EntityData** の文字列を基にした **Id** プロパティに置き換えられて、Mobile Services が使用する **Id** となります。

2. **MobileOrder.cs** ファイルをサービス プロジェクトの **DataObjects** フォルダーに作成します。

        using Microsoft.WindowsAzure.Mobile.Service;
        using Newtonsoft.Json;
        using System.ComponentModel.DataAnnotations;
        using System.ComponentModel.DataAnnotations.Schema;

        namespace ShoppingService.DataObjects
        {
            public class MobileOrder : EntityData
            {
                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                [NotMapped]
                public int CustomerId { get; set; }

                [Required]
                public string MobileCustomerId { get; set; }

                public string MobileCustomerName { get; set; }
            }
        }

    **Customer** リレーションシップ プロパティが、**Customer** の名前と、クライアントでリレーションシップを手動でモデル化するために使用できる **MobileCustomerId** プロパティに置き換えられます。ここでは、**CustomerId** プロパティを無視できます。これは、後で使用されるだけです。

3. **EntityData** 基本クラスにシステム プロパティが追加されて、DTO にモデルの型よりも多くのプロパティがあることが確認できます。これらのプロパティを保存する場所が必要なのは明らかなため、元のデータベースにいくつかの列を追加します。これによりデータベースが変更されますが、変更は単なる追加 (スキーマへの新しい列の追加) であるため既存のアプリケーションを損なうことはありません。これを実行するには、次のステートメントを **Customer.cs** と **Order.cs** の先頭に追加します。
    
        using System.ComponentModel.DataAnnotations.Schema;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.ComponentModel.DataAnnotations;
        using System;

    その後、次のようにこれらの追加のプロパティを、それぞれのクラスに追加します。

        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        [Index]
        [TableColumn(TableColumnType.CreatedAt)]
        public DateTimeOffset? CreatedAt { get; set; }

        [TableColumn(TableColumnType.Deleted)]
        public bool Deleted { get; set; }

        [Index]
        [TableColumn(TableColumnType.Id)]
        [MaxLength(36)]
        public string Id { get; set; }

        [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
        [TableColumn(TableColumnType.UpdatedAt)]
        public DateTimeOffset? UpdatedAt { get; set; }

        [TableColumn(TableColumnType.Version)]
        [Timestamp]
        public byte[] Version { get; set; }

4. 先ほど追加したシステム プロパティには、データベースの操作で透過的に発生する組み込みの動作 (作成日時/更新日時の自動更新など) がいくつか備わっています。これらの動作を有効にするには、**ExistingContext.cs** を変更する必要があります。ファイルの先頭に、次を追加します。
    
        using System.Data.Entity.ModelConfiguration.Conventions;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.Linq;

    その後、次のように **ExistingContext** の本体で [**OnModelCreating**](http://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) をオーバーライドします。

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceTableColumn", (property, attributes) => attributes.Single().ColumnType.ToString()));

            base.OnModelCreating(modelBuilder);
        } 

5. ここで、いくつかのサンプル データをデータベースに入力します。ファイル **WebApiConfig.cs** を開きます。新しい [**IDatabaseInitializer**](http://msdn.microsoft.com/library/gg696323.aspx) を作成し、以下のように **Register** メソッド内で構成します。

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.Models;
        using System;
        using System.Collections.Generic;
        using System.Collections.ObjectModel;
        using System.Data.Entity;
        using System.Web.Http;

        namespace ShoppingService
        {
            public static class WebApiConfig
            {
                public static void Register()
                {
                    ConfigOptions options = new ConfigOptions();

                    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

                    Database.SetInitializer(new ExistingInitializer());
                }
            }

            public class ExistingInitializer : ClearDatabaseSchemaIfModelChanges<ExistingContext>
            {
                protected override void Seed(ExistingContext context)
                {
                    List<Order> orders = new List<Order>
                    {
                        new Order { OrderId = 10, Item = "Guitars", Quantity = 2, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 20, Item = "Drums", Quantity = 10, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 30, Item = "Tambourines", Quantity = 20, Id = Guid.NewGuid().ToString() }
                    };

                    List<Customer> customers = new List<Customer>
                    {
                        new Customer { CustomerId = 1, Name = "John", Orders = new Collection<Order> { 
                            orders[0]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 2, Name = "Paul", Orders = new Collection<Order> { 
                            orders[1]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 3, Name = "Ringo", Orders = new Collection<Order> { 
                            orders[2]}, Id = Guid.NewGuid().ToString()},
                    };

                    foreach (Customer c in customers)
                    {
                        context.Customers.Add(c);
                    }

                    base.Seed(context);
                }
            }
        }

<a name="Mapping"></a>
## DTO とモデルとの間にマッピングを確立する

ここまでで、**Customer** および **Order** というモデル型と **MobileCustomer** および **MobileOrder** という DTO を作成しましたが、この 2 つの間での自動的な変換をバックエンドに指示する必要があります。ここで Mobile Services は、[**AutoMapper**](http://automapper.org/) というオブジェクト リレーショナル マッパーを使用します。この機能は、プロジェクトで既に参照されています。

1. 次のコードを **WebApiConfig.cs** の先頭に追加します。

        using AutoMapper;
        using ShoppingService.DataObjects;

2. マッピングを定義するには、次のコードを **WebApiConfig** クラスの **Register** メソッドに追加します。

        Mapper.Initialize(cfg =>
        {
            cfg.CreateMap<MobileOrder, Order>();
            cfg.CreateMap<MobileCustomer, Customer>();
            cfg.CreateMap<Order, MobileOrder>()
                .ForMember(dst => dst.MobileCustomerId, map => map.MapFrom(x => x.Customer.Id))
                .ForMember(dst => dst.MobileCustomerName, map => map.MapFrom(x => x.Customer.Name));
            cfg.CreateMap<Customer, MobileCustomer>();

        });

これで、AutoMapper はオブジェクトを互いにマッピングします。対応する名前を持つプロパティはすべて照合されます。たとえば、**MobileOrder.CustomerId** は自動的に **Order.CustomerId** にマッピングされます。カスタム マッピングは、上のように構成できます。ここでは、**MobileCustomerName** プロパティを **Customer** リレーションシップ プロパティの **Name** プロパティにマッピングしています。

<a name="DomainManager"></a>
## ドメイン専用のロジックを実装する

次の手順では、[**MappedEntityDomainManager**](http://msdn.microsoft.com/library/dn643300.aspx) を実装します。これは、マッピングしたデータ ストアとクライアントからの HTTP トラフィックを処理するコントローラーとの間で抽象化レイヤーとして機能します。次のセクションでは、DTO という観点でのみコントローラーを記述できます。ここで追加する **MappedEntityDomainManager** は、元のデータ ストアとの通信を処理すると同時に、そのデータ ストア専用のロジックを実装する場所も提供します。

1. **MobileCustomerDomainManager.cs** をプロジェクトの **Models** フォルダーに追加します。次の実装を貼り付けます。

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Data.Entity;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileCustomerDomainManager : MappedEntityDomainManager<MobileCustomer, Customer>
            {
                private ExistingContext context;

                public MobileCustomerDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                public static int GetKey(string mobileCustomerId, DbSet<Customer> customers, HttpRequestMessage request)
                {
                    int customerId = customers
                       .Where(c => c.Id == mobileCustomerId)
                       .Select(c => c.CustomerId)
                       .FirstOrDefault();

                    if (customerId == 0)
                    {
                        throw new HttpResponseException(request.CreateNotFoundResponse());
                    }
                    return customerId;
                }

                protected override T GetKey<T>(string mobileCustomerId)
                {
                    return (T)(object)GetKey(mobileCustomerId, this.context.Customers, this.Request);
                }
                
                public override SingleResult<MobileCustomer> Lookup(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return LookupEntity(c => c.CustomerId == customerId);
                }

                public override async Task<MobileCustomer> InsertAsync(MobileCustomer mobileCustomer)
                {
                    return await base.InsertAsync(mobileCustomer);
                }

                public override async Task<MobileCustomer> UpdateAsync(string mobileCustomerId, Delta<MobileCustomer> patch)
                {
                    int customerId = GetKey<int>(mobileCustomerId);

                    Customer existingCustomer = await this.Context.Set<Customer>().FindAsync(customerId);
                    if (existingCustomer == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileCustomer existingCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);
                    patch.Patch(existingCustomerDTO);
                    Mapper.Map<MobileCustomer, Customer>(existingCustomerDTO, existingCustomer);

                    await this.SubmitChangesAsync();

                    MobileCustomer updatedCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);

                    return updatedCustomerDTO;
                }

                public override async Task<MobileCustomer> ReplaceAsync(string mobileCustomerId, MobileCustomer mobileCustomer)
                {
                    return await base.ReplaceAsync(mobileCustomerId, mobileCustomer);
                }

                public override async Task<bool> DeleteAsync(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return await DeleteItemAsync(customerId);
                }
            }
        }

    このクラスの重要な部分は、**GetKey** メソッドです。このメソッドでは、元のデータ モデルのオブジェクトの ID プロパティを探す方法を指示します。

2. 次のように **MobileOrderDomainManager.cs** をプロジェクトの **Models** フォルダーに追加します。

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileOrderDomainManager : MappedEntityDomainManager<MobileOrder, Order>
            {
                private ExistingContext context;

                public MobileOrderDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                protected override T GetKey<T>(string mobileOrderId)
                {
                    int orderId = this.context.Orders
                        .Where(o => o.Id == mobileOrderId)
                        .Select(o => o.OrderId)
                        .FirstOrDefault();

                    if (orderId == 0)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }
                    return (T)(object)orderId;
                }

                public override SingleResult<MobileOrder> Lookup(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return LookupEntity(o => o.OrderId == orderId);
                }

                private async Task<Customer> VerifyMobileCustomer(string mobileCustomerId, string mobileCustomerName)
                {
                    int customerId = MobileCustomerDomainManager.GetKey(mobileCustomerId, this.context.Customers, this.Request);
                    Customer customer = await this.context.Customers.FindAsync(customerId);
                    if (customer == null)
                    {
                        throw new HttpResponseException(Request.CreateBadRequestResponse("Customer with name '{0}' was not found", mobileCustomerName));
                    }
                    return customer;
                }

                public override async Task<MobileOrder> InsertAsync(MobileOrder mobileOrder)
                {
                    Customer customer = await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);
                    mobileOrder.CustomerId = customer.CustomerId;
                    return await base.InsertAsync(mobileOrder);
                }

                public override async Task<MobileOrder> UpdateAsync(string mobileOrderId, Delta<MobileOrder> patch)
                {
                    Customer customer = await VerifyMobileCustomer(patch.GetEntity().MobileCustomerId, patch.GetEntity().MobileCustomerName);

                    int orderId = GetKey<int>(mobileOrderId);

                    Order existingOrder = await this.Context.Set<Order>().FindAsync(orderId);
                    if (existingOrder == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileOrder existingOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);
                    patch.Patch(existingOrderDTO);
                    Mapper.Map<MobileOrder, Order>(existingOrderDTO, existingOrder);

                    // This is required to map the right Id for the customer
                    existingOrder.CustomerId = customer.CustomerId;

                    await this.SubmitChangesAsync();

                    MobileOrder updatedOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);

                    return updatedOrderDTO;
                }

                public override async Task<MobileOrder> ReplaceAsync(string mobileOrderId, MobileOrder mobileOrder)
                {
                    await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);

                    return await base.ReplaceAsync(mobileOrderId, mobileOrder);
                }

                public override Task<bool> DeleteAsync(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return DeleteItemAsync(orderId);
                }
            }
        }

    この例では、**InsertAsync** と **UpdateAsync** メソッドに注目します。これらのメソッドで、それぞれの **Order** に関連付けられた有効な **Customer** を必要とするリレーションシップを適用します。**InsertAsync** では、**Order.CustomerId** プロパティにマッピングされた **MobileOrder.CustomerId** プロパティに入力することが確認できます。この値は、一致する **MobileOrder.MobileCustomerId** で **Customer** を検索することで取得します。その理由は、既定でクライアントが認識するのは、**Customer** の Mobile Services の ID (MobileOrder.MobileCustomerId) だけであり、この ID は、外部キー (MobileOrder.CustomerId) を **Order** から **Customer** に設定するのに必要な実際のプライマリ キーとは異なるためです。これは、挿入操作を容易にするために、このサービスの内部でのみ使用されます。

これで、DTO をクライアントに公開するコントローラーを作成する準備ができました。

<a name="Controller"></a>
## DTO を使用して TableController を実装する

1. 次のように **Controllers** フォルダーで、ファイル **MobileCustomerController.cs** を追加します。

        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileCustomerController : TableController<MobileCustomer>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    var context = new ExistingContext();
                    DomainManager = new MobileCustomerDomainManager(context, Request, Services);
                }

                public IQueryable<MobileCustomer> GetAllMobileCustomers()
                {
                    return Query();
                }

                public SingleResult<MobileCustomer> GetMobileCustomer(string id)
                {
                    return Lookup(id);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PatchAsync(string id, Delta<MobileCustomer> patch)
                {
                    return base.UpdateAsync(id, patch);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PostAsync(MobileCustomer item)
                {
                    return base.InsertAsync(item);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task DeleteAsync(string id)
                {
                    return base.DeleteAsync(id);
                }
            }
        }

    AuthorizeLevel 属性を使用して、コントローラー上の挿入/更新/削除操作へのパブリック アクセスを制限していることに注意します。このシナリオでは、Customer の一覧は読み取り専用ですが、新しい Order を作成し、既存の顧客と関連付けることはできます。

2. 次のように、**Controllers** フォルダーに、ファイル **MobileOrderController.cs** を追加します。

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Description;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileOrderController : TableController<MobileOrder>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    ExistingContext context = new ExistingContext();
                    DomainManager = new MobileOrderDomainManager(context, Request, Services);
                }

                public IQueryable<MobileOrder> GetAllMobileOrders()
                {
                    return Query();
                }

                public SingleResult<MobileOrder> GetMobileOrder(string id)
                {
                    return Lookup(id);
                }

                public Task<MobileOrder> PatchMobileOrder(string id, Delta<MobileOrder> patch)
                {
                    return UpdateAsync(id, patch);
                }

                [ResponseType(typeof(MobileOrder))]
                public async Task<IHttpActionResult> PostMobileOrder(MobileOrder item)
                {
                    MobileOrder current = await InsertAsync(item);
                    return CreatedAtRoute("Tables", new { id = current.Id }, current);
                }

                public Task DeleteMobileOrder(string id)
                {
                    return DeleteAsync(id);
                }
            }
        }

3. これで、サービスを実行する準備が整いました。**F5** キーを押して、ヘルプ ページに組み込まれているテスト クライアントを使用してデータを変更します。

両方のコントローラーの実装が、DTO **MobileCustomer** および **MobileOrder** を専用で使用し、基になるモデルに依存しないことに注意してください。これらの DTO は容易に JSON にシリアル化して、すべてのプラットフォームで Mobile Services クライアント SDK とデータを交換するために使用できます。たとえば、Windows ストア アプリケーションを作成する場合、対応するクライアント側の型は以下のようになります。この型は、他のクライアント プラットフォームでも同様です。

    using Microsoft.WindowsAzure.MobileServices;
    using System;

    namespace ShoppingClient
    {
        public class MobileCustomer
        {
            public string Id { get; set; }

            public string Name { get; set; }

            [CreatedAt]
            public DateTimeOffset? CreatedAt { get; set; }

            [UpdatedAt]
            public DateTimeOffset? UpdatedAt { get; set; }

            public bool Deleted { get; set; }
            
            [Version]
            public string Version { get; set; }

        }

    }

次の手順では、サービスにアクセスするクライアント アプリケーションを作成できます。
<!--HONumber=54-->