# Version.Net

**StirlingLabs/Version.Net** is an MsBuild package providing versioning configuration for our .NET projects.

<sub>[Like a Version](https://www.abc.net.au/triplej/like-a-version/) is a weekly live music segment on [Triple J](https://www.abc.net.au/triplej/) where musicians play one of their own songs plus a cover.</sub>
![Like a Version](./like-a-version.jpeg)

## What does it do?

It provides a predictable and deterministic date-based SemVer versioning scheme that Stirling Labs has standardised upon.

It uses [SourceLink](https://github.com/dotnet/sourcelink/) and [GitInfo](https://github.com/devlooped/GitInfo/) to add relevant versioning metadata to the artifacts created by dependent projects. The implementation of SourceLink also facilitates additional debugging capabilities in IDEs like Visual Studio, VSCode, and Rider. Dirty builds also have metadata to help identify their origin without exposing any private information.

## How do I use it?

To create a new version, just create a new tag in "vYY.M.update" format.  This can be done automatically for you;

```sh
> dotnet msbuild -t:CreateTag
```

If you have a solution with more than one project, you will get an error;
```
Microsoft (R) Build Engine version 16.9.0+57a23d249 for .NET
Copyright (C) Microsoft Corporation. All rights reserved.

MSBUILD : error MSB1011: Specify which project or solution file to use because this folder contains more than one project or solution file.

```

That's ok, you can just tell it to just use one of your projects in the solution.

```sh
> dotnet msbuild -t:CreateTag MyProject
```

or you can do it manually if you need the control;

```sh
> git tag -a v21.7.0 -m "My first version in July 2021"
```

...but please come up with a better message than that!

By default, the git push command doesn’t transfer tags to GitHub, so you will have to explicitly push tags after you have created them. This is just like sharing branches, we would push the tag we created above with;

```sh
> git push origin v21.7.0
```

## How do I install it?

In this documentation, the _solution level_ is assumed to be the directory of the solution (`.sln`) file,
typically at the repository root. The _project level_ refers to each of the the individual projects (`.csproj`) in the
solution themselves.

This repo should be added as a submodule at the _solution level_. The command line operation is as follows;

```sh
> git submodule add -b master -- "git@github.com:StirlingLabs/Version.Net.git" "StirlingLabs.Version"
```

The resulting *.gitmodules* should read as follows;

```
[submodule "StirlingLabs.Version"]
    path = StirlingLabs.Version
    url = git@github.com:StirlingLabs/Version.Net.git
    branch = master
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
        <Import Project="StirlingLabs.Version/Version.proj" />
    </ImportGroup>
</Project>
```

At the _project level_, each project file should be edited to import the solution-level *Version.proj* by adding:

```xml
  <ImportGroup>
    <Import Project="../Version.proj" />
  </ImportGroup>
```

Note: we are assuming projects are in a subdirectory immediately below the solution (hence `../`) as is best practice but adjust this as necessary.

For example, after editing, the *StirlingLabsExampleProject.csproj* file might look like this;

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
