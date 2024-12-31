FROM nginx:alpine AS base
WORKDIR /usr/share/nginx/html
EXPOSE 80

FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["BlazorWasmStandaloneApp/BlazorWasmStandaloneApp.csproj", "BlazorWasmStandaloneApp/"]
RUN dotnet restore "BlazorWasmStandaloneApp/BlazorWasmStandaloneApp.csproj"
COPY . .
WORKDIR "/src/BlazorWasmStandaloneApp"
RUN dotnet build "BlazorWasmStandaloneApp.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "BlazorWasmStandaloneApp.csproj" -c $BUILD_CONFIGURATION -o /app/publish

FROM base AS final
WORKDIR /usr/share/nginx/html
COPY --from=publish /app/publish/wwwroot /usr/local/webapp/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf