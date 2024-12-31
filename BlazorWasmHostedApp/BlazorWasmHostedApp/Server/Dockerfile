#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["BlazorWasmHostedApp/Server/BlazorWasmHostedApp.Server.csproj", "BlazorWasmHostedApp/Server/"]
COPY ["BlazorWasmHostedApp/Client/BlazorWasmHostedApp.Client.csproj", "BlazorWasmHostedApp/Client/"]
COPY ["BlazorWasmHostedApp/Shared/BlazorWasmHostedApp.Shared.csproj", "BlazorWasmHostedApp/Shared/"]
RUN dotnet restore "BlazorWasmHostedApp/Server/BlazorWasmHostedApp.Server.csproj"
COPY . .
WORKDIR "/src/BlazorWasmHostedApp/Server"
RUN dotnet build "BlazorWasmHostedApp.Server.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "BlazorWasmHostedApp.Server.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "BlazorWasmHostedApp.Server.dll"]