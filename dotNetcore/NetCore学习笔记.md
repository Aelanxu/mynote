## .NetCore学习笔记1

- 项目目录与配置

```C#
public class Program
    {
        public static void Main(string[] args)
        {
            CreateHostBuilder(args).Build().Run(); //  Build()之后变成asp.net项目
        }
          // 配置控制台的方法
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>  // 载入配置文件的方法
                {
                    webBuilder.UseStartup<Startup>(); //   载入Stertup的类文件
                });
    }
```

```c#
    public class Startup
    {
		// 该方法载入依赖注入的配置
        public void ConfigureServices(IServiceCollection services) //IServiceCollection是接口
        {
            // 注册服务，把服务注册到IOC容器里
            // 注册一个MVC的方法，带Views
            services.AddControllersWithViews(); 
            // 该方法和上一个功能一样，只是不带Views,如作为WEBAPI 不需要带视图
            services.AddControllers();
        }
        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {   
            //  用于判断是否是开发模式
            if (env.IsDevelopment())
            {   
                // 展示错误信息到一个页面里
                app.UseDeveloperExceptionPage();
            }
            // 用以访问CSS,images,Js and etc 中间件
              app.UseStaticFiles();
            // 强制把http请求转为https请求	
             app.UseHttpsRedirection();
             // 身份认证中间件
            app.UserAuthentication()
            // 路由中间件
            app.UseRouting();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapGet("/", async context =>
                {
                    await context.Response.WriteAsync("Hello World!");
                });
            });
        }
    }
```

依赖注入（简称DI，全称：Dependency Injection）

IOC 容器（Inversion of Control）

步骤：

1. 注册服务
2. 请求实例
3. 实例的生命周期
   1. 生命周期
      1. Transient :服务每次被请求的时候都会生成新的实例；
      2. Scoped:web请求，一个WEB请求产生一个实例，当web请求被处理完成后生命周期结束；
      3. Singleton:服务的实例一旦被创建，以后所有的请求都会使用这个实例，一直存活到我们整个应用程序停止；

-  端点  （endpoint）

  就是进来的http请求的url的结尾那部分，这部分会被中间件进行处理。

- 管道（pipeline)

  ASP .NET Core 再处理http请求的时候 使用一个管道，返回也通过管道反回

- ASP .NET Core 应用的多样性

  -  MVC:/Home/Index
  - Razor Pages:/SomePage
  - SignalR:/Hub/Chat
