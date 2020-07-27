#build client app
FROM node:latest as build-client
WORKDIR /ClientApp
COPY ./ClientApp/package.json .
COPY ./ClientApp/package-lock.json .
RUN npm install

# copy client app files and build npm project
COPY ./ClientApp/ . 
RUN npm run build

FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.csproj .
RUN dotnet restore

# copy and publish app and libraries without client files build
COPY . .
ENV SkipNpmBuild true
RUN dotnet publish --no-restore -c release -o /app

# final stage/image
FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
WORKDIR /app
# copy .net core app
COPY --from=build /app . 
#copy front-end app
COPY --from=build-client ./ClientApp/build ./ClientApp/build
ENTRYPOINT ["dotnet", "SlowDistance.webApi.dll"]