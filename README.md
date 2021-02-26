## Mapper Examples

### Object To Object

Data:


```JSon
{
  "clientName": "Sharp",
  "clientAge": 15,
  "clientStreetAddress": "15 street 1",
  "clientStreetName": "Rue de avenue",
  "childFirstName": "Joe",
  "childMiddleName": "Heat",
  "childLastName": "Marg"
}
```

Schema:

```json
{
  "client": {
    "name": "@clientName",
    "age": "@clientAge",
    "address": {
      "streetNumber": "@clientStreetAddress",
      "streetName": "@clientStreetName"
    },
    "childName": "Mr. $concat @childFirstName $concat $with '' @childMiddleName $concat $with '-' @childLastName"
  }
}
```

Output:


```json
{
  "client": {
    "name": "Sharp",
    "age": 15,
    "address": {
      "streetNumber": "15 street 1",
      "streetName": "Rue de avenue"
    },
    "childName": "Mr. JoeHeat-Marg"
  }
}
```

------

### If Conditions

Data:

```json
{
  "client": {
    "name": "Joe",
    "dob": "2000"
  },
  "drivers": [
    {
      "dob": "1990",
      "id": "181",
      "isDeleted": true,
      "yearsInsured": "10",
      "gender": "male"
    }
  ]
}
```

Schema:

```json
{
  "field1": "$if @drivers[0].notfounddata $equal @drivers[0].notfounddata $return yes $otherwise $return no",
  "field2": "$if @drivers[0].dob $less than 1000 $return Hello $or @drivers[0].dob $greater than 1000 $return Yes",
  "field3": "$if @client.dob $greater than 1900 $return PEX (Cross / linked polyethylene) $otherwise $return no",
  "field4": "$if @client.dob $greater than 1900 $return yes $otherwise $return no",
  "field5": "$if @drivers[0].isDeleted $equal true $return yes $otherwise $return no",
  "field6": "$if @drivers[0].yearsInsured $greater than 2000 $return hello",
  "field7": "$if @drivers[0].id $equal 181 $return @drivers[0].gender",
  "field8": "$if @drivers[0].id $not equal 180 $return @drivers[0].dob $otherwise $return yes",
  "field9": "$if @drivers[0].id $not equal 181 $return no $otherwise $return @client.name"
}
```

Output:

```json
{
  "field1": "no",
  "field2": "Yes",
  "field3": "PEX (Cross / linked polyethylene)",
  "field4": "yes",
  "field5": "yes",
  "field6": null,
  "field7": "male",
  "field8": "1990",
  "field9": "Joe"
}
```

------

### Dates

Data:

```json
{
  "client": {
    "date": "2017-11-30T13:21:31.243"
  }
}
```

Schema:

```json
{
  "clientFullDate": "$date @client.date $format YYYY-MM-DD hh:mm:ss a",
  "clientDate": "$date @client.date $format YYYY-MM-DD",
  "clientDateTime": "$date @client.date $format hh:mm:ss a",
  "clientDateYear": "$date @client.date $format YYYY",
  "clientDateMonth": "$date @client.date $format MM",
  "clientDateDay": "$date @client.date $format DD",
  "clientDateHour": "$date @client.date $format hh",
  "clientDateMin": "$date @client.date $format mm",
  "clientDateSec": "$date @client.date $format ss",
  "clientDateAmPm": "$date @client.date $format a"
}
```

Output:

```json
{
  "clientFullDate": "2017-11-30 01:21:31 pm",
  "clientDate": "2017-11-30",
  "clientDateTime": "01:21:31 pm",
  "clientDateYear": "2017",
  "clientDateMonth": "11",
  "clientDateDay": "30",
  "clientDateHour": "01",
  "clientDateMin": "21",
  "clientDateSec": "31",
  "clientDateAmPm": "pm"
}
```

------

### Arrays

#### Array of objects to array of primitives (with filter)

Data

```json
{
  "phones": [
    {
      "number": "123",
      "id": "1"
    },
    {
      "number": "12",
      "id": "2"
    }
  ]
}
```

Schema:

```json
{
  "phones": [
    {
      "arrays": "@phones",
      "pick": "@this.number",
      "filter": "$if @this.number $greater than 100 $return true"
    }
  ]
}
```

Output:

```json
{
  "phones": ["123"]
}
```

If the filter is replaced with find, the result will be 

```json
{
  "phones": "123"
}
```

If the filter (or find) are removed, the result will be

```
{
  "phones": ["123", "12"]
}
```

#### Array of primitives to array of objects (with filter)

Data

```json
{
  "client": {
    "name": "joe"
  },

  "ids": [1,2,3,4,5]
}

```

Schema

```json
{
  "clientName": "@client.name",
  "idsObj": [
    {
      "arrays": "@ids",
      "filter": "$if @this $greater than 3 $return true",
      "map": {
        "id": "@this"
      }
    }
  ]
}
```

Output

```json
{
  "clientName": "joe",
  "idsObj": [
    {
      "id": 4
    },
    {
      "id": 5
    }
  ]
}
```

If the filter is replaced with find, result will be

```json
{
  "clientName": "joe",
  "idsObj":
   {
     "id": 4
   }
}
```

If the filter (or find) are removed, the result will be

```json
{
  "clientName": "joe",
  "idsObj": [
    {
      "id": 1
    },
    {
      "id": 2
    },
    {
      "id": 3
    },
    {
      "id": 4
    },
    {
      "id": 5
    }
  ]
}
```

#### Array of primitive to array of primitive (with filter)

Data

```json
{
  "client": {
    "name": "joe"
  },

  "ids": [1,2,3,4,5]
}
```

Schema

```json
{
  "idsObj": [
    {
      "arrays": "@ids",
      "filter": "$if @this $greater than 3 $return true",
      "pick": "@this"
    }
  ]
}
```

Output

```json
{
  "idsObj": [
    4, 5
  ]
}
```

If the filter is replaced with find, the result will be:

```json
{
   "idsObj": 4
}
```

If the filter and result are removed, the result will be:

```json
{
  "idsObj": [
    1,2,3,4,5
  ]
}

```

#### Array of objects to array of objects (with filter)

Data

```json
{
  "quoteId": "77",
  "cars": [
    {
      "id": "1",
      "model": "Jaguar",
      "year": 2000,
      "drivers": [
        {
          "id": "d1id1",
          "name": "Test1"
        },
        {
          "id": "d1id2",
          "name": "Test2"
        }
      ],
      "locations": [
        {
          "id": "1"
        },
        {
          "id": "2"
        }
      ]
    },
    {
      "id": "2",
      "model": "BMW",
      "year": 2012,
      "drivers": [
        {
          "id": "d2id1",
          "name": "Test12"
        },
        {
          "id": "d2id2",
          "name": "Test22"
        }
      ],
      "locations": [
        {
          "id": "3"
        },
        {
          "id": "4"
        }
      ]
    }
  ],
  "motorcycles": [
    {
      "id": "13",
      "model": "Harvey",
      "year": 2003,
      "drivers": [
        {
          "id": "dtest1",
          "name": "Test1"
        },
        {
          "id": "dtest2",
          "name": "Test2"
        }
      ],
      "locations": [
        {
          "id": "5"
        },
        {
          "id": "6"
        }
      ]
    }
  ]
}
```

Schema:

```json
{
  "vehicles": [
    {
      "arrays": "@cars $$and @motorcycles",
      "filter": "$if @this.year $greater than 2000 $return true",
      "map": {
        "details": {
          "id": "@this.id",
          "model": "@this.model",
          "year": "@this.year",
          "objectID": "@quoteId",
          "newValue": 2,
          "drivers": [
            {
              "arrays": "@this.drivers",
              "map": {
                "id": "@this.id",
                "parentID": "@this1.id"
              }
            }
          ],
          "ids": [
            {
              "arrays": "@this.drivers $$and @this.locations",
              "pick": "@this.id"
            }
          ]
        }
      }
    }
  ]
}
```

Output

```json
{
  "vehicles": [
    {
      "details": {
        "id": "2",
        "model": "BMW",
        "year": 2012,
        "objectID": "77",
        "newValue": 2,
        "drivers": [
          {
            "id": "d2id1",
            "parentID": "2"
          },
          {
            "id": "d2id2",
            "parentID": "2"
          }
        ],
        "ids": [
          "d2id1",
          "d2id2",
          "3",
          "4"
        ]
      }
    },
    {
      "details": {
        "id": "13",
        "model": "Harvey",
        "year": 2003,
        "objectID": "77",
        "newValue": 2,
        "drivers": [
          {
            "id": "dtest1",
            "parentID": "13"
          },
          {
            "id": "dtest2",
            "parentID": "13"
          }
        ],
        "ids": [
          "dtest1",
          "dtest2",
          "5",
          "6"
        ]
      }
    }
  ]
}
```

If the filter is replaced with find, the result will be

```json
{
  "vehicles": {
    "details": {
      "id": "2",
      "model": "BMW",
      "year": 2012,
      "objectID": "77",
      "newValue": 2,
      "drivers": [
        {
          "id": "d2id1",
          "parentID": "2"
        },
        {
          "id": "d2id2",
          "parentID": "2"
        }
      ],
      "ids": [
        "d2id1",
        "d2id2",
        "3",
        "4"
      ]
    }
  }
}
```

#### Constructing an array from non-array data (pick returns a primitive value while map returns an object)

Data

```json
{
  "clientName": "Sharp",
  "clientAge": 15,
  "clientStreetAddress": "15 street 1",
  "clientStreetName": "Rue de avenue",
  "childFirstName": "Joe",
  "childMiddleName": "Heat",
  "childLastName": "Marg",
  "vehicle_id_1": "a",
  "vehicle_id_2": "b",
  "vehicle_id_3": "c",
  "vehicle_id_4": "d",
  "vehicle_id_5": null,
  "driver_id_1": "1",
  "driver_id_2": "2",
  "driver_id_3": "3",
  "driver_id_4": "4",
  "driver_id_5": null
}
```

Schema

```json
{
  "client": {
    "name": "@clientName",
    "age": "@clientAge",
    "address": {
      "streetNumber": "@clientStreetAddress",
      "streetName": "@clientStreetName"
    },
    "childName": "Mr. $concat @childFirstName $concat $with '' @childMiddleName $concat $with '-' @childLastName",
    "anotherFormName": "Mr. $concat @childFirstName $concat @childLastName $concat @childMiddleName",
    "vehicles": [
      {
        "map": [
          {
            "id": "@vehicle_id_1",
            "drivers": [
              {
                "pick": [
                  "@driver_id_1"
                ]
              }
            ]
          },
          {
            "id": "@vehicle_id_2",
            "drivers": [
              {
                "pick": [
                  "@driver_id_2"
                ]
              }
            ]
          },
          {
            "id": "@vehicle_id_3",
            "drivers": [
              {
                "pick": [
                  "@driver_id_3"
                ]
              }
            ]
          },
          {
            "id": "@vehicle_id_4",
            "drivers": [
              {
                "pick": [
                  "@driver_id_4"
                ]
              }
            ]
          },
          {
            "id": "@vehicle_id_5",
            "drivers": [
              {
                "map": [
                  {
                    "id": "@driver_id_5"
                  }
                ]
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Output

```json
{
  "client": {
    "name": "Sharp",
    "age": 15,
    "address": {
      "streetNumber": "15 street 1",
      "streetName": "Rue de avenue"
    },

    "childName": "Mr. JoeHeat-Marg",
    "anotherFormName": "Mr. Joe Marg Heat",
    "vehicles": [
      {
        "id": "a",
        "drivers": ["1"]
      },
      {
        "id": "b",
        "drivers": ["2"]
      },
      {
        "id": "c",
        "drivers": ["3"]
      },
      {
        "id": "d",
        "drivers": ["4"]
      },
      {
        "id": null,
        "drivers": [{"id": null}]
      }
    ]
  }
}
```



------



### Value Mapping

Data

```json
{ "garage" : "Attached Garage - 1 Car" }

```

Schema:

```json
{
  "garage": {
    "this": {
      "Attached Garage - 1 Car": "attached",
      "Detached Garage - 1 Car": "dttached"
    },
    "capacity": {
      "Attached Garage - 1 Car": 1,
      "Detached Garage - 1 Car": 1
    }
  }
}
```

Output:

```json
{
  "garage": "attached",
  "capacity": 1
}
```

------

### Default Keyword

Data:

```json
{ "garage" : "New Value" }
```

Shema:

```json
{
  "garage": {
    "this": {
      "Attached Garage - 1 Car": "attached",
      "Detached Garage - 1 Car": "attached",
      "$default": "other"
    }
  }
}
```

Output:

```json
{ "garage" : "other" }
```

------

### Same Keyword

Data

```json
{ "attribute": "a" }
```

Schema:

```json
{
  "attribute": {
    "this": {
      "b": "1",
      "c": "2",
      "$default": "$same$"
    }
  }
}
```

Output:

```json
{ "attribute": "a" }
```

------

### Combined Test Case

Data:

```json
{
  "homeType": "TownHouse",
  "mainId": "2",
  "vehicles": [
    {
      "id": "1",
      "vehicleType": "something else",
      "drivers": [
        {
          "id": "D1"
        }
      ]
    },
    {
      "id": "2",
      "vehicleType": "car",
      "drivers": [
        {
          "id": "D2"
        },
        {
          "id": "D3"
        }
      ]
    }
  ],
  "homes": [
    {
      "id": "1"
    },
    {
      "id": "2"
    }
  ],
  "games": [
    {
      "types": [
        {
          "type": "console",
          "name": "type11"
        }
      ],
      "id": 2
    }
  ],
  "metaInfo": {
    "isValid": "TRUE",
    "name": "Test"
  }
}
```

Schema:

```json
{
  "homeType": {
    "this": {
      "home": "house",
      "condo": "condoHouse",
      "$default": "$same$"
    },
    "isHouse": {
      "home": true,
      "condo": false,
      "$default": null
    }
  },
  "vehicles": [
    {
      "vehicleType": {
        "this": {
          "car": "personal vehicle",
          "bus": "public transportation",
          "$default": "other"
        },
        "isPersonal": {
          "car": true,
          "bus": false,
          "$default": null
        }
      },
      "drivers": [
        {
          "id": {
            "this": {
              "D1": "driver1",
              "D2": "driver2",
              "D3": "driver3"
            }
          }
        }
      ]
    }
  ],
  "games": [
    {
      "types": [
        {
          "type": {
            "this": {
              "console": "gaming console"
            }
          }
        }
      ]
    }
  ],
  "metaInfo": {
    "isValid": {
      "this": {
        "TRUE": true,
        "FALSE": false,
        "$default": null
      }
    }
  }
}
```

Output:

```json
{
  "homeType": "TownHouse",
  "mainId": "2",
  "isHouse": null,
  "vehicles": [
    {
      "id": "1",
      "vehicleType": "other",
      "isPersonal": null,
      "drivers": [
        {
          "id": "driver1"
        }
      ]
    },
    {
      "id": "2",
      "vehicleType": "personal vehicle",
      "isPersonal": true,
      "drivers": [
        {
          "id": "driver2"
        },
        {
          "id": "driver3"
        }
      ]
    }
  ],
  "homes": [
    {
      "id": "1"
    },
    {
      "id": "2"
    }
  ],
  "games": [
    {
      "id": 2,
      "types": [
        {
          "type": "gaming console",
          "name": "type11"
        }
      ]
    }
  ],
  "metaInfo": {
    "isValid": true,
    "name": "Test"
  }
}
```

------

### Removing Undefined Values

Data:

```json
{
  "vehicles": [
    {
      "id": "1",
      "name": "vehicle1",
      "claims": [
        {
          "id": "a",
          "name": null
        },
        {
          "id": "b",
          "name": "c12"
        }
      ]
    },
    {
      "id": null,
      "name": "vehicle2",
      "claims": [
        {
          "id": "c",
          "name": "c21"
        },
        {
          "id": "d",
          "name": "c22"
        }
      ]
    }
  ],
  "motorcycles": [
    {
      "id": "3",
      "name": "motorcycle1",
      "claims": [
        {
          "id": "e",
          "name": "c31"
        }
      ]
    }
  ]
}
```

Schema:

```json
{
  "allClaims": [
    {
      "$$repeat$$": "@vehicles.claims $$and @motorcycles.claims",
      "id": "@this.id",
      "claimName": "@this.name",
      "parentID": "@this1.id",
      "parentName": "@this2.parentIdentifier"
    }
  ]
}
```

Output (if flag is set to true):

```json
{
  "allClaims": [
    {
      "id": "a",
      "claimName": null,
      "parentID": "1"
    },
    {
      "id": "b",
      "claimName": "c12",
      "parentID": "1"
    },
    {
      "id": "c",
      "claimName": "c21",
      "parentID": null
    },
    {
      "id": "d",
      "claimName": "c22",
      "parentID": null
    },
    {
      "id": "e",
      "claimName": "c31",
      "parentID": "3"
    }
  ]
}
```

Output (if flag is set to false):

```json
{
  "allClaims": [
    {
      "id": "a",
      "claimName": null,
      "parentID": "1",
      "parentName": undefined
    },
    {
      "id": "b",
      "claimName": "c12",
      "parentID": "1",
      "parentName": undefined
    },
    {
      "id": "c",
      "claimName": "c21",
      "parentID": null,
      "parentName": undefined
    },
    {
      "id": "d",
      "claimName": "c22",
      "parentID": null,
      "parentName": undefined
    },
    {
      "id": "e",
      "claimName": "c31",
      "parentID": "3",
      "parentName": undefined
    }
  ]
}
```

------

### Translate Paths

```js
const object = {
  "mainObjId": "77",
  "cars": [
    {
      "id": "1",
      "model": "Jaguar",
      "year": 2000,
      "drivers": [
        {
          "id": "d1id1",
          "name": "Test1"
        }, {
          "id": "d1id2",
          "name": "Test2"
        }
      ]
    }, {
      "id": "2",
      "model": "BMW",
      "year": 2012,
      "drivers": [
        {
          "id": "d2id1",
          "name": "Test12"
        }, {
          "id": "d2id2",
          "name": "Test22"
        }
      ]
    }
  ],

  "motorcycles": [
    {
      "id": "13",
      "model": "Harvey",
      "year": 2003,
      "drivers": [
        {
          "id": "dtest1",
          "name": "Test1"
        }, {
          "id": "dtest2",
          "name": "Test2"
        }
      ]
    }
  ]
};

const paths = ['cars.drivers.name', 'motorcycles.drivers.name'];
const actualPaths = sharpMapper.translatePaths(object, paths);

/* 
output
[
  "cars[0].drivers[0].name",
  "cars[0].drivers[1].name",
  "cars[1].drivers[0].name",
  "cars[1].drivers[1].name",
  "motorcycles[0].drivers[0].name",
  "motorcycles[0].drivers[1].name"
]
*/
```

### Enforce Arrays

```js
var object = {
  "data": {
    "policies": {
      "vehicles": {
        "Name": "test",
        "subValues": "a"
      },

      "houses": [
        {
          "Name": "h1",
          "subValues": "a"
        },
        {
          "Name": "h2",
          "subValues": ["a", "b"]
        }
      ]
    }
  }
}

var sharpMapper  =  require('sharp-mapper');
var writterPaths = ['data.policies', 'data.policies.vehicles', 'data.policies.vehicles.subValues', 'data.policies.houses', 'data.policies.houses.subValues', 'data.noarry'];
var updatedObject =  sharpMapper.enforceArrays(object, writtenPaths);

/*
result is
{
  "data": {
    "policies": [{
      "vehicles": [{
        "Name": "test",
        "subValues": ["a"]
      }],

      "houses": [
        {
          "Name": "h1",
          "subValues": ["a"]
        },
        {
          "Name": "h2",
          "subValues": ["a", "b"]
        }
      ]
    }]
  }
}
*/
```

