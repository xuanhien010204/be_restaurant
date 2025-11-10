# Multi-stage Dockerfile placed in RestaurantManagement.Api/
# Build context should be repo root when building: docker build -f RestaurantManagement.Api/Dockerfile -t restaurant-api .

# Build stage
FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
WORKDIR /src

# Copy csproj files and restore (improves caching)
COPY RestaurantManagement.Domain/RestaurantManagement.Domain.csproj ./RestaurantManagement.Domain/
COPY RestaurantManagement.Application/RestaurantManagement.Application.csproj ./RestaurantManagement.Application/
COPY RestaurantManagement.Infrastructure/RestaurantManagement.Infrastructure.csproj ./RestaurantManagement.Infrastructure/
COPY RestaurantManagement.Api/RestaurantManagement.Api.csproj ./RestaurantManagement.Api/

RUN dotnet restore RestaurantManagement.Api/RestaurantManagement.Api.csproj

# Copy everything and publish
COPY . .
RUN dotnet publish RestaurantManagement.Api/RestaurantManagement.Api.csproj -c Release -o /app/publish

# Runtime stage
FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS runtime
WORKDIR /app
COPY --from=build /app/publish ./publish

# Expose container port (app will bind to runtime PORT)
EXPOSE 80

# Start app using runtime PORT environment variable; fallback to 80
ENTRYPOINT ["sh", "-c", "dotnet /app/publish/RestaurantManagement.Api.dll --urls \"http://0.0.0.0:${PORT:-80}\""]
