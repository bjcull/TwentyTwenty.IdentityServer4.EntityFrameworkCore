#20|20 IdentityServer4.EntityFrameworkCore

###Entity Framework Core persistence layer for [IdentityServer v4](https://github.com/IdentityServer/IdentityServer4)
[![Build status](https://ci.appveyor.com/api/projects/status/wnvka7rjwx66wjk5/branch/master?svg=true)](https://ci.appveyor.com/project/2020IP/twentytwenty-identityserver4-entityframeworkcore/branch/master)
[![NuGet](https://img.shields.io/nuget/v/TwentyTwenty.IdentityServer4.EntityFrameworkCore.svg)](https://www.nuget.org/packages/TwentyTwenty.IdentityServer4.EntityFrameworkCore/)

#### Usage
The primary key type can be configured for ClientStore and ScopeStore.  To facilitate this, subclass the `ClientConfigurationContext<TKey>` and `ScopeConfigurationContext<TKey>` with the desired key type.
```
public class ClientConfigurationContext : ClientConfigurationContext<Guid>
{
	public ClientConfigurationContext(DbContextOptions options)
		: base(options)
	{ }
}

public class ScopeConfigurationContext : ScopeConfigurationContext<Guid>
{
	public ScopeConfigurationContext(DbContextOptions options)
		: base(options)
	{ }
}
```
In the `Startup.cs`, register your DbContexts with Entity Framework
```
public void ConfigureServices(IServiceCollection services)
{
	...
	services.AddEntityFrameworkSqlServer()
		.AddDbContext<ClientConfigurationContext>(o => o
			.UseSqlServer(connectionString, b =>
			b.MigrationsAssembly(typeof(Startup).GetTypeInfo().Assembly.GetName().Name)))
		.AddDbContext<ScopeConfigurationContext>(o => o
			.UseSqlServer((connectionString, , b =>
			b.MigrationsAssembly(typeof(Startup).GetTypeInfo().Assembly.GetName().Name)))
		.AddDbContext<OperationalContextEx>(o => o
			.UseSqlServer((connectionString, , b =>
			b.MigrationsAssembly(typeof(Startup).GetTypeInfo().Assembly.GetName().Name)));
	...
}
```
Register the EFCore Contexts
```
public void ConfigureServices(IServiceCollection services)
{
	...
	var builder = services.AddIdentityServer(options =>
	{
		options.RequireSsl = false;
	});

	builder.ConfigureEntityFramework()
		.RegisterOperationalStores()
		.RegisterClientStore<Guid, ClientConfigurationContext>()
		.RegisterScopeStore<Guid, ScopeConfigurationContext<>();
	...
}
```
#### Contributing
To get started, [sign the Contributor License Agreement](https://www.clahub.com/agreements/2020IP/TwentyTwenty.IdentityServer4.EntityFrameworkCore).
