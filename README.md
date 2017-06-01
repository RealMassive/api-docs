# api-docs

Central Repository for RealMassive API Documentation

# Getting Data Into RealMassive

There are four ways to get your organization's data onto the RealMassive platform. (*: getting_data_into_rm.md doc details go here)

## 1. You manage your inventory through our API

Using our REST API, create and update CRE entities on the RealMassive platform which reflect your data. Recommended for enterprise or companies with full IT support.

We have an open and fully documented REST API. It communicates over HTTPS using JSON.

- Customer Benefits:
    + Customer retains complete control: additions, updates, and removals are controlled by the customer
    + Ability to push complete database dumps or incremental changes as their needs require
    + Cheapest long-term solution
    + Highest data integrity

- Customer Responsibilities:
    + Dedicated resources to learn and implement our API
    + Push data to us in bulk or incrementally. This keeps our data in sync.

## 2. We manage your inventory through your API

We will automate the ingestion and translation of your API into data on the RealMassive platform. Recommended for small/medium sized business.

If you have a SOAP- or JSON-based API, we can learn and implement your API using in-house tools.

- Customer Benefits:
    + RealMassive does the heavy lifting of API integration.
    + Ability to push complete database dumps or incremental changes
    + Cheapest long-term solution
    + Highest data integrity

- Customer Responsibilities:
    + Customer has a usable, modern API with documentation. Cutsomer must give us access. Customer's IT must be robust enough to handle API calls.

## 3. We manage your inventory via RealMassive Rosetta

We will automate the ingestion and translation of your data via a predetermined format into data on the RealMassive platform. Recommended for small businesses or those with little IT support.

If you don't have an API, but can export your inventory into a specific format, we can use an internal tool, called Rosetta, to keep your data up to date.

- Customer Benefits:
    + Customer doesn't need to learn our API.
    + Customer can simply create a specific type of export.
    + Low-cost option (but more expensive than API).
    + Higest data integrity.

- Customer Responsibilities:
    + Customer must export their data into a specific format at a publicly accessible URL. That data must be kept up to date.

## 4. You send us pre-formatted CSV or Microsoft Excel Spreadsheets

We will parse and process your data in a "best effort" approximation of our model using internal tools and put the data on the RealMassive platform. Recommended for businesses with absolutely no IT support.

- Customer Benefits:
    + Listings on RealMassive are as up to date as you and we can manage.

- Customer Responsibilities:
    + Customer sends up updates in a consumable format.

## REST API Documentation

Our REST API complies with the JSON API specification, a specification that provides a convention for defining resources in a REST API with JSON. See the following links for information on JSON and JSON API:

- http://www.json.org/
- http://jsonapi.org/

### Getting Started Tutorial

Read our [Getting Started Tutorial](rest-api-getting-started.md) for a crash course into how to get started with our API. You'll learn about the fundamentals of entity types, creating a building with listings, and seeing it live on our site.

### Examples

- Getting an access token and using refresh tokens
- Basic CRUD operations: creation, reading, updating, deleting, and creating and deleting relationships between entities
- Creating a building with spaces and listings
- Creating land with parcels for sale
- Creating buildings and land for sale
- Setting up a contact on a listing
- Creating permissions for your team

### References

- Access and refresh tokens
- Understanding JSON API
- ERD Diagram
- RealMassive Schema
- RealMassive Schema field options
- Detailed generated API documentation
- Understanding Permissions
- Understanding Media

## Rosetta Documentation

- How CRESS works
- The CRESS (Commercial Real Estate Standard Schema) (*: or whatever) specification
- Exposing your data to RealMassive