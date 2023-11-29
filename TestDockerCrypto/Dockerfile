#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443
ENV PATH="${PATH}:/opt/cprocsp/bin/amd64/"
ADD linux-amd64_deb.tgz /cryptopro
RUN /cryptopro/linux-amd64_deb/install.sh

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["nuget.config", "."]
COPY ["LibCore.Windows.2023.8.9.1.nupkg", "LibCore.Windows.2023.8.9.1.nupkg"]
COPY ["LibCore.Linux.2023.8.9.1.nupkg", "LibCore.Linux.2023.8.9.1.nupkg"]
COPY ["TestDockerCrypto/TestDockerCrypto.csproj", "TestDockerCrypto/"]
RUN dotnet restore "./TestDockerCrypto/./TestDockerCrypto.csproj"
COPY . .
WORKDIR "/src/TestDockerCrypto"
RUN dotnet build "./TestDockerCrypto.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./TestDockerCrypto.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "TestDockerCrypto.dll"]