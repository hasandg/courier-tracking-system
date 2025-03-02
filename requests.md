# API Requests Documentation

This document contains sample API requests for testing and using the Courier Tracking System's microservices.

## Table of Contents
- [Location Service](#location-service)
- [Store Service](#store-service)
- [Distance Calculation Service](#distance-calculation-service)

---

## Location Service

Base URL: `http://localhost:8081/api/v1/locations`

### 1. Register a New Courier Location

```bash
# Register a new location for a courier
curl -X POST http://localhost:8081/api/v1/locations \
  -H "Content-Type: application/json" \
  -d '{
    "courierId": "c12345",
    "latitude": 40.9923307,
    "longitude": 29.1244229,
    "timestamp": "2023-06-10T15:30:45Z"
  }'
```

### 2. Get Courier Location History

```bash
# Get all locations for a specific courier
curl -X GET http://localhost:8081/api/v1/locations/courier/c12345 \
  -H "Accept: application/json"
```

### 3. Get Courier Locations by Time Range

```bash
# Get locations for a courier within a specific time range
curl -X GET "http://localhost:8081/api/v1/locations/courier/c12345/timerange?startTime=2023-06-10T00:00:00Z&endTime=2023-06-10T23:59:59Z" \
  -H "Accept: application/json"
```

### 5. Get All Courier IDs

```bash
# Get a list of all courier IDs in the system
curl -X GET http://localhost:8081/api/v1/locations/couriers \
  -H "Accept: application/json"
```

### 6. Delete Courier Location History

```bash
# Delete location history for a specific courier (admin operation)
curl -X DELETE http://localhost:8081/api/v1/locations/courier/c12345 \
  -H "Authorization: Bearer {admin-token}"
```

---

## Store Service

Base URL: `http://localhost:8082/api/v1/stores`

### 1. Get Store by ID

```bash
# Get store details by ID
curl -X GET http://localhost:8082/api/v1/stores/s789 \
  -H "Accept: application/json"
```

### 2. Get All Stores

```bash
# Get a list of all stores
curl -X GET http://localhost:8082/api/v1/stores \
  -H "Accept: application/json"
```

### 4. Get Stores by Geographic Area

```bash
# Get stores within a specific radius (in km) of a location
curl -X GET "http://localhost:8082/api/v1/stores/nearby?latitude=40.9923307&longitude=29.1244229&radius=5" \
  -H "Accept: application/json"
```

---

## Distance Calculation Service

Base URL: `http://localhost:8083/api/v1/distances`

### 1. Calculate Courier's Total Distance

```bash
# Calculate the total distance traveled by a courier in a day
curl -X GET "http://localhost:8083/api/v1/distances/courier/c12345/total?date=2023-06-10" \
  -H "Accept: application/json"
```

### 2. Get Courier's Travel Log

```bash
# Get detailed travel log for a courier for a specific day
curl -X GET "http://localhost:8083/api/v1/distances/courier/c12345/log?date=2023-06-10" \
  -H "Accept: application/json"
```

### 3. Get Distance Between Courier and Store

```bash
# Calculate current distance between a specific courier and store
curl -X GET "http://localhost:8083/api/v1/distances/between?courierId=c12345&storeId=s789" \
  -H "Accept: application/json"
```

### 4. Get Couriers Near Store

```bash
# Get a list of couriers within a specified distance (meters) of a store
curl -X GET "http://localhost:8083/api/v1/distances/store/s789/couriers?maxDistance=100" \
  -H "Accept: application/json"
```

### 5. Get Store Visit Events

```bash
# Get a list of store visit events by a courier
curl -X GET "http://localhost:8083/api/v1/distances/courier/c12345/visits?date=2023-06-10" \
  -H "Accept: application/json"
```

---

## Complex Scenarios

### 1. Courier Registration and First Location Update

```bash
# 1. Register a new courier (would be handled by a user/identity service)
# 2. First location update
curl -X POST http://localhost:8081/api/v1/locations \
  -H "Content-Type: application/json" \
  -d '{
    "courierId": "c99999",
    "latitude": 40.9923307,
    "longitude": 29.1244229,
    "timestamp": "2023-06-10T08:00:00Z"
  }'
```

### 2. Track Courier Movement Pattern

```bash
# 1. Get courier's location history
curl -X GET http://localhost:8081/api/v1/locations/courier/c12345 \
  -H "Accept: application/json"

# 2. Calculate total distance traveled
curl -X GET "http://localhost:8083/api/v1/distances/courier/c12345/total?date=2023-06-10" \
  -H "Accept: application/json"

# 3. Check store visits
curl -X GET "http://localhost:8083/api/v1/distances/courier/c12345/visits?date=2023-06-10" \
  -H "Accept: application/json"
```

### 3. Store Entry Detection

```bash
# 1. Courier approaches store (first location update near store)
curl -X POST http://localhost:8081/api/v1/locations \
  -H "Content-Type: application/json" \
  -d '{
    "courierId": "c12345",
    "latitude": 40.9925307,
    "longitude": 29.1246229,
    "timestamp": "2023-06-10T10:15:00Z"
  }'

# 2. Courier enters store (second location update very close to store)
curl -X POST http://localhost:8081/api/v1/locations \
  -H "Content-Type: application/json" \
  -d '{
    "courierId": "c12345",
    "latitude": 40.9923307,
    "longitude": 29.1244229,
    "timestamp": "2023-06-10T10:16:30Z"
  }'

# 3. Check if courier is near store
curl -X GET "http://localhost:8083/api/v1/distances/between?courierId=c12345&storeId=s789" \
  -H "Accept: application/json"

# 4. Get store visit events (should include this new visit)
curl -X GET "http://localhost:8083/api/v1/distances/courier/c12345/visits?date=2023-06-10" \
  -H "Accept: application/json"
```


### 4. System Health Check Workflow

```bash
# 1. Check location service health
curl -X GET http://localhost:8081/actuator/health \
  -H "Accept: application/json"

# 2. Check store service health
curl -X GET http://localhost:8082/actuator/health \
  -H "Accept: application/json"

# 3. Check distance calculation service health
curl -X GET http://localhost:8083/actuator/health \
  -H "Accept: application/json"
``` 