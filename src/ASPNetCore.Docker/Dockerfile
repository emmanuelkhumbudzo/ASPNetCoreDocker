#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base


RUN apt-get update \
    && apt-get install -y --allow-unauthenticated \
        libc6-dev \
        libgdiplus \
        libx11-dev \
     && rm -rf /var/lib/apt/lists/*

WORKDIR /app
EXPOSE 7008

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build

WORKDIR /src
COPY ["./src/.", ""]
RUN dotnet restore "./ASPNetCore.Docker/ASPNetCore.Docker.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "./ASPNetCore.Docker/ASPNetCore.Docker.csproj" -c Release -o /app

FROM build AS publish
RUN dotnet publish "./ASPNetCore.Docker/ASPNetCore.Docker.csproj" -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
RUN mkdir /app/LINUXFILEPATH

RUN sed -i 's/MinProtocol = TLSv1.2/MinProtocol = TLSv1/g' /etc/ssl/openssl.cnf
RUN sed -i 's/MinProtocol = TLSv1.2/MinProtocol = TLSv1/g' /usr/lib/ssl/openssl.cnf
RUN sed -i 's/DEFAULT@SECLEVEL=2/DEFAULT@SECLEVEL=1/g' /etc/ssl/openssl.cnf
RUN sed -i 's/DEFAULT@SECLEVEL=2/DEFAULT@SECLEVEL=1/g' /usr/lib/ssl/openssl.cnf

ENTRYPOINT ["dotnet", "ASPNetCore.Docker.dll"]