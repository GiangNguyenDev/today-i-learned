# Cascade Delete

Source: https://learn.microsoft.com/en-us/ef/core/saving/cascade-delete

# Entity Framework Core Commands

## Add a migration

```console
dotnet ef migrations add InitialCreate
```

## Create database and apply migration to it

```console
dotnet ef database update
```

## Reset all migrations

```console
dotnet ef database update 0
dotnet ef migrations remove
```
