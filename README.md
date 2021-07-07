# Version.Net
An MsBuild package versioning configuration for our .NET projects.

## What does it do?

It provides a predictable and deterministic date-based SemVer versioning scheme.

It uses [SourceLink](https://github.com/dotnet/sourcelink/) and [GitInfo](https://github.com/devlooped/GitInfo/)
to add relevant versioning metadata to the artifacts created by dependent projects.

The implementation of SourceLink also facilitates additional debugging capabilities in IDEs like Visual Studio, VSCode, and Rider.

Dirty builds also have metadata to help identify their origin without exposing any private information.

## How do I use it?

In this documentation, the _solution level_ is assumed to be the directory of the solution (`.sln`) file,
typically at the repository root. The _project level_ refers to each of the the individual projects (`.csproj`) in the
solution themselves.

A submodule should also be added at the _solution level_.


The command line operation is as follows;
```sh
> git submodule add -- "git@github.com:StirlingLabs/Version.Net.git" "StirlingLabs.Version"
```


The resulting *.gitmodules* should read as follows;
```
[submodule "StirlingLabs.Version"]
	path = StirlingLabs.Version
	url = git@github.com:StirlingLabs/Version.Net.git
```

Also at the _solution level_, a *Version.proj* file should be created.


Content of *Version.proj*:
```xml
<Project>
    <PropertyGroup>
        <Authors>The Stirling Labs Team</Authors>
        <Owners>Stirling Labs</Owners>
        <Company>Stirling Labs</Company>
        <RepositoryType>git</RepositoryType>
        <RepositoryUrl>https://github.com/StirlingLabs/Utilities.Net.git</RepositoryUrl>
        <PackageProjectUrl>https://github.com/StirlingLabs/Utilities.Net</PackageProjectUrl>
        <GitDefaultBranch>main</GitDefaultBranch>
        <GitCommitsIgnoreMerges>true</GitCommitsIgnoreMerges>
    </PropertyGroup>
    <ImportGroup>
        <Import Project="SlVer/Version.proj" />
    </ImportGroup>
</Project>
```


At the _project level_, an import to the solution-level *Version.proj* file should be added.
```xml
  <ImportGroup>
    <Import Project="../Version.proj" />
  </ImportGroup>
```


Example *StirlingLabsExampleProject.csproj* file is as follows;
```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>net5.0</TargetFramework>
        <Title>Stirling Labs Example Project</Title>
    </PropertyGroup>
    
    <ImportGroup>
        <Import Project="../Version.proj"/>
    </ImportGroup>
</Project>

```
