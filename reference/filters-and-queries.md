# Querying and filtering the API

RealMassive has a large amount of data, and with it, many entities. In order to work with our data, it's important to be able to query and filter it properly to get the data that you want.

## Basic structure

The simplest query you can conduct is a plain GET request to a resource, such as `buildings`:

```bash
curl https://api.realmassive.com/buildings
```

This will return you a large list of buildings:

```json
{
  "meta":{
    "count":54849
  },
  "data":[
    {
      "relationships":{
        "spaces":{
          "links":{
            "related":"/buildings/1295335036706358273/spaces"
          }
        }
      },
      "attributes":{
        "updated":"2016-07-02T10:00:07.178080",
        "year_built":2006,
        "title":"Lpt-Vanguard",
        "deleted":false,
        "building_size":{
          "units":"sqft",
          "value":"123340.000000"
        },
        "building_class":"A",
        "created":"2015-02-17T02:01:46.125624",
        "building_type":"industrial",
        "address":{
          "city":"Scottsdale",
          "zipcode":"85260",
          "longitude":-111.8971548,
          "county":"Maricopa",
          "state":"AZ",
          "street":"8501 East Raintree Drive",
          "latitude":33.6174818
        }
      },
      "type":"buildings",
      "id":"1295335036706358273"
    },
    {
      "relationships":{
        "spaces":{
          "links":{
            "related":"/buildings/1295335036706358274/spaces"
          }
        }
      },
      "attributes":{
        "updated":"2016-07-02T10:00:08.333910",
        "title":"11030 David Taylor Drive",
        "deleted":false,
        "building_size":{
          "units":"sqft",
          "value":"100447.000000"
        },
        "building_class":"A",
        "created":"2015-01-19T01:50:34.152095",
        "building_type":"office",
        "address":{
          "city":"Charlotte",
          "zipcode":"28262",
          "longitude":-80.763208,
          "county":"Mecklenburg",
          "state":"NC",
          "street":"11030 David Taylor Drive",
          "latitude":35.340649
        }
      },
      "type":"buildings",
      "id":"1295335036706358274"
    },
    // ... more buildings
  ]
}
```

You can see the count of total entities available with the current query, **which includes the entities in the current payload**. That means that if you received 10 entities in this GET request, there are 54,839 entities left in the database (54,849 total count - 10 entities = 54,839 left).

## Getting individual and related entities

If you want to get a specific individual entity, you can query the route `/<entity-type>/<id>`. For example, to get a building shown above, we can GET https://api.realmassive.com/buildings/1295335036706358273:

```json
{
  "data":{
    "relationships":{
      "spaces":{
        "links":{
          "related":"/buildings/1295335036706358273/spaces"
        }
      }
    },
    "attributes":{
      "updated":"2016-07-02T10:00:07.178080",
      "year_built":2006,
      "title":"Lpt-Vanguard",
      "deleted":false,
      "building_size":{
        "units":"sqft",
        "value":"123340.000000"
      },
      "building_class":"A",
      "created":"2015-02-17T02:01:46.125624",
      "building_type":"industrial",
      "address":{
        "city":"Scottsdale",
        "zipcode":"85260",
        "longitude":-111.8971548,
        "county":"Maricopa",
        "state":"AZ",
        "street":"8501 East Raintree Drive",
        "latitude":33.6174818
      }
    },
    "type":"buildings",
    "id":"1295335036706358273"
  }
}
```

**Note that the `data` key in this response is now an object instead of an array.** All singular resources are returned in this way.

Let's say you now want to get the `spaces` for this building. The link is provided by the key `data.relationships.spaces.links.related`. Your application can consume this link, or you can visit the URL structure `/<entity-type>/<id>/<related-entity-type>`, which is consistent across all of our resources. For example, querying `https://api.realmassive.com/buildings/1295335036706358273/spaces` returns:

```json
{
  "meta":{
    "count":1
  },
  "data":[
    {
      "relationships":{
        "buildings":{
          "links":{
            "related":"/spaces/1295335205946524806/buildings/1295335036706358273"
          }
        },
        "leases":{
          "links":{
            "related":"/spaces/1295335205946524806/leases"
          }
        }
      },
      "attributes":{
        "updated":"2016-07-02T10:00:08.493430",
        "space_size":{
          "units":"sqft",
          "value":"123340.000000"
        },
        "created":"2015-02-17T02:01:46.248741",
        "deleted":false,
        "max_contiguous":{
          "units":"sqft",
          "value":"123340.000000"
        },
        "min_divisible":{
          "units":"sqft",
          "value":"123340.000000"
        }
      },
      "type":"spaces",
      "id":"1295335205946524806"
    }
  ]
}
```

Note that this response's `data` key _does_ use an array, because it represents a collection of entities, even though this building only happens to have a single space in the collection.

## Limits and offset

You can adjust the total number of results or the offset of the results with the `page[limit]` query param. For instance, with a GET request to `https://api.realmassive.com/buildings?page[limit]=1`, you get the following:

```json
{
  "meta": {
    "count": 54850
  },
  "data": [
    {
      "relationships": {
        "spaces": {
          "links": {
            "related": "/buildings/1295335036706358273/spaces"
          }
        }
      },
      "attributes": {
        "updated": "2016-07-02T10:00:07.178080",
        "year_built": 2006,
        "title": "Lpt-Vanguard",
        "deleted": false,
        "building_size": {
          "units": "sqft",
          "value": "123340.000000"
        },
        "building_class": "A",
        "created": "2015-02-17T02:01:46.125624",
        "building_type": "industrial",
        "address": {
          "city": "Scottsdale",
          "zipcode": "85260",
          "longitude": -111.8971548,
          "county": "Maricopa",
          "state": "AZ",
          "street": "8501 East Raintree Drive",
          "latitude": 33.6174818
        }
      },
      "type": "buildings",
      "id": "1295335036706358273"
    }
  ]
}
```

**Valid amounts for `page[limit]` are 1 to 100.** By default, the limit is automatically set to 10.

If you need to see more entities beyond the limit, you can add the `page[offset]` query param. This will cause the API to return the entities from the specified offset. So if a plain GET request to `https://api.realmassive.com/buildings` returns you the first 10 buildings from our API, then making the GET request for `https://api.realmassive.com/buildings?page[offset]=10` will return you the next 10 buildings:

```json
{
  "meta":{
    "count":54840
  },
  "data":[
    {
      "relationships":{
        "spaces":{
          "links":{
            "related":"/buildings/1295335036714746891/spaces"
          }
        },
        "attachments":{
          "links":{
            "related":"/buildings/1295335036714746891/attachments"
          }
        }
      },
      "attributes":{
        "updated":"2016-07-02T10:00:18.922150",
        "year_built":1998,
        "title":"Ardenwood Business Park",
        "deleted":false,
        "building_size":{
          "units":"sqft",
          "value":"48240.000000"
        },
        "created":"2015-03-02T01:58:10.752098",
        "building_type":"office",
        "address":{
          "city":"Fremont",
          "zipcode":"94555",
          "longitude":-122.0680522,
          "county":"Alameda",
          "state":"CA",
          "street":"34781 Campus Drive",
          "latitude":37.5476605
        },
        "building_subtype":"medical"
      },
      "type":"buildings",
      "id":"1295335036714746891"
    },
    {
      "relationships":{
        "spaces":{
          "links":{
            "related":"/buildings/1295335036714746892/spaces"
          }
        },
        "attachments":{
          "links":{
            "related":"/buildings/1295335036714746892/attachments"
          }
        }
      },
      "attributes":{
        "build_status":"existing",
        "updated":"2016-07-02T10:00:20.448280",
        "year_built":1999,
        "title":"One Peachtree Pointe",
        "deleted":false,
        "building_size":{
          "units":"sqft",
          "value":"160000.000000"
        },
        "building_class":"A",
        "building_type":"office",
        "created":"2015-05-27T12:44:31.955160",
        "tenancy":"multiple",
        "address":{
          "city":"Atlanta",
          "zipcode":"30309",
          "longitude":-84.3882065,
          "county":"Fulton County",
          "state":"GA",
          "street":"1545 Peachtree Street Northeast",
          "latitude":33.7972669
        }
      },
      "type":"buildings",
      "id":"1295335036714746892"
    },
    // ... more buildings
  ]
}
```

Note that the count has changed--it has decreased from `54850` to `54840`. By combining `page[limit]` and `page[offset]` you can implement pagination in your application. For instance, the following queries will, altogether, return 300 buildings:

	1. GET https://api.realmassive.com/buildings?page[limit]=100
	2. GET https://api.realmassive.com/buildings?page[limit]=100&page[offset]=100
	3. GET https://api.realmassive.com/buildings?page[limit]=100&page[offset]=200

## Sorting

We've been working with the open `/buildings` route. **However, by default there is no guaranteed sort order.** Therefore, most of the time you will want to apply a sort order explicitly with the `sort` property. You can use it in the format `sort=<attribute>`. This can be any valid attribute for the entity type. For instance, with buildings all of these are valid:

- GET https://api.realmassive.com/buildings?sort=created
- GET https://api.realmassive.com/buildings?sort=updated
- GET https://api.realmassive.com/buildings?sort=building_type
- GET https://api.realmassive.com/buildings?sort=building_type.value
- GET https://api.realmassive.com/buildings?sort=address.county

You'll note that for nested attributes, you use a dot (`.`) character to dereference to the inner attribute. **By default, sorting is in ascending order.** You can get descending order by prepending the attribute with a negative sign (`-`). If you wanted to get the most recently created buildings, you would make a GET request to `https://api.realmassive.com/buildings?sort=-created`.

## Includes

Entities which are related to other entities in the API are shown with a `relationships` property object. For instance, this building has related `spaces` and `attachments` entities, indicated by the `relationships` key.

```json
{
  "relationships":{
    "spaces":{
      "links":{
        "related":"/buildings/1295335040464455176/spaces"
      }
    },
    "attachments":{
      "links":{
        "related":"/buildings/1295335040464455176/attachments"
      }
    }
  },
  "attributes":{
    "updated":"2016-07-02T10:15:49.007040",
    "year_built":1973,
    "title":"6555 Burnet Road",
    "deleted":false,
    "building_size":{
      "units":"sqft",
      "value":"9500.000000"
    },
    "created":"2014-03-18T15:34:14.714170",
    "building_type":"retail",
    "address":{
      "city":"Austin",
      "zipcode":"78757",
      "longitude":-97.738221,
      "county":"Travis",
      "state":"TX",
      "street":"6555 Burnet Road",
      "latitude":30.341443
    },
    "air_conditioned":true
  },
  "type":"buildings",
  "id":"1295335040464455176"
}
```

You could query the links provided to find the specific collection associated with this building, but if you are working with multiple buildings, it becomes cumbersome and unperformant to make a request for each building's entities. Instead, you can use the `include` keyword, which tells the API to return the related entities inside of an `include` property next to the `data` property. Here's an example of using the `include` property on an individual entity:

`GET https://api.realmassive.com/buildings/1295335036706358273?include=spaces`

```json
{
  "included":[
    {
      "attributes":{
        "updated":"2016-07-02T10:00:08.493430",
        "space_size":{
          "units":"sqft",
          "value":"123340.000000"
        },
        "created":"2015-02-17T02:01:46.248741",
        "deleted":false,
        "max_contiguous":{
          "units":"sqft",
          "value":"123340.000000"
        },
        "min_divisible":{
          "units":"sqft",
          "value":"123340.000000"
        }
      },
      "type":"spaces",
      "id":"1295335205946524806"
    }
  ],
  "data":{
    "relationships":{
      "spaces":{
        "data":[
          {
            "type":"spaces",
            "id":"1295335205946524806"
          }
        ],
        "links":{
          "related":"/buildings/1295335036706358273/spaces"
        }
      }
    },
    "attributes":{
      "updated":"2016-07-02T10:00:07.178080",
      "year_built":2006,
      "title":"Lpt-Vanguard",
      "deleted":false,
      "building_size":{
        "units":"sqft",
        "value":"123340.000000"
      },
      "building_class":"A",
      "created":"2015-02-17T02:01:46.125624",
      "building_type":"industrial",
      "address":{
        "city":"Scottsdale",
        "zipcode":"85260",
        "longitude":-111.8971548,
        "county":"Maricopa",
        "state":"AZ",
        "street":"8501 East Raintree Drive",
        "latitude":33.6174818
      }
    },
    "type":"buildings",
    "id":"1295335036706358273"
  }
}
```

Note that the space was returned as well, inside of the `included` key. Also note that the `data.relationships.spaces` key now has a `data` array property which contains a list of objects corresponding to the included objects.

You can also include entities related to a collection. For example, here is an example of requesting two buildings and also including their spaces:

`GET https://api.realmassive.com/buildings?include=spaces&page[limit]=2`

```json
{
  "included":[
    {
      "attributes":{
        "updated":"2016-07-02T10:00:08.493430",
        "space_size":{
          "units":"sqft",
          "value":"123340.000000"
        },
        "created":"2015-02-17T02:01:46.248741",
        "deleted":false,
        "max_contiguous":{
          "units":"sqft",
          "value":"123340.000000"
        },
        "min_divisible":{
          "units":"sqft",
          "value":"123340.000000"
        }
      },
      "type":"spaces",
      "id":"1295335205946524806"
    },
    {
      "attributes":{
        "updated":"2016-07-02T10:00:15.291820",
        "space_size":{
          "units":"sqft",
          "value":"100447.000000"
        },
        "created":"2015-01-19T01:50:34.433841",
        "deleted":false,
        "max_contiguous":{
          "units":"sqft",
          "value":"100447.000000"
        },
        "min_divisible":{
          "units":"sqft",
          "value":"100447.000000"
        }
      },
      "type":"spaces",
      "id":"1295335179388191959"
    }
  ],
  "meta":{
    "count":54850
  },
  "data":[
    {
      "relationships":{
        "spaces":{
          "data":[
            {
              "type":"spaces",
              "id":"1295335205946524806"
            }
          ],
          "links":{
            "related":"/buildings/1295335036706358273/spaces"
          }
        }
      },
      "attributes":{
        "updated":"2016-07-02T10:00:07.178080",
        "year_built":2006,
        "title":"Lpt-Vanguard",
        "deleted":false,
        "building_size":{
          "units":"sqft",
          "value":"123340.000000"
        },
        "building_class":"A",
        "created":"2015-02-17T02:01:46.125624",
        "building_type":"industrial",
        "address":{
          "city":"Scottsdale",
          "zipcode":"85260",
          "longitude":-111.8971548,
          "county":"Maricopa",
          "state":"AZ",
          "street":"8501 East Raintree Drive",
          "latitude":33.6174818
        }
      },
      "type":"buildings",
      "id":"1295335036706358273"
    },
    {
      "relationships":{
        "spaces":{
          "data":[
            {
              "type":"spaces",
              "id":"1295335179388191959"
            }
          ],
          "links":{
            "related":"/buildings/1295335036706358274/spaces"
          }
        }
      },
      "attributes":{
        "updated":"2016-07-02T10:00:08.333910",
        "title":"11030 David Taylor Drive",
        "deleted":false,
        "building_size":{
          "units":"sqft",
          "value":"100447.000000"
        },
        "building_class":"A",
        "created":"2015-01-19T01:50:34.152095",
        "building_type":"office",
        "address":{
          "city":"Charlotte",
          "zipcode":"28262",
          "longitude":-80.763208,
          "county":"Mecklenburg",
          "state":"NC",
          "street":"11030 David Taylor Drive",
          "latitude":35.340649
        }
      },
      "type":"buildings",
      "id":"1295335036706358274"
    }
  ]
}
```

Important notes for including resources:

- **If you include a resource, there is no limit imposed upon the number of included entities that may be pulled in.** This is unlike the hard max limit of 100 for the number of entities that may be return in top-level collections. So if you include `space` entities for 10 buildings, each of which have 20 spaces apiece, you will receive 200 space entities inside the `include` array.
- **The API does not automatically nest your entities for you**. Unfortunately, you must map back between the `included` entities and primary entity in order to build a data structure where these entities are explicitly related. We are aware that this is inconvenient and hope to work on solutions to improve this programmatic experience in the future. In the meantime, we recommend building interstitial maps in memory that represent the links between the entity types you are working with which you can access as needed in your application.
- **The included entities do not have their relationship data or related links included.** Note how the spaces included in the above link do not have a `relationships` property, even though in reality that space is associated to a lease.

You can also include multiple related entity types at once by separating them with a comma. For instance, if I wanted to get the attachments, sales, and spaces for a building all at once I can do:

`GET https://api.realmassive.com/buildings/1295335036823799010?include=attachments,sales,spaces`

This returns the entity with the sales, attachments, and spaces entities included:

```json
{
  "included":[
    {
      "attributes":{
        "updated":"2017-04-12T17:46:07.753250",
        "created":"2017-04-12T17:46:07.753250",
        "price":{
          "units":"usd",
          "value":"4200000.000000"
        },
        "archived":false
      },
      "type":"sales",
      "id":"1491487338805593555"
    },
    {
      "attributes":{
        "category":"exterior",
        "updated":"2017-04-12T17:45:39.897753",
        "created":"2017-04-12T17:45:39.897753"
      },
      "type":"attachments",
      "id":"1491487105132528941"
    },
    {
      "attributes":{
        "category":"brochure",
        "updated":"2017-04-12T17:45:39.898334",
        "created":"2017-04-12T17:45:39.898334"
      },
      "type":"attachments",
      "id":"1491487105140917550"
    },
    {
      "attributes":{
        "category":"exterior",
        "updated":"2016-07-16T02:27:31.910826",
        "created":"2016-07-16T02:27:31.910826",
        "deleted":false
      },
      "type":"attachments",
      "id":"1295335546171688809"
    },
    {
      "attributes":{
        "category":"exterior",
        "updated":"2016-07-16T02:27:31.910879",
        "created":"2016-07-16T02:27:31.910879",
        "deleted":false
      },
      "type":"attachments",
      "id":"1295335546171688812"
    },
    {
      "attributes":{
        "updated":"2016-07-02T10:33:46.293330",
        "space_size":{
          "units":"sqft",
          "value":"110203.000000"
        },
        "description":"The large building is located on the well-traveled Colonial Drive, west of the fairgrounds. The property is ideal for a variety of uses, including showroom, bulk storage and industrial. Seller financing is available.\n\u00e2\u20ac\u00a2Water/Sewer: City\n\u00e2\u20ac\u00a2Fire Safety: Sprinklered\n\u00e2\u20ac\u00a2Roof: 2007 - 15 Year Warranty\n\u00e2\u20ac\u00a2Traffic Count: 47,000 AADT",
        "created":"2015-05-04T18:57:20.435400",
        "deleted":false,
        "max_contiguous":{
          "units":"sqft",
          "value":"110203.000000"
        },
        "min_divisible":{
          "units":"sqft",
          "value":"110203.000000"
        }
      },
      "type":"spaces",
      "id":"1295335177450423843"
    }
  ],
  "data":{
    "relationships":{
      "spaces":{
        "data":[
          {
            "type":"spaces",
            "id":"1295335177450423843"
          }
        ],
        "links":{
          "related":"/buildings/1295335036823799010/spaces"
        }
      },
      "sales":{
        "data":[
          {
            "type":"sales",
            "id":"1491487338805593555"
          }
        ],
        "links":{
          "related":"/buildings/1295335036823799010/sales"
        }
      },
      "attachments":{
        "data":[
          {
            "type":"attachments",
            "id":"1491487105132528941"
          },
          {
            "type":"attachments",
            "id":"1491487105140917550"
          },
          {
            "type":"attachments",
            "id":"1295335546171688809"
          },
          {
            "type":"attachments",
            "id":"1295335546171688812"
          }
        ],
        "links":{
          "related":"/buildings/1295335036823799010/attachments"
        }
      }
    },
    "attributes":{
      "build_status":"existing",
      "updated":"2016-07-02T10:09:38.402000",
      "title":"4815 West Colonial Drive",
      "year_renovated":2007,
      "building_size":{
        "units":"sqft",
        "value":"110203.000000"
      },
      "created":"2015-01-12T02:40:18.409322",
      "lot_size":{
        "units":"sqft",
        "value":"3.360000"
      },
      "deleted":false,
      "building_type":"retail",
      "address":{
        "city":"Orlando",
        "zipcode":"32808",
        "longitude":-81.443158,
        "county":"orange",
        "state":"FL",
        "street":"4815 West Colonial Drive",
        "latitude":28.553185
      }
    },
    "type":"buildings",
    "id":"1295335036823799010"
  }
}
```

### Nested includes

You can also include entities that are related to the entities you just included, a feature we call "nested includes". This is done simply by appending a dot to the included entity type and indicating the deeply-related entity types to include. For instance, if I wanted to pull in the spaces and leases and subleases asociated with this building, I would do:

`GET https://api.realmassive.com/buildings/1295335036823799010?include=spaces.leases,spaces.subleases`

Note how we need to separate `spaces.leases` from `spaces.subleases` with a comma. We get this result:

```json
{
  "included":[
    {
      "relationships":{
        "subleases":{
          "data":[
            {
              "type":"subleases",
              "id":"1491489996182390587"
            }
          ],
          "links":{
            "related":"/spaces/1295335177450423843/subleases"
          }
        },
        "leases":{
          "data":[
            {
              "type":"leases",
              "id":"1295335177450423844"
            }
          ],
          "links":{
            "related":"/spaces/1295335177450423843/leases"
          }
        }
      },
      "attributes":{
        "updated":"2016-07-02T10:33:46.293330",
        "space_size":{
          "units":"sqft",
          "value":"110203.000000"
        },
        "description":"The large building is located on the well-traveled Colonial Drive, west of the fairgrounds. The property is ideal for a variety of uses, including showroom, bulk storage and industrial. Seller financing is available.\n\u00e2\u20ac\u00a2Water/Sewer: City\n\u00e2\u20ac\u00a2Fire Safety: Sprinklered\n\u00e2\u20ac\u00a2Roof: 2007 - 15 Year Warranty\n\u00e2\u20ac\u00a2Traffic Count: 47,000 AADT",
        "created":"2015-05-04T18:57:20.435400",
        "deleted":false,
        "max_contiguous":{
          "units":"sqft",
          "value":"110203.000000"
        },
        "min_divisible":{
          "units":"sqft",
          "value":"110203.000000"
        }
      },
      "type":"spaces",
      "id":"1295335177450423843"
    },
    {
      "attributes":{
        "archived":false,
        "updated":"2017-04-12T17:51:24.537146",
        "created":"2017-04-12T17:51:24.537146"
      },
      "type":"subleases",
      "id":"1491489996182390587"
    },
    {
      "attributes":{
        "updated":"2016-07-02T10:33:46.293330",
        "created":"2015-05-04T18:57:20.435400",
        "deleted":false,
        "archived":true,
        "rate":{
          "units":"dollar_per_sf",
          "frequency":"yearly",
          "type":"triple_net"
        }
      },
      "type":"leases",
      "id":"1295335177450423844"
    }
  ],
  "data":{
    "relationships":{
      "spaces":{
        "data":[
          {
            "type":"spaces",
            "id":"1295335177450423843"
          }
        ],
        "links":{
          "related":"/buildings/1295335036823799010/spaces"
        }
      },
      "sales":{
        "links":{
          "related":"/buildings/1295335036823799010/sales"
        }
      },
      "attachments":{
        "links":{
          "related":"/buildings/1295335036823799010/attachments"
        }
      }
    },
    "attributes":{
      "build_status":"existing",
      "updated":"2016-07-02T10:09:38.402000",
      "title":"4815 West Colonial Drive",
      "year_renovated":2007,
      "building_size":{
        "units":"sqft",
        "value":"110203.000000"
      },
      "created":"2015-01-12T02:40:18.409322",
      "lot_size":{
        "units":"sqft",
        "value":"3.360000"
      },
      "deleted":false,
      "building_type":"retail",
      "address":{
        "city":"Orlando",
        "zipcode":"32808",
        "longitude":-81.443158,
        "county":"orange",
        "state":"FL",
        "street":"4815 West Colonial Drive",
        "latitude":28.553185
      }
    },
    "type":"buildings",
    "id":"1295335036823799010"
  }
}
```

You will see that the `space` entity now has a `relationships` key - this is because our nested include causes more relationship data to be pulled in in order to know what entity has the  Also note that when we specify a nested include, all the entity types we specified "on the way" to the last entity type will be included as well. In this case, that is the `space` specified in `spaces.leases` and `spaces.subleases`. For another example, if you queried on an organization entity and included `cards.contacts.leases.spaces.buildings`, you would get not just the `buildings` entities, but also the `cards`, `contacts`, `leases`, and `spaces` entities in-between.

## Filtering

In order to find specific entities you may be looking for according to the criteria on the entities, you can use the API's filtering features. You can filter on any attribute of the entity you're searching to find entities that match that exactly by using `filter[where][<attribute>]=<value>`. So for example, if you were trying to find a building with the title "Frost Bank Tower", you would do:

`GET https://api.realmassive.com/buildings?filter[where][title]=Frost%20Bank%20Tower`

This returns the entity representing the Frost Bank Tower:

```json
{
  "meta":{
    "count":1
  },
  "data":[
    {
      "relationships":{
        "spaces":{
          "links":{
            "related":"/buildings/1295335052099454372/spaces"
          }
        },
        "attachments":{
          "links":{
            "related":"/buildings/1295335052099454372/attachments"
          }
        }
      },
      "attributes":{
        "build_status":"existing",
        "updated":"2016-10-14T16:10:32.425800",
        "year_built":2003,
        "title":"Frost Bank Tower",
        "deleted":false,
        "leed_rating":"gold",
        "opex":{
          "units":"usd"
        },
        "building_class":"a",
        "building_type":"office",
        "created":"2016-07-16T02:26:33.012608",
        "lot_size":{
          "units":"sf"
        },
        "tenancy":"multi_tenant",
        "air_conditioned":true,
        "building_size":{
          "units":"sf",
          "value":"535078.000000"
        },
        "address":{
          "city":"Austin",
          "zipcode":"78701",
          "longitude":-97.7426923,
          "county":"Travis County",
          "state":"TX",
          "street":"401 Congress Avenue",
          "latitude":30.2664665
        },
        "description":"Frost Bank Tower is in the business and geographic center of downtown Austin. This iconic building built in a modern art deco design cuts a distinctive silhouette on the Austin skyline. Frost Bank Tower features floor plates from 18,000 to 27,000 SF with virtually column-free design.\n\nLocated on Congress Avenue, the building is surrounded by fine and casual dining options, hotels, museums and art galleries. Just a few blocks away from the Capitol Complex to the north and Lady Bird Lake to the south. Frost Bank Office Tower offers direct access to MOPAC (Loop 1) and IH-35.\n\nAmenities\n\u2022 Building concierge\n\u2022 Fitness club, showers, personal training\n\u2022 Conference facility\n\u2022 Multiple restaurants on-site\n\u2022 Houndstooth gourmet coffee shop\n\u2022 Full service banking facilities/ATM\n\u2022 Dry cleaning services on-site\n\u2022 Overnight courier drop boxes\n\u2022 2.7/1000 structured parking ratio\nhttp://frostbanktoweraustin.com/"
      },
      "type":"buildings",
      "id":"1295335052099454372"
    }
  ]
}
```

You can also filter using equality operators with the form `filter[where][<attribute>][<operator>]=<value>`. The list of possible operators is:

- `eq` (this is implied if you omit the operator, so you don't need to use this one explicitly)
- `gt` - greater than
- `lt` - less than
- `gte` - greater than or equal
- `lte` - less than or equal

So if you were trying to find buildings built after the year 2000, you could do:

`GET https://api.realmassive.com/buildings?filter[where][year_built][ge]=2000`

You can get only entities that have a specific string attribute present by asserting that its value is greater than 0: `GET https://api.realmassive.com/buildings?filter[where][title][ge]=0`

You can get only entities that have a specific related entity in a similar way, by asserting that the value of the related entity's id is greater than 0. This will return only buildings that have sale entities related: `GET https://api.realmassive.com/buildings?filter[where][sales.id][ge]=0`

You might think from the previous example that you can filter on the values of related entities. You would be correct! This query will return only buildings that have a sale that is not archived: `GET https://api.realmassive.com/buildings?filter[where][sales.id][ge]=0&filter[where][sales.archived]=false`

You can also apply a filter to nested includes. This query will return all buildings which have a space which has a sublease entity: `GET https://api.realmassive.com/buildings?filter[where][spaces.subleases.id][ge]=0`

Note, however, that applying a filter to an included item will not filter out the included entities. It only applies as a filter to the primary entity (which are, in the previous examples, buildings). So if you made this query with the intention of getting a building and only its subleases:

`GET https://api.realmassive.com/buildings?filter[where][spaces.subleases.id][ge]=0&include=spaces.subleases`

...you might be surprised to find that it also includes spaces that are related to leases, not subleases. This happens because the filter and include params are not inclusive of one another; first, the filter is applied (to the buildings), and then the include pulls in ALL of the spaces and subleases for the building, including the spaces related to leases. You as the client must determine which included spaces are related to the present subleases, but this is fairly trivial because the `space` entities you want will have a `relationships.subleases` key which you can look for, and exclude all other spaces.
