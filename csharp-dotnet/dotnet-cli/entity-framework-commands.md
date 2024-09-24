# Add a migration

```console
dotnet ef migrations add InitialCreate
```

# Create database and apply migration to it

```console
dotnet ef database update
```

# Reset all migrations

```console
dotnet ef database update 0
dotnet ef migrations remove
```

# Drop a database

```console
dotnet ef database drop -p <project> -s <startup-project>
```