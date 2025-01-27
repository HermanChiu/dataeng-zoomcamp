
# Data Engineering Homework Module 1: Docker & SQL
Answers for module 1 Homework

## Question 1. Understanding docker first run 

Run docker with the `python:3.12.8` image in an interactive mode, use the entrypoint `bash`.

The version of `pip` in the image is
```
docker run --rm -it --entrypoint=bash python:3.12.8

pip --version
```

Answer:
```
pip 24.3.1 from /usr/local/lib/python3.12/site-packages/pip (python 3.12)
```

## Question 2. Understanding Docker networking and docker-compose

Given the following `docker-compose.yaml`, what is the `hostname` and `port` that **pgadmin** should use to connect to the postgres database?

Hostname: db

Port: 5432

Answer: 
```
db:5432
```

## Question 3. Trip Segmentation Count


During the period of October 1st 2019 (inclusive) and November 1st 2019 (exclusive), how many trips, **respectively**, happened:
1. Up to 1 mile
2. In between 1 (exclusive) and 3 miles (inclusive),
3. In between 3 (exclusive) and 7 miles (inclusive),
4. In between 7 (exclusive) and 10 miles (inclusive),
5. Over 10 miles 
#### 1. Up to 1 mile
```sql
SELECT COUNT(trip_distance)
FROM green_trip_data_2019
WHERE lpep_pickup_datetime >= '2019-10-01'
  AND lpep_dropoff_datetime < '2019-11-01'
  AND trip_distance <= 1;
```

#### 2. Between 1 and 3 miles

```sql
SELECT COUNT(trip_distance)
FROM green_trip_data_2019
WHERE lpep_pickup_datetime >= '2019-10-01'
  AND lpep_dropoff_datetime < '2019-11-01'
  AND trip_distance > 1
  AND trip_distance <= 3;
```

#### 3. Between 3 and 7 miles

```sql
SELECT COUNT(trip_distance)
FROM green_trip_data_2019
WHERE lpep_pickup_datetime >= '2019-10-01'
  AND lpep_dropoff_datetime < '2019-11-01'
  AND trip_distance > 3
  AND trip_distance <= 7;
```

#### 4. Between 7 and 10 miles

```sql
SELECT COUNT(trip_distance)
FROM green_trip_data_2019
WHERE lpep_pickup_datetime >= '2019-10-01'
  AND lpep_dropoff_datetime < '2019-11-01'
  AND trip_distance > 7
  AND trip_distance <= 10;
```

#### 5. Over 10 miles

```sql
SELECT COUNT(trip_distance)
FROM green_trip_data_2019
WHERE lpep_pickup_datetime >= '2019-10-01'
  AND lpep_dropoff_datetime < '2019-11-01'
  AND trip_distance > 10;
```

Answer:
```
104,802;  198,924;  109,603;  27,678;  35,189
```

## Question 4. Longest trip for each day

Which was the pick up day with the longest trip distance?
Use the pick up time for your calculations.

```sql
SELECT DATE (lpep_pickup_datetime) AS pickup_date,
   MAX (trip_distance) AS max_trip_distance
FROM green_trip_data_2019
WHERE lpep_pickup_datetime >= '2019-10-01' 
  AND lpep_pickup_datetime < '2019-11-01'
GROUP BY DATE (lpep_pickup_datetime)
ORDER BY max_trip_distance DESC LIMIT 1;
```
Answer:
```
2019-10-31
```

## Question 5. Three biggest pickup zones

Which were the top pickup locations with over 13,000 in
`total_amount` (across all trips) for 2019-10-18?

Consider only `lpep_pickup_datetime` when filtering by date.

```sql
SELECT z."Zone",SUM(gtd."total_amount") AS total_amount
FROM green_trip_data_2019 AS gtd
JOIN zones z ON gtd."PULocationID" = z."LocationID"
WHERE DATE (gtd."lpep_pickup_datetime") = '2019-10-18'
GROUP BY z."Zone"
HAVING SUM (gtd."total_amount") > 13000
ORDER BY total_amount DESC LIMIT 3;
```
Answer: 
```
East Harlem North
East Harlem South
Morningside Heights
```

## Question 6. Largest tip

For the passengers picked up in October 2019 in the zone
named "East Harlem North" which was the drop off zone that had
the largest tip?

```sql
SELECT z."Zone", gtd."tip_amount"
FROM green_trip_data_2019 AS gtd
JOIN zones AS z ON gtd."DOLocationID" = z."LocationID"
WHERE DATE (gtd."lpep_pickup_datetime") >= '2019-10-01'
  AND DATE (gtd."lpep_pickup_datetime") < '2019-11-01'
  AND gtd."PULocationID" = (SELECT "LocationID" FROM zones WHERE "Zone" = 'East Harlem North')
GROUP BY z."Zone"
ORDER BY total_tip DESC LIMIT 1;
```
Answer:
```
JFK Airport
```

## Question 7. Terraform Workflow

Which of the following sequences, **respectively**, describes the workflow for: 
1. Downloading the provider plugins and setting up backend,
2. Generating proposed changes and auto-executing the plan
3. Remove all resources managed by terraform`

Answer:
```
terraform init, terraform apply -auto-approve, terraform destroy
```