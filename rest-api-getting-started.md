RealMassive REST API Programming Guide
--

Revision: Draft-1

Welcome!

This guide will show you how to programmatically create and manage inventory on RealMassive using the RealMassive API. The topics presented here are the low-level operations _currently_ required to manage inventory via API. These topics are:
 1. creating and relating entities; 
 1. permissions; 
 1. entity relationships; 
 1. and finally, examples.

Before reading this guide, please review the resources in our [Quickstart Guide](https://realmassive.github.io/realmassive.github.io/index.html). This guide assumes you have read and understand all content therein, including JSON API and authorization (tokens).


# Creating and Relating Entities

The core patttern for creating data on RealMassive through our REST API has two steps: _Create_ & _Relate_.

## 1. Create one or more Entities

Entities are objects like `Buildings`, `Spaces`, `Leases`, `Subleases`, `Contacts`, `Organizations`, etc. These represent real world concepts from commercial real estate. 
* You create entities by sending a `POST` request with an appropriate JSON payload.
* Responses to your successful requests will contain a new, globally unique, identifier (`id`) for the newly created entity. Store that id somewhere, you will need it for later steps.
* Each entity's full structure can be found in our [API documentation](https://app.swaggerhub.com/apis/inchoate/real-massive_api/2.0). The read-only fields **not editable** by the user are: `created`, `id`, and `updated`. Those can be safely ignored when creating entities.

Let's take a look at some examples. We will create an `Organization` and a `Building` by sending a POST request to the server for each.

### Example 1. Create an Organization

In order to create an `Organization` you should first figure out org data are required. See our [API documentation](https://app.swaggerhub.com/apis/inchoate/real-massive_api/2.0) for the full list. We suggest you provide at least a name. The more data you provide, the better you will be represented in the open CRE marketplace. You can create an organization using a `POST` request formatted as in the following example.

#### Request
```shell
curl -X POST \
  https://api-sandbox.realmassive.com/organizations \
  -H 'authorization: Bearer eyJhbG..byAw5QC4JM' \
  -H 'content-type: application/json' \
  -d '{
  "data": {
    "attributes": {
      "address": {
        "city": "Austin",
        "county": "Travis",
        "full_state": "Texas",
        "state": "TX",
        "street": "1145 W 5th",
        "zipcode": "78703"
      },
      "bio": "RealMassive is an Austin-based tech startup disrupting a $15 trillion industry by connecting every person and place in a modern, global commercial real estate (CRE) marketplace. We leverage big data to provide CRE pros with intuitive property list and search tools, digital marketing capabilities, and real-time performance analytics. Our digital marketplace is the nexus between tenants, investors, owners, properties, and brokers. We'\''re in fast-growth mode and seeking the most talented and ambitious candidates to help us scale our business quickly.",
      "email": "contact@realmassive.com",
      "name": "RealMassive",
      "phone": "(682) 587-9750",
      "social": {
        "linkedin": "https://www.linkedin.com/company/realmassive",
        "twitter": "https://www.twitter.com/realmassive",
        "website": "https://www.realmassive.com"
      }
    },
    "type": "organizations"
  }
}
'
```

#### Response
```python
{
  "data": {
    "attributes": {
      "bio": "RealMassive is an Austin-based tech startup disrupting a $15 trillion industry by connecting every person and place in a modern, global commercial real estate (CRE) marketplace. We leverage big data to provide CRE pros with intuitive property list and search tools, digital marketing capabilities, and real-time performance analytics. Our digital marketplace is the nexus between tenants, investors, owners, properties, and brokers. We're in fast-growth mode and seeking the most talented and ambitious candidates to help us scale our business quickly.",
      "updated": "2017-05-26T22:00:25.890105+00:00",
      "name": "RealMassive",
      "created": "2017-05-26T22:00:25.890105+00:00",
      "social": {
        "website": "https://www.realmassive.com",
        "twitter": "https://www.twitter.com/realmassive",
        "linkedin": "https://www.linkedin.com/company/realmassive"
      },
      "phone": "(682) 587-9750",
      "address": {
        "city": "Austin",
        "zipcode": "78703",
        "county": "Travis",
        "state": "TX",
        "street": "1145 W 5th",
        "full_state": "Texas"
      },
      "email": "contact@realmassive.com"
    },
    "type": "organizations",
    "id": "1523505465516885282"
  }
}
```

Save the `id` and `type` returned from the server. If managing inventory you will need that info for tying listings and brokers to your organization.


### Example 2. Create a Building

Just like the above, in order to create a `Building` you should first figure out org data are required. See our [API documentation](https://app.swaggerhub.com/apis/inchoate/real-massive_api/2.0) for the full list. We suggest you provide at least as much data as possible. Buildings are extremely important and deserve the best data. You can create a building by `POST`ing a request formatted as in the following example.

#### Request

```shell
curl -X POST \
  https://api-staging.realmassive.com/buildings \
  -H 'authorization: Bearer eyJhbG..byAw5QC4JM' \
  -H 'content-type: application/json' \
  -d '{
  "data": {
    "attributes": {
      "address": {
        "city": "Austin",
        "county": "Travis",
        "full_state": "Texas",
        "latitude": 30.2714351,
        "longitude": -97.7602741,
        "state": "TX",
        "street": "1145 W 5th St",
        "zipcode": "78703"
      },
      "air_conditioned": true,
      "build_status": "existing",
      "building_class": "C",
      "building_size": {
        "units": "SQFT",
        "value": 15000
      },
      "building_type": "office",
      "description": "string",
      "floor_count": 3,
      "signage": "Lighted sign on building.",
      "tenancy": "multi",
      "title": "RealMassive Office",
      "year_built": 1970
    },
    "type": "buildings"
  }
}'
```

#### Response

```shell
{
  "data": {
    "attributes": {
      "build_status": "existing",
      "updated": "2017-05-26T22:06:49.349060+00:00",
      "year_built": 1970,
      "floor_count": 3,
      "title": "RealMassive Office",
      "building_size": {
        "units": "SQFT",
        "value": "15000.000000"
      },
      "building_class": "C",
      "created": "2017-05-26T22:06:49.349060+00:00",
      "building_type": "office",
      "tenancy": "multi",
      "signage": "Lighted sign on building.",
      "address": {
        "city": "Austin",
        "zipcode": "78703",
        "longitude": -97.7602741,
        "county": "Travis",
        "state": "TX",
        "street": "1145 W 5th St",
        "latitude": 30.2714351,
        "full_state": "Texas"
      },
      "air_conditioned": true,
      "description": "string"
    },
    "type": "buildings",
    "id": "1523508682204120356"
  }
}
```

IDs that look like, `1523508682204120356` are globally unique in a RealMassive environment (like sandbox or production). Once you create your entities, you should save its type and id for later use.

If you're wondering how I knew what fields to send see our [API documentation](https://swaggerhub.com/apis/inchoate/real-massive_api/2.0), which enumerates the details.

In this step you created data using the API, but it's _free floating data_, not tied to any organization and won't be found in searches or listings. In the next step you will learn how to connect entities together creating usable data on our platform.

## 2. Relate the Entities

Assume you created a `Building` and `Space` using what you learned above. You now have a `Building` and a `Space`, but they don't know about each other. To relate them togther or (add the space to the building) you issue one more post commands:
  `POST /buildings/{building_id}/spaces`
passing to that route the payload with the space id. This relates the space to the specified building.

Let's consider another example, where we want to create a `Contact` for a `Lease.` You would do the following:
1. `POST /contacts` to create the new contact. Store the ID returned. The contact is stored in the databse:
<img src="images/2-1-contacts.png" height="45px" />

2. `POST /leases` to create the new lease. Store the ID returned. The lease is added to the database, but is still unrelated to the contact:
<img src="images/2-3-contacts-leases-unrelated.png" height="125px" />

3. `POST /leases/{leaseID}/contacts` to add that contact to that space. The lease and contact are now related; the "Lease has a contact":
<img src="images/2-3-contacts-leases-related.png" height="125px" />

Now that you understand the basic pattern, the next thing to know is how permissions and data are handled. 

# Data Hierarchy and Permissions

## Nomenclature and Types

It's important to clarify the different types of entities on the RealMassive platform. This is not a comprehensive list but should be enough information to give you a sense of how to work with the API.

- **Permissions-handling entities** - these entity types are used to manage access
on the RealMassive platform.
    - **Users** - Users represent a RealMassive user; this is what you use to log in.
    - **Teams** - Teams are used to give permissions to various users. Users are part of one or more teams.
    - **Permissions** - Permissions are actual objects that are related to a team and some other entity and have a permission level of read, write, or admin. Any user that belongs to a team uses the permissions objects related to that team to determine what the user has access to.
    - **Memberships** - Memberships are used to tie users to various teams. You won't have to worry about this one too much unless you are managing a users' teams.
- **Data entities** - these entity types represent actual data on the RealMassive
platform, and are what you will be interacting with
    - **Organizations** - Organizations are the *real world data representation* of any organization such as a company or firm in real life. Organizations entities can be associated with RealMassive users and teams, but there can also exist Organization entities that are represented as real world companies that are not on the RealMassive platform.
    - **Buildings** - Building entities represent real world buildings and can be related to Spaces.
    - **Spaces** - Space entities represent physical spaces in buildings that may be listed as for lease or sublease. For instance, a real-world building containing 30 office spaces will be represented as a Building entity with 30 Space entities related to it.
    - **Leases and Subleases** - Collectively referred to as "listings", Lease and Sublease entities represent the details of a space within a building being listed, such as rate, price, and availability date. Leases and Subleases are separate from Spaces because one Space could multiple Leases or Subleases available over different time periods (or perhaps concurrently!).

### Detailed Example: Creating your first listing

We'll show some demo requests and briefly explain what is happening. See the [RealMassive API documentation](http://docs.realmassiveapiv20.apiary.io/) for details on how to interact with each endpoint. NOTE: the API documentation is fairly verbose and descriptive, exhaustively detailing every endpoint, but almost every entity endpoint (save for Users, Teams, and Permissions) functions the same, following most parts of the [JSON API specification](http://jsonapi.org/).

The steps are roughly as follows:

1. [Create your organization](#create-your-organization)
2. [Create a building](#create-a-building)
3. [Create a space](#create-a-space)
4. [Relate the space to the building](#relate-the-space-to-the-building)
5. [Create a listing](#create-a-listing) (and relate it to space and organization at the same time!)
6. [View the fruits of your labor](#view-the-fruits-of-your-labor)

#### Create your Organization

We want to create our own Organization so that we can assign listings to it. To do this, we send an HTTP POST request to the `/organizations` endpoint containing a JSON payload:

```
POST /organizations HTTP/1.1
Host: api.realmassive.com
Authorization: Bearer <Token>
Content-Type: application/json
Cache-Control: no-cache

{
    "data": {
        "type": "organizations",
        "attributes": {
            "name": "Way's Demo Org"
        }
    }
}
```

POST requests must be accompanied with two headers: `Authorization: Bearer <your_auth_token>` and `Content-Type: application/json`. The body should be raw JSON.

Here's what the whole thing looks like as a CURL request:

```
curl -X POST -H "Authorization: Bearer <Token>" -H "Content-Type: application/json" -H "Cache-Control: no-cache" -d '{
    "data": {
        "type": "organizations",
        "attributes": {
            "name": "Way'"'"'s Demo Org"
        }
    }
}' "https://api.realmassive.com/organizations"
```

After a successful creation, we receive the following response:

```
{
  "data": {
    "attributes": {
      "created": "2016-12-01T18:40:33.524153+00:00",
      "name": "Way's Demo Org",
      "updated": "2016-12-01T18:40:33.524153+00:00"
    },
    "id": "1395872364452906568",
    "type": "organizations"
  }
}
```

Hold onto the ID for later use.

Current State of the Database:

<img src="images/3-1-org.png" height="65px" />

#### Create a building

Similar to creating an organization, we POST to `/buildings` with a JSON payload.

```
POST /buildings HTTP/1.1
Host: api-sandbox.realmassive.com
Authorization: Bearer <Token>
Content-Type: application/json
Cache-Control: no-cache

{
    "data": {
        "type": "buildings",
        "attributes": {
            "address": {
                "latitude": 30.21204,
                "longitude": -97.73125
            },
            "title": "My Building",
            "building_class": "A",
            "building_size": {
                "value": 32000,
                "units": "sqft"
            }
        }
    }
}
```

As a cURL request:

```
curl -X POST -H "Authorization: Bearer <Token>" -H "Content-Type: application/json" -H "Cache-Control: no-cache" -d '{
    "data": {
        "type": "buildings",
        "attributes": {
            "address": {
                "latitude": 30.21204,
                "longitude": -97.73125
            },
            "title": "My Building",
            "building_class": "A",
            "building_size": {
                "value": 32000,
                "units": "sqft"
            }
        }
    }
}' "https://api-sandbox.realmassive.com/buildings"
```

We receive a response indicating that our request worked:

```
{
  "data": {
    "attributes": {
      "address": {
        "latitude": 30.21204,
        "longitude": -97.73125
      },
      "building_class": "A",
      "building_size": {
        "units": "sqft",
        "value": "32000.000000"
      },
      "created": "2016-12-01T20:11:08.782713+00:00",
      "title": "My Building",
      "updated": "2016-12-01T20:11:08.782713+00:00"
    },
    "id": "1395889931850614350",
    "type": "buildings"
  }
}
```

In order for buildings to show up on RealMassive's search engine, it must have a latitude and longitude. At this time, address fields inputted will not automatically be geocoded.

[**You can see the total list of attributes for various schemas in this gist.**](https://gist.github.com/wayspurrchen/a272683dbb75f534dab4115f5616426f)

Current State of the Database:

<img src="images/3-2-org-building.png" height="105px" />


#### Creating a Space

Once that's complete, you can create a Space that will be related to the building (we will do the relating in a different step).

We POST to `/spaces` with a payload:

```
POST /spaces HTTP/1.1
Host: api-sandbox.realmassive.com
Authorization: Bearer <Token>
Content-Type: application/json
Cache-Control: no-cache

{
    "data": {
        "type": "spaces",
        "attributes": {
            "floor_number": "1",
            "unit_number": "1A",
            "space_size": {
                "value": 1000,
                "units": "sqft"
            }
        }
    }
}
```

As a CURL:

```
curl -X POST -H "Authorization: Bearer <Token>" -H "Content-Type: application/json" -H "Cache-Control: no-cache" -d '{
    "data": {
        "type": "spaces",
        "attributes": {
            "floor_number": "1",
            "unit_number": "1A",
            "space_size": {
                "value": 1000,
                "units": "sqft"
            }
        }
    }
}' "https://api-sandbox.realmassive.com/spaces"
```

You'll notice that all of these requests are practically identical in syntax. Once you get used to the syntax, it becomes intuitive to interact with almost all of the endpoints, and very easy to build programmatic solutions against.

Response from the server:

```
{
  "data": {
    "attributes": {
      "created": "2016-12-01T20:17:36.484374+00:00",
      "floor_number": "1",
      "space_size": {
        "units": "sqft",
        "value": "1000.000000"
      },
      "unit_number": "1A",
      "updated": "2016-12-01T20:17:36.484374+00:00"
    },
    "id": "1395893184097158738",
    "type": "spaces"
  }
}
```

Current State of the Database:

<img src="images/3-3-org-building-space.png" height="105px" />

#### Relate the space to the building

Now that we've created our building and space, we need to relate the building to the space so that we can see the space when we view the building. To do so, we POST a small entity reference object to either `/buildings/<building_id>/spaces` or `/spaces/<spaces_id>/buildings` - it doesn't matter which. I'll use the former:

```
POST /buildings/1395889931850614350/spaces HTTP/1.1
Host: api-sandbox.realmassive.com
Authorization: Bearer <Token>
Content-Type: application/json
Cache-Control: no-cache

{
    "data": {
        "type": "spaces",
        "id": "1395893184097158738"
    }
}
```

As a CURL:

```
curl -X POST -H "Authorization: Bearer <Token>" -H "Content-Type: application/json" -H "Cache-Control: no-cache" -H -d '{
    "data": {
        "type": "spaces",
        "id": "1395893184097158738"
    }
}' "https://api-sandbox.realmassive.com/buildings/1395889931850614350/spaces"
```

For the response, we receive the data for whatever entity reference we POSTed, and can see that it has a `relationships` property now containing a reference to the building. In this case, we POSTed the `space` with id `1395893184097158738` to the building with id `1395889931850614350`, so we get back the space:

```
{
  "data": {
    "attributes": {
      "created": "2016-12-01T20:17:36.484374+00:00",
      "floor_number": "1",
      "space_size": {
        "units": "sqft",
        "value": "1000.000000"
      },
      "unit_number": "1A",
      "updated": "2016-12-01T20:17:36.484374+00:00"
    },
    "id": "1395893184097158738",
    "relationships": {
      "buildings": {
        "links": {
          "related": "/spaces/1395893184097158738/buildings/1395889931850614350"
        }
      }
    },
    "type": "spaces"
  }
}
```

Current State of the Database:

<img src="images/3-4-org-building-space-related1.png" height="115px" />

#### Create a listing (and relate it to space and organization at the same time!)

It's actually possible to both create an entity and relate it to another entity in one request, so you don't have to make two separate requests as we did previously. In order to do this, you create a new entity as you normally would, but then add the names of the types of entity you want to relate with to the `attributes` field. In our case, we want to relate the listing to the organization we created previously, as well as the space we just created.

```
POST /leases HTTP/1.1
Host: api-sandbox.realmassive.com
Authorization: Bearer <Token>
Content-Type: application/json
Cache-Control: no-cache

{
    "data": {
        "type": "leases",
        "attributes": {
            "available_date": "2016-12-15T05:55:33.246496+00:00",
            "price": {
                "value": 12,
                "units": "usd"
            },
            "rate": {
                "frequency": "weekly",
                "type": "nnn",
                "units": "dollar_per_sf"
            },
            "spaces": {
                "data": {
                    "type": "spaces",
                    "id": "1395893184097158738"
                }
            },
            "organizations": {
                "data": [{
                    "type": "organizations",
                    "id": "1395872364452906568"
                }]
            }
        }
    }
}
```

```
curl -X POST -H "Authorization: Bearer <Token>" -H "Content-Type: application/json" -H "Cache-Control: no-cache" -d '{
    "data": {
        "type": "leases",
        "attributes": {
            "available_date": "2016-12-15T05:55:33.246496+00:00",
            "price": {
                "value": 12,
                "units": "usd"
            },
            "rate": {
                "frequency": "weekly",
                "type": "nnn",
                "units": "dollar_per_sf"
            },
            "spaces": {
                "data": {
                    "type": "spaces",
                    "id": "1395893184097158738"
                }
            },
            "organizations": {
                "data": [{
                    "type": "organizations",
                    "id": "1395872364452906568"
                }]
            }
        }
    }
}' "https://api-sandbox.realmassive.com/leases"
```

You'll see that the response shows a lease that has been created with our attributes, and has a `relationships` key:

```
{
  "data": {
    "attributes": {
      "archived": false,
      "available_date": "2016-12-15T05:55:33+00:00",
      "created": "2016-12-01T21:08:21.049025+00:00",
      "price": {
        "units": "usd",
        "value": "12.000000"
      },
      "rate": {
        "frequency": "weekly",
        "type": "nnn",
        "units": "dollar_per_sf"
      },
      "updated": "2016-12-01T21:08:21.049025+00:00"
    },
    "id": "1395918723751085655",
    "relationships": {
      "organizations": {
        "links": {
          "related": "/leases/1395918723751085655/organizations"
        }
      },
      "spaces": {
        "links": {
          "related": "/leases/1395918723751085655/spaces/1395893184097158738"
        }
      }
    },
    "type": "leases"
  }
}
```

Current State of the Database:

<img src="images/3-4-org-building-space-related2.png" height="165px" />

#### View the fruits of your labor

Once you've done all of these steps, if you specified a proper latitude and longitude on your building, you should now be able to find your building on the RealMassive platform. You can jump straight to the building's page on the front-end by visiting the URL `https://sandbox.realmassive.com/buildings/<building_id>`.


### Entity Relationship Diagrams

The following diagrams illustrate how our entities fit together to model the domain of commercial real estate.


#### Users
<img src="images/erd-users0.png" height="65px" />

A `User` is someone who will login to RealMassive or use an API to manage data. `tos_status` reflects whether or not this user has accepted our terms of service. If you haven't accepted out terms, you cannot use our system. A `User` is little more than an email address and unique ID.

##### Users, Teams and Permissions

<img src="images/erd-users1.png" height="95px" />

Users can be have `Memberships` in zero or more `Teams`. This is how permissions are handled. You add `Users` to teams. Then, you add other stuff to teams with a given permission of: `read` or `admin`. If a user is part of a team that has `admin` privileges, for example, on a building, then any user in that team can edit that building. This is similar to the GitHub permissions model.

##### Users and Cards

<img src="images/erd-users2.png" height="205px" />

Finally, users can be related to zero or more `Cards`. Discussed in detail below, `Cards` are like business cards for your brokers/contacts. `Cards` allow brokers/contacts to have listings on RealMassive without having a full `User` account. Brokers/contacts can have many cards, tailored to their desired use case.


#### Organizations

<img src="images/erd-orgs0.png" height="225px" />

`Organizations` represent real world entities that partake in the business of commercial real estate. Organizations uniquely identify the listing agency or other CRE company, its brokers/contacts, and all its on- and off-market listings. A listing is a `Lease`, `Sublease`, or `Sale`. 

<img src="images/erd-orgs1.png" height="385px" />


#### Teams

<img src="images/erd-users1.png" height="125px" />

As previously mentioned, `Teams` are used to control permissions. `Users` are added to a team through `Memberships`. Other entities are added to the team through `Permissions`. Users can control various assets through `Permissions` as shown below:

<img src="images/erd-teams1.png" height="505px" />

#### Buildings
Buildings have quite a few data fields. We suggest you provide as much data as possible.

##### Selling a Building
Buildings can be for `Sale`. You can attach zero or more `Sales` _directly_ to a `Building` to list it for sale.

<img src="images/erd-buildings0.png" height="505px" />


##### Leasing or Subleasing Space within a Building

Buildings aren't leased or subleased. The configured `Space` within a `Building` is `Leased` or `Subleased`. You can have as many `Spaces` inside a `Building` as you desire. You attach `Leases` and `Subleases` to `Space` to lease or sublease that space. You can have as many leases or subleases as desired on one space. A `Space` can only be related to one `Building`, though.

<img src="images/erd-buildings1.png" height="505px" />


#### Spaces
<img src="images/erd-spaces0.png" height="255px" />


#### Land
##### Selling Land
You can sell `Land` directly, just like you can sell a `Building` directly. Create the land object and attach the sale object directly to it.

<img src="images/erd-land0.png" height="225px" />

##### Selling Parcels of Land
If you need to divvy your land up into `Parcels` you may do so. Simply create the desired number of Parcels and attach the appropriate `Sales` objects to them.

The image below, shows that land can be directly sold or you can create parcels on the land and sell those.

<img src="images/erd-land1.png" height="385px" />



#### Parcels
See [Land](/Land) to information on selling `Parcels` of `Land`.

#### Listings: Lease, sublease, sales
`Sales`, `Leases`, and `Subleases` are known simply as Listings. There is no direct Listing object, but it's a useful, collective term. 

All Listings belong to an organization:

<img src="images/realmassive_listings1.png" height="585px" />


All Listings have one or more Contacts:

<img src="images/realmassive_listings2.png" height="585px" />

So, combining the information above on Leasing, and Subleasing space; and selling Land or Parcels; and Contacts and Organizations, this part of the model starts to unfold in full:

<img src="images/realmassive_listings0.png" height="385px" />


#### Cards and Contacts
Cards represent business cards for your brokers or contacts. You do not need a `User` to list space on RealMassive; you just need a `Card`. This is useful to allow marketing managers to add brokers to listings without having to get that broker to create a new account on RealMassive.

You attach a `Card` to a Listing by creating a `Contact` relating the `Card` to the `Listing`. Recall that `Listing` is a placeholder term for a `Lease`, `Sublease` or `Sale`:

<img src="images/erd_contact_cards0.png" height="425px" />

Notice that `Contacts` -- the relationship between a `Card` and a `Listing` has a `precedence`. This allows you to order brokers/contacts on listings:

<img src="images/erd_contact_cards1.png" height="85px" />


#### Media and Attachments
`Media` are images and brochures like JPGs, GIFs, PNGs, and PDFs. To add images to a `Listing` you upload the `Media` through our Media service:

<img src="images/erd-media0.png" height="195px" />

Then, you create an attachment to tie that particular piece of media back to your `Card`, `Building`, `Space`, `Parcel`, `Land`, etc:

<img src="images/erd-media1.png" height="195px" />

`Attachments`, like `Memberships` have precedence so you can order your media on an entity as you see fit.


