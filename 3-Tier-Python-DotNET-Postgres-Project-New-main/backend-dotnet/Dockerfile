# syntax=docker/dockerfile:1

# ============================
# Stage 1 - Restore
# ============================

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build

WORKDIR /src

COPY ProjectOps.Api.csproj .

RUN --mount=type=cache,target=/root/.nuget/packages \
    dotnet restore

COPY . .

RUN --mount=type=cache,target=/root/.nuget/packages \
    dotnet publish \
      -c Release \
      -o /app/publish \
      --no-restore \
      /p:UseAppHost=false


# ============================
# Stage 2- Runtime
# ============================

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime

WORKDIR /app

ENV ASPNETCORE_URLS=http://+:8080

COPY --from=build \
     --chown=app:app \
     /app/publish .

USER app

EXPOSE 8080

ENTRYPOINT ["dotnet", "ProjectOps.Api.dll"]
