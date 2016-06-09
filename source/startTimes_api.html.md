---
title: go~mus Start Times API Documentation

language_tabs:
  - shell

toc_footers:
  - <a href='https://gomus.de'>go~mus</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---


# Introduction

Welcome to the go~mus API! You can use our API to access go~mus API endpoints, which can get information on various museums, exhibitions, products and tickets in our database.

We have examples using curl! You can view code examples in the dark area to the right.

# Index

This endpoint retrieves start times from all online available bookings.

There are 3 depths available for this endpoint. Which one you use will depend on the amount of information you need.

## HTTP Request

`GET https://smb-staging.gomus.de/api/v4/start_times`


## Query Parameters

```shell
curl "https://smb-staging.gomus.de/api/v4/products/start_times?start_at=2016-06-20&end_at=2016-06-25"
```

> The above command is for depth any and returns JSON structured like this:

```json
{
    "data": {
        "2016-06-20": false,
        "2016-06-21": false,
        "2016-06-22": false,
        "2016-06-23": true,
        "2016-06-24": true,
        "2016-06-25": true
    }
}
```

```shell
curl "https://smb-staging.gomus.de/api/v4/products/start_times?start_at=2016-06-22&end_at=2016-06-23&depth=products"
```

> The above command is for depth products and returns JSON structured like this:

```json
{
    "data": {
        "2016-06-22": [],
        "2016-06-23": [
            {
                "product": 100176
            }
        ]
    }
}
```

```shell
curl "https://smb-staging.gomus.de/api/v4/products/start_times?start_at=2016-06-22&end_at=2016-06-23&depth=all&product_ids[]=101208"
```

> The above command is for depth all and returns JSON structured like this:

```json
{
    "data": {
        "2016-06-22": [
            {
                "product": {
                    "id": 101208,
                    "start_times": []
                }
            }
        ],
        "2016-06-23": [
            {
                "product": {
                    "id": 101208,
                    "start_times": [
                        "2016-06-23T15:15:00+02:00",
                        "2016-06-23T16:30:00+02:00"
                    ]
                }
            }
        ]
    }
}
```

- depth (String), default: any, [any, products, all]
    - any, returns a boolean for each day depending on if at least one product is available
    - products, returns the booking ids of available bookings for each day
    - all, returns all information about each available booking for each day

- show_booked_out*, (Boolean), default: false, return bookend out bookings as well
    * only available for depth: all

- product_ids (Array), only return bookings for these products

- booking_types (Array), default: group, [group, single, courses]
    - group, return group bookings
    - single, return single bookings
    - courses, return course bookings

- start_at (Date), default: start of today, only return bookings with a start time after this date

- end_at (Date), default: end of day defined by start_at, only return bookings with a start time before this date

- participants (Integer), default: 10, only return bookings wtih at least this number of participants available

- group_by (String), default: day, [day, none]
    - day, return bookings grouped by day
    - none, return bookings with no grouping

- opening_hours_start, (Integer), default 9 * 60, only return bookings with start times after this number of minutes from the start of the day

- opening_hours_end, (Integer), default 22 * 60, only return bookings with start times before this number of minutes from the start of the day

# Show

This endpoint retrieves start times and availabilities for one product.

The responses for groups and single bookings differ slightly.

## HTTP Request

`GET https://smb-staging.gomus.de/api/v4/start_times`


## Query Parameters

```shell
curl "https://smb-staging.gomus.de/api/v4/products/start_times/101174?start_date=2016-07-01"
```

> The above command is for an event and returns JSON structured like this:

```json
{
    "data": {
        "2016-07-01 16:00:00 +0200": [
            {
                "id": 22988,
                "product_id": 101174,
                "title": "El Siglo de Oro",
                "sub_title": null,
                "description": null,
                "start_time": "2016-07-01T16:00:00+02:00",
                "seats": {
                    "max": 20,
                    "booked": 8,
                    "available": 12
                }
            }
        ]
    }
}
```

> The above command is for a group booking and returns JSON structured like this:

```json
{
    "data": {
        "2016-07-15 10:00:00 +0200": {
            "bookable": false
        },
        "2016-07-15 10:30:00 +0200": {
            "bookable": true,
            "participants": {
                "min": 1,
                "max": 25,
                "bookable": 25,
                "query": null
            }
        }
    }
}
```

- start_date (Date), default: today, only return bookings with a start time on this date

- participants (Integer), default: nil, only return bookings wtih at least this number of participants available

- show_booked_out*, (Boolean), default: false, return bookend out bookings as well
    * only available for single bookings