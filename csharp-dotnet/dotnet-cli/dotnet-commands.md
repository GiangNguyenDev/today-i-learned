# Common dotnet commands

`dotnet --info`

`dotnet new -l`

`dotnet new sln <solution name>` : Create new solution

`dotnet new <project name> -o <project folder>` : Create new project in a folder.

`dotnet sln add <project name>` : Add a project into a solution.

`dotnet add reference ../<project name>` : Add project reference to a project

`dotnet restore` : Restore all dependencies and tools of a project.

# Entity Framework and database

`dotnet tool install -g dotnet-ef`

`dotnet tool update -g dotnet-ef -v 8.0.8`

`dotnet ef migrations add <name> -o <output directory>`

`dotnet ef database update`

`dotnet ef database drop -p <project> -s <startup project>`

`dotnet ef migrations remove -p <project> -s <startup project>`

`dotnet ef migrations add <name> -p <project> -s <startup project> -o <output directory>`

# Run a project

`dotnet watch run`

# Certificate

`dotnet dev-certs https -t` : Trust development certificate for https with command