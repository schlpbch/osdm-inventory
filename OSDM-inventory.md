# OSDM inventory API

## Vehicle Availability

### Availability Request

#### Scenario: no `serviceLevel` or `placeProperty` preference

**POST /availabilities** and body

```json
{
  "trip": {
    "tripId": "1e287122-30e9-11ec-8d3d-0242ac130003",
    "origin": {
      "id": "8500010",
      "name": "Basel SBB" 
    },
    "destination": {
      "id": "8509000",
      "name": "Chur"
    },
    "startTime": "",
    "endTime": "",
    "transfers": 1,
    "tripLegs": [
      {
        "legId": 0,
        "timedLeg": {
          "legBoard": {
            "stopPlace": {
              "stopPlaceRef": "8500010",
              "stopPlaceName": "Basel SBB",
            },
            "serviceDeparture": {
              "timetabledTime": "2021-10-20T12:52:23:232Z",
            }
          },
          "legAlight": {
            "stopPlace": {
              "stopPlaceRef": "8509000",
              "stopPlaceName": "Chur",              
            },
             "serviceArrival": {
              "timetabledTime": "2021-10-20T12:52:23:232Z",
            }
          },
          "service:" {
            "publishedServiceName": "PE 905",
            "vehicleNumber":  "905",
            "operatorRef": "ICE 9"
          }
        }
      }
    ]
  }
}
```

#### Availability Response

```json
{
  "trip": {
    ..
    "tripLegs": [
      {
        "legId": 0,
        ..
      }
    ]
  },
  "availabilityPerLeg":  [
    {
      "legId": 0,
      "availableServices": [
        {
          "serviceType": "FIRST_CLASS_SEAT",
          "numericAvailability": 19,
          "placeProperties": [
            "WINDOW", "AISLE"
          ]
        },
        {
          "serviceType": "SECOND_CLASS_SEAT",
          "numericAvailability": 19,
          "placeProperties": [
            "WINDOW", "AISLE"
          ]
        },
        {
          "serviceType": "BIKE",
          "numericAvailability": 5,
          "placeProperties": [
            "BIKE", "ACC_BIKE"
          ]
        }
      ]
    }
  ]
}
```

#### Scenario: `serviceLevel: FIRST_CLASS_SEAT` only

**POST /availabilities** and body

```json
{
  "trip": {
    ..
    "tripLegs": [
      {
        "legId": 0,
        ..
      }
    ]
  },
  "requestedServiceLevels": [
    "FIRST_CLASS_SEAT"
  ]
}
```

#### Availability Response

```json
{
  "trip": {
    ..
    "tripLegs": [
      {
        "legId": 0,
        ..
      }
    ]
  },
  "availabilityPerLeg":  [
    {
      "legId": 0,
      "availableServices": [
        {
          "serviceType": "FIRST_CLASS_SEAT",
          "availableZones": [
            {
              "numericAvailability": 19,
              "zoneType": "BUSINESS",
              "placeProperties": [
                "WINDOW", "AISLE"
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

#### Scenario: `serviceLevel: FIRST_CLASS_SEAT` and `placeProperty: WINDOW` only

**POST /availabilities** and body

```json
{
  "trip": {
    ..
    "tripLegs": [
      {
        "legId": 0,
        ..
      }
    ]
  },
  "requestedServiceLevels": [
    "FIRST_CLASS_SEAT"
  ],
  "requestedPlaceProperties": [
    "WINDOW"
  ]
}
```

#### Availability Response

```json
{
  "trip": {
    ..
    "tripLegs": [
      {
        "legId": 0,
        ..
      }
    ]
  },
  "availabilityPerLeg": [
    {
      "legId": 0,
      "availableServices": [
        {
          "serviceType": "FIRST_CLASS_SEAT",
          "availableZones": [
            {
              "numericAvailability": 19,
              "zoneType": "BUSINESS",
              "placeProperties": [
                "WINDOW"
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

## Pre-Booking Places

### Pre-Booking Request

**POST /bookings/** and body

#### Scenario: Customer has chosen service level and/or place property

  ```json
  {
    "trip": {
      ..
      "tripLegs": [
        {
          "legId": 0,
          ..
        }
      ]
    },
    "selectionPerLeg": [
      {
        "legId": 0,
        "selectedOptions": [
          {
            "passenger": {
              "externalReference": "TK-ID-2314234",
              "firstName": "Patrick",
              "lastName": "Schweizer",
              "dateOfBirth": "1972-12-22",
              "eMail": "p.schweizer@bluewin.ch",
              "phoneNumber": "078 434 4434"
            },
            "serviceLevel": "FIRST_CLASS_SEAT",
            "placeProperties": [
              "WINDOW"
            ]
          }
        ]
      }
    ]
  }
  ```

#### Scenario: Customer has chosen a reference place

  ```json
  {
    "trip": {
      ..
      "tripLegs": [
        {
          "legId": 0,
          ..
        }
      ]
    },
    "selectionPerLeg": [
      {
        "legId": 0,
        "selectedReferencePlace": {
          "passengers": [
              {
                "externalReference": "TK-ID-2314234",
                "firstName": "Patrick",
                "lastName": "Schweizer",
                "dateOfBirth": "1972-12-22",
                "eMail": "p.schweizer@bluewin.ch",
                "phoneNumber": "078 434 4434"
              }
          ],
          "serviceLevel": "FIRST_CLASS_SEAT",
          "referencePlace": {
            "coachNumber": "23",
            "placeNumber": "12"
          }
        }
      }
    ]
  }
  ```

#### Scenario: Customer has chosen coach and place (on a seat map)

  ```json
  {
    "trip": {
      ..
      "tripLegs": [
        {
          "legId": 0,
          ..
        }
      ]
    },
    "selectionPerLeg": [
      {
        "legId": 0,
        "selectedCoaches": {
          "coachNumber": "2",
          "selectedPlaces": [
            {
              "passenger": {
                "externalReference": "TK-ID-2314234",
                "firstName": "Patrick",
                "lastName": "Schweizer",
                "dateOfBirth": "1972-12-22",
                "eMail": "p.schweizer@bluewin.ch",
                "phoneNumber": "078 434 4434"
              },
              "placeNumber": "2"
            }
          ]
        }
      }
    ]
  }
  ```

### PreBooking Reply

```json
{
  "bookingId": "d97ee98a-30ab-11ec-8d3d-0242ac130003",
  "state": "PREBOOKED",
  "trip": {
    ..
    "tripLegs": [
      {
        "legId": 0,
        ..
      }
    ]
  },
  "reservedPlaces": [
    {
      "passenger": {
        "externalReference": "TK-ID-2314234",
        "firstName": "Patrick",
        "lastName": "Schweizer",
        "dateOfBirth": "1972-12-22",
        "eMail": "p.schweizer@bluewin.ch",
        "phoneNumber": "078 434 4434"
      },
      "placePerLeg": [
        {
          "legId": 0,
          "place": {
            "vehicleNumber": "ICE 905",
            "coachNumber": "3",
            "placeNumber" "33",
            "zoneType": "BUSINESS",
            "placeProperties": [
              "WINDOW"
            ]
          }
        }
      ]
    }
  ]
}
```

## Confirm Booking

### Booking Request

**PATCH /bookings/{bookingId}** with body

```json
{
  "bookingId": "d97ee98a-30ab-11ec-8d3d-0242ac130003",
  "state": "CONFIRM",
}
```

### Booking Reply

```json
{
  "bookingId": "d97ee98a-30ab-11ec-8d3d-0242ac130003",
  "state": "CONFIRMED",
  "reservedPlaces": [
    {
      ..
    }
  ]
}
```

Returns `200 booking confirmed`

## Cancel Booking

### Cancel Booking Request

**DELETE /bookings/{bookingId}**

### Cancel Booking Reply

Returns `200 booking cancel`
