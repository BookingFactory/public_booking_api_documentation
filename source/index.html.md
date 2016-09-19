---
title: Public Booking API Reference

language_tabs:
  - javascript

toc_footers:
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

Welcome to the Booking Factory Public Booking API! You can use our API to write your own booking engine.

Our API based at simple HTTP requests, as result you can use any language! You can view code examples in the dark area to the right, we provide examples at JavaScript, but you can use CURL or other libraries.

This example API documentation page was created with [Slate](https://github.com/tripit/slate). Feel free to edit it and use it as a base for your own API's documentation.

# API Endpoint
```javascript
const API_ENDPOINT = 'https://app.thebookingfactory.com/api/public/v1/';
```

Our API endpoint located at https://app.thebookingfactory.com/api/public/v1/

# Authentication
```javascript
const API_KEY = 'meowmeowmeow';
```

> Make sure to replace `meowmeowmeow` with your API key.

Our engine uses API keys to allow access to the API. You can register a new API key at your [hotel settings page](https://app.thebookingfactory.com/client/#/settings/other/api_key).

We expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Token: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Methods

## Get Available Dates

```javascript
fetch(
    API_ENDPOINT + 'available_dates?date_from=2016-09-20',
    {
      method:  'GET',
      headers: { 'Token': API_KEY }
    }
  )
  .then(function(raw_response) {
    return raw_response.json();
  })
  .then(function(response) {
    console.log(response);
  })
```

> The above command returns JSON structured like this:

```json
{
  "success": true,
  "date_from": "2016-09-20",
  "date_to": "2017-09-20",
  "dates": [
    "2016-09-20",
    "2016-09-21",
    "2016-09-22",
    ...
    "2017-09-20"
  ]
}
```

This endpoint retrieves list of all available to book dates. Date is available to book if we have at least one room available to book at this date.

Using this API you can get available dates for one year from current date, for one year from date_from or for dates between date_from and date_to.

### HTTP Request

`GET http://app.thebookingfactory.com/api/public/v1/available_dates`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
date_from | false | Date from, should be a valid date in ISO 8601 YYYY-MM-DD format. If values is not present, API use current date as date_from
date_to | false | Date to, should be a valid date in ISO 8601 YYYY-MM-DD format. If values is not present, API calculate date_to as date_from + 1 year

### Response
Parameter | Description
--------- | -----------
success | Show current request status, if all is ok then TRUE, if request has any errors then FALSE
errors | Array of error descriptions. Displayed only if success == false
date_from | Date in ISO 8601 YYYY-MM-DD format
date_to | Date in ISO 8601 YYYY-MM-DD format
dates | Array of available dates in ISO 8601 YYYY-MM-DD format


## Get Available Rooms

```javascript
fetch(
    API_ENDPOINT + 'available_rooms?date_from=2016-09-20&date_to=2016-09-22&currency=RUB',
    {
      method:  'GET',
      headers: { 'Token': API_KEY }
    }
  )
  .then(function(raw_response) {
    return raw_response.json();
  })
  .then(function(response) {
    console.log(response);
  })
```

> The above command returns JSON structured like this:

```json
{
  "success": "true",
  "currency": "RUB",
  "rooms": [
    {
      "short_code": "exdb",
      "name": "Executive Double Room",
      "short_name": "Executive Double",
      "description": "Small description...",
      "max_adults": 2,
      "max_children": 0,
      "max_infants": 0,
      "max_occupancy": 2,
      "priority": 0,
      "photos": [
        "photo_url1", "photo_url2", "photo_url3"
      ],
      "facilities": [
        { "title": "Transfer", "icon": "bus" },
        { "title": "Dog Friendly", "icon": "paw" }
      ],
      "count_of_rooms": 7,
      "better_price": "8780.40",
      "better_price_offered": true
    },
    ...
    {
      "short_code": "trfr",
      "name": "Twin Room / Family Room",
      "short_name": "Twin Room",
      "description": "Small description for room type",
      "max_adults": 2,
      "max_children": 2,
      "max_infants": 0,
      "max_occupancy": 4,
      "priority": 3,
      "photos": [
        "photo_url1", "photo_url2", "photo_url3"
      ],
      "facilities": [],
      "count_of_rooms": 4,
      "better_price": "878.04",
      "better_price_offered": true
    }
  ]
}
```

This method return all available to book room types in selected date range.
date_from and date_to arguments is required.

### HTTP Request

`GET http://app.thebookingfactory.com/api/public/v1/available_rooms`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
date_from | true | Date from, should be a valid date in ISO 8601 YYYY-MM-DD format
date_to | true | Date to, should be a valid date in ISO 8601 YYYY-MM-DD format
currency | false | Currency code to convert price from hotel currency to user currency based at Exchange rate. If argument is not present, prices returned at hotel base currency.

<aside class="info">
Currency. This is optional field represented at many our methods. If this argument is present we convert all money fields to selected currency. We support only 3 symbols based currency codes.
</aside>

### Response
Parameter | Description
--------- | -----------
success | Show current request status, if all is ok then TRUE, if request has any errors then FALSE
errors | Array of error descriptions. Displayed only if success == false
currency | Currency ISO 4217 alpha-code used to display money fields
rooms | Array of available to book room types

#### Room Type object description
Parameter | Description
--------- | -----------
short_code | Unique code for room type, we use this field as Room Type ID at public requests
name | Name of Room Type
short_name | Short name of Room Type
description | Description of Room Type
max_adults | Max count of adult guests
max_children | Max count of children
max_infants | Max count of infants
max_occupancy | Max occupancy
priority | Visible priority to order Room Types
photos | Array of Room Type photo URLs
facilities | Array of Room Type facilities. Every facility represented as object with title and icon, where icon is FontAwesome icon key
count_of_rooms | Count of available to book room at current Room Type
better_price | Better available price
better_price_offered | True if better price provided with discount, false if better price is base rate

## Get Available Offers

```javascript
fetch(
    API_ENDPOINT + 'available_offers?date_from=2016-09-20&date_to=2016-09-22&room_type=exdb',
    {
      method:  'GET',
      headers: { 'Token': API_KEY }
    }
  )
  .then(function(raw_response) {
    return raw_response.json();
  })
  .then(function(response) {
    console.log(response);
  })
```

> The above command returns JSON structured like this:

```json
{
  "success": "true",
  "currency": "GBP",
  "offers": [
    {
      "title": "Base Rate",
      "rate_category": 3,
      "special_offer": null,
      "total_price": "22.00",
      "cancellation_policy": "Cancellation policy description"
    },
    {
      "title": "15% Discount",
      "rate_category": 3,
      "special_offer": 1,
      "total_price": "18.70",
      "cancellation_policy": "Cancellation policy description"
    }
  ]
}
```

This endpoint retrieves list of all available to book offers.
date_from, date_to and room_type is required fields.

### HTTP Request

`GET http://app.thebookingfactory.com/api/public/v1/available_dates`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
date_from | true | Date from, should be a valid date in ISO 8601 YYYY-MM-DD format
date_to | true | Date to, should be a valid date in ISO 8601 YYYY-MM-DD format
room_type | true | Selected room short_code
currency | false | Currency code to convert price from hotel currency to user currency based at Exchange rate

### Response
Parameter | Description
--------- | -----------
success | Show current request status, if all is ok then TRUE, if request has any errors then FALSE
errors | Array of error descriptions. Displayed only if success == false
currency | Currency ISO 4217 alpha-code used to display money fields
offers | Array of available to book offers for selected Room Type

#### Offer object description
Parameter | Description
--------- | -----------
title | Offer title
rate_category | Rate Category ID
special_offer | Special Offer ID
total_price | Total price
cancellation_policy | Cancellation policy description


## Get Available Extras

```javascript
fetch(
    API_ENDPOINT + 'available_extras',
    {
      method:  'GET',
      headers: { 'Token': API_KEY }
    }
  )
  .then(function(raw_response) {
    return raw_response.json();
  })
  .then(function(response) {
    console.log(response);
  })
```

> The above command returns JSON structured like this:

```json
{
  "success": true,
  "currency": "GBP",
  "extras": [
    {
      "id": 1,
      "title": "Extra Title 1",
      "price": "2.00",
      "vat": "10.00"
    },
    {
      "id": 2,
      "title": "Extra Title 2",
      "price": "12.00",
      "vat": "20.00"
    }
  ]
}
```

This endpoint retrieves list of all available extras.

### HTTP Request

`GET http://app.thebookingfactory.com/api/public/v1/available_extras`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
currency | false | Currency code to convert price from hotel currency to user currency

### Response
Parameter | Description
--------- | -----------
success | Show current request status, if all is ok then TRUE, if request has any errors then FALSE
errors | Array of error descriptions. Displayed only if success == false
currency | Currency ISO 4217 alpha-code used to display money fields
extras | Array of available to book extras

#### Extras object description
Parameter | Description
--------- | -----------
id | Extra ID
title | Extra title
price | Extra price per item
vat | VAT percent for extra


## Check Member

```javascript
fetch(
    API_ENDPOINT + 'check_member?email=member@thebookingfactory.com',
    {
      method:  'GET',
      headers: { 'Token': API_KEY }
    }
  )
  .then(function(raw_response) {
    return raw_response.json();
  })
  .then(function(response) {
    console.log(response);
  })
```

> The above command returns JSON structured like this:

```json
{
  "success": "true",
  "member_is_present": true
}
```

This endpoint check member exists or not in our system.

If member is exists, system send email with authentication code for member. Member can use this code to auth at your booking engine.

### HTTP Request

`GET http://app.thebookingfactory.com/api/public/v1/check_member`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
email | true | Valid member email address

### Response
Parameter | Description
--------- | -----------
success | Show current request status, if all is ok then TRUE, if request has any errors then FALSE
errors | Array of error descriptions. Displayed only if success == false
member_is_present | True if member exists, False if not


## Get Member Details

```javascript
fetch(
    API_ENDPOINT + 'member_details?email=member@thebookingfactory.com&code=1234'
    {
      method:  'GET',
      headers: { 'Token': API_KEY }
    }
  )
  .then(function(raw_response) {
    return raw_response.json();
  })
  .then(function(response) {
    console.log(response);
  })
```

> The above command returns JSON structured like this:

```json
{
  "success": "true",
  "member": {
    "first_name": "FirstName",
    "last_name": "LastName",
    "email": "member@thebookingfactory.com",
    "phone": "+123321123321",
    "address": "Member address",
    "city": "City",
    "postal_code": "POSTAL CODE",
    "state": "State",
    "country": "Country",
    "last_4": "XXXX"
  }
}
```

This endpoint retrieves member details to fill booking form.

### HTTP Request

`GET http://app.thebookingfactory.com/api/public/v1/member_details`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
email | true | Valid member email address
code | true | 4 digest authentication code

### Response
Parameter | Description
--------- | -----------
success | Show current request status, if all is ok then TRUE, if request has any errors then FALSE
errors | Array of error descriptions. Displayed only if success == false
member | Member object

#### Member object descriptions
Parameter | Description
--------- | -----------
first_name | Members first name
last_name | Members last name
email | Members email
phone | Members phone
address | Members address
city | Members city
postal_code | Members postal code
state | Members state
country | Members country
last_4 | Members card last 4 digest


## Calculate Booking Totals

```javascript
fetch(
    API_ENDPOINT + 'calculate_totals',
    {
      method:  'POST',
      headers: {
        'Token': API_KEY,
        'Content-Type': 'application/json'
      },
      body: {
        'date_from' : '2016-09-22',
        'date_to' : '2016-09-23',
        'rooms' : [
          {
            'room_code' : 'exdb',
            'offer' : null,
            'rate_category' : 3
          }
        ],
        'extras' : [
          'id': 1,
          'count': 10
        ]
      }
    }
  )
  .then(function(raw_response) {
    return raw_response.json();
  })
  .then(function(response) {
    console.log(response);
  })
```

> The above command returns JSON structured like this:

```json
{
  "success": "true",
  "currency": "GBP",
  "total": {
    "total": "11.00",
    "subtotal": "3.65",
    "rooms_total": "11.00",
    "extras_total": "0.00",
    "taxes": {
      "included_taxes": [
        {
          "title": "VAT",
          "value": "20.0",
          "sum": "1.83"
        },
        {
          "title": "Luxury Tax",
          "value": "5.0",
          "sum": "0.52"
        },
        {
          "title": "City Tax",
          "value": "5.0 per room",
          "sum": "5.00"
        }
      ],
      "excluded_taxes": []
    }
  }
}
```

This endpoint calculate totals for provided booking object

### HTTP Request

`POST http://app.thebookingfactory.com/api/public/v1/calculate_totals`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
date_from | true | date_from should be a valid date in ISO 8601 YYYY-MM-DD format
date_to | true | date_to should be a valid date in ISO 8601 YYYY-MM-DD format
rooms | true | Array of Rooms object
extras | true | Array of Extras object

#### Room object
Parameter | Description
--------- | -----------
room_code | Unique room code
offer | ID of selected offer, null if user selected default rate category
rate_category | ID of selected rate category

NOTE: All of this data you can get from offers method

#### Extra object
Parameter | Description
--------- | -----------
extra_id | ID of selected Extra
count | Count of items for selected Extra

### Response
Parameter | Description
--------- | -----------
success | Show current request status, if all is ok then TRUE, if request has any errors then FALSE
errors | Array of error descriptions. Displayed only if success == false
totals | Member object

#### Totals object
Parameter | Description
--------- | -----------
total | Total booking sum
subtotal | Subtotal sum without taxes
rooms_total | Sum of total rooms cost
extras_total | Sum of total extras cost
taxes | Object with included and excluded taxes, every tax described as Title, Percent and Sum


## Create Booking

```javascript
fetch(
    API_ENDPOINT + 'create_booking',
    {
      method:  'POST',
      headers: {
        'Token': API_KEY,
        'Content-Type': 'application/json'
      },
      body: {
        'date_from' : '2016-09-22',
        'date_to' : '2016-09-23',
        'rooms' : [
          {
            'room_code' : 'exdb',
            'offer' : null,
            'rate_category' : 3
          }
        ],
        'extras' : [
          'id': 1,
          'count': 10
        ],
        'card': {
      		'token': 'valid_credit_card_token',
      		'provider': 'braintree'
      	},
      	'customer': {
      		'email' : 'customer@test.com',
      		'first_name' : 'FirstName',
      		'last_name' : 'LastName',
      		'phone_number': '+12312332123',
      		'address': 'Address',
      		'country': 'GB',
      		'city': 'City name',
      		'postal_code': 'Postal Code'
      	}
      }
    }
  )
  .then(function(raw_response) {
    return raw_response.json();
  })
  .then(function(response) {
    console.log(response);
  })
```

> The above command returns JSON structured like this:

```json
{
  "success": true,
  "token": "AAA-000000-W"
}
```

This endpoint create new booking at TheBookingFactory system.

### HTTP Request

`POST http://app.thebookingfactory.com/api/public/v1/create_booking`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
date_from | true | date_from should be a valid date in ISO 8601 YYYY-MM-DD format
date_to | true | date_to should be a valid date in ISO 8601 YYYY-MM-DD format
rooms | true | Array of Rooms object
extras | true | Array of Extras object
card | true | Card object
customer | true | Customer object

#### Room object
Parameter | Description
--------- | -----------
room_code | Unique room code
offer | ID of selected offer, null if user selected default rate category
rate_category | ID of selected rate category

NOTE: All of this data you can get from offers method

#### Extra object
Parameter | Description
--------- | -----------
extra_id | ID of selected Extra
count | Count of items for selected Extra

#### Card object
Parameter | Description
--------- | -----------
token | Valid credit card token from tokenization service
provider | Tokenization service name

#### Customer object
Parameter | Description
--------- | -----------
email | Customer email
first_name | Customer first name
last_name | Customer last name
phone | Customer phone
address | Customer address
city | Customer city
postal_code | Customer postal code
state | Customer state
country | Customer country ISO 3166-1 alpha-2 code


### Response
Parameter | Description
--------- | -----------
success | Show current request status, if all is ok then TRUE, if request has any errors then FALSE
errors | Array of error descriptions. Displayed only if success == false
token | Booking token string
