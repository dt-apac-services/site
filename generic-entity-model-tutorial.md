summary: Generic Entity Model Tutorial
id: generic-entity-model-tutorial
categories: generic-entity-model
tags: generic-entity-model
status: Published 
authors: Adam Gardner
Feedback Link: https://dt-apac-services.github.io/site/

# Generic Entity Model Tutorial

## Overview 

The generic entity model in Dynatrace means you can represent **any** physical or logical "item" in Dynatrace. You are no longer restricted to hosts, process groups, processes, services and applications.

Metrics and events can be assigned to these entities.

Imagine you operate a car rental company. You probably want a way to represent `cars` in Dynatrace. Then you can retrieve the telemetry data from each car and attach it to the correct car in Dynatrace. Any faults with the car might be pushed into Dynatrace as a problem opening event. More minor events (such as notifications that a car requires a service) could be pushed as informational events.

![car list](assets/car_list.png)
![car 1](assets/car_1.png)

## Prerequisites

Although there is an extensive set of Dynatrace APIs, the authors of this codelab have done the hard work for you and created a helper script to create things for you.

You'll need:

- Python Installed
- Requests Module Installed: `pip install requests`
- A Dynatrace environment
- A Dynatrace API Token with permissions: `read settings`, `write settings`, `read entities`, `write entities` and `ingest metrics`

## Define Entity

Define what type of entity to create. In our case, a `car`.

Next define the properties (or facts) that you'd like your car to have, such as:

- Brand
- Model
- Colour
- Fuel Type
- Fuel Tank Capacity
- Current Hire Status (whether the car is currently hired out or not)

Create a JSON file which follows this syntax:

```
[{
    "name": "car",
    "attributes": [ "registration_number", "brand", "model", "colour", "tank_capacity", "hire_status" ]
}]
```

Save this JSON file.

## Create the Entity

Download the helper script from [here](https://github.com/dt-apac-innovators/generic-entity-model/blob/main/app.py) and save as `app.py` alongside your JSON file.

Run the helper script in dry run mode (no changes will be made):

```
python app.py --input=input.json --environment=https://<TENANT-ID>.live.dynatrace.com --token=dtc01.*** --dry-run
```

If all is OK, remove the `--dry-run` flag and your entity will be created:

```
python app.py --input=input.json --environment=https://<TENANT-ID>.live.dynatrace.com --token=dtc01.***
```

Note: This script will check whether entity types for the names you specified in `input.json` already exist. If they do exist, the script will not overwrite.

## Create Entities

The framework of your `car` object is now in Dynatrace. It is time to create some instances of `car` objects. Do this by pushing metrics to your `car`.

The helper script above automatically adds a `carid` dimension so each `car` must have a unique ID.

Push a metric that indicates that the `car` has been "discovered" (ie. it actually exists):

Modify the following command with your details. Notice the `1` just means we've discovered the car.
```
curl -X POST "https://<TENANT-ID>.live.dynatrace.com/api/v2/metrics/ingest" ^
-H "accept: */*" ^
-H "Authorization: Api-Token <API-TOKEN>" ^
-H "Content-Type: text/plain; charset=utf-8" ^
-d "entity.car.discovered,carid=ABC123,registration_number=ABC123,brand=Mercedes,model=C-Class,colour=Silver,tank_capacity=60,hire_status=spare 1"
```

You should get the following output:
```
{
  "linesOk":1,
  "linesInvalid":0,
  "error":null,
  "warnings":null
}
```

Repeat this process if you'd like to create additional `car`s. You must include **all** of your fields every time you push a metric.

## View Your Cars

Go to this URL to view a list of your cars:

```
https://<TENANT-ID>.live.dynatrace.com/ui/entity/list/entity:car
```

![car list](assets/car_list.png)

## Push Additional Metrics

You decide to push a metric to your car to track fuel consumption in real time.

The metric ID you decide on is `entity.car.fuel`

To signal that the car has 43 litres of fuel left in the tank, you'd push this:

```
curl -X POST "https://<TENANT-ID>.live.dynatrace.com/api/v2/metrics/ingest" ^
-H "accept: */*" -H "Authorization: Api-Token <API-TOKEN>" ^
-H "Content-Type: text/plain; charset=utf-8" ^
-d "entity.car.fuel,carid=ABC123,registration_number=ABC123,brand=Mercedes,model=C-Class,colour=Silver,tank_capacity=60,hire_status=spare 43"
```

Someone has just hired your car. Change the `hire_status` to `hired`:

```
curl -X POST "https://<TENANT-ID>.live.dynatrace.com/api/v2/metrics/ingest" ^
-H "accept: */*" ^
-H "Authorization: Api-Token <API-TOKEN>" ^
-H "Content-Type: text/plain; charset=utf-8" ^
-d "entity.car.discovered,carid=ABC123,registration_number=ABC123,brand=Mercedes,model=C-Class,colour=Silver,tank_capacity=60,hire_status=hired 1"
```

## Push Events To Cars

To push events to entities in Dynatrace, find the `CUSTOM_DEVICE` ID for each car:

```
curl -X GET "https://<TENANT-ID>.live.dynatrace.com/api/v2/entities?entitySelector=type(\"entity:car\"),registration_number(\"VO51123\")" ^
-H "accept: application/json; charset=utf-8" ^
-H "Authorization: Api-Token <API-TOKEN>"
```

Then tag that car with its unique registration number:

```
curl -X POST "https://<TENANT-ID>.live.dynatrace.com/api/v2/tags?entitySelector=type(\"entity:car\"),registration_number(\"VO51123\")" ^
-H "accept: application/json; charset=utf-8" ^
-H "Authorization: Api-Token <API-TOKEN>" ^
-H "Content-Type: application/json; charset=utf-8" ^
-d "{\"tags\":[{\"key\":\"registration_number\",\"value\":\"VO51123\"}]}"
```

This script retrieves each `car` entity where the `registration_number` exists and tags it appropriately. You should see this response:

```
{
  "matchedEntitiesCount":1,
  "appliedTags":[{
    "context":"CONTEXTLESS",
    "key":"registration_number",
    "value":"VO51123",
    "stringRepresentation":"registration_number:VO51123"
  }]
}
```

![car tag](assets/car_tag.png)

Push an event to your car (in this case, an event that opens a problem):

```
curl -X POST "https://<TENANT-ID>.live.dynatrace.com/api/v1/events" ^
-H "accept: application/json" ^
-H "Content-Type: application/json" ^
-H "Authorization: Api-Token <API-TOKEN>" ^
-d "{\"title\":\"problem title\",\"description\":\"problem description\",\"source\":\"Swagger API\",\"eventType\":\"ERROR_EVENT\",\"timeoutMinutes\":1,\"attachRules\":{\"entityIds\":[\"CUSTOM_DEVICE-5D477C401606B766\"],\"tagRule\":[{\"meTypes\":[\"CUSTOM_DEVICE\"],\"tags\":[{\"context\":\"CONTEXTLESS\",\"key\":\"registration_number\",\"value\":\"VO51123\"}]}]}}"
```

![car problem](assets/car_problem.png)

![car problem 2](assets/car_problem_2.png)