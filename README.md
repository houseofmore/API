![Checkfront API](https://www.checkfront.com/images/brand/Checkfront-Logo-45.png)

# Checkfront API

*version 2.1*

---

The Checkfront API allows developers to expand and build on the [Checkfront Booking Platform](http://www.checkfront.com). The API makes it easy to create web, desktop & mobile applications that integrate and with your Checkfront account.

Our API is built around open, standard and secure technologies to streamline development and maintain integrity of your data.

The Checkfront API extends the following functionality:

* Query inventory availability.
* Get date based rate and pricing information.
* Create a new booking.
* Update an existing booking.
* Retrieve customer information.

### Prerequisites:

This document assumes you have some knowledge of programming, and API implementations -- if not, please see our list of existing plug-ins and extensions or contact us to for in introduction to a qualified Checkfront developer in your area.

Some functionality documented here may not be available to you based on your plan, or access level of your account.  

## REST Interface

The Checkfront API uses a REST interface. This means that method calls are made over the Internet by sending standard HTTPs GET or POST requests to the Checkfront API REST server.

Nearly any computer language can be used to communicate over HTTP with the REST server including remote web sites, mobile devices and desktop applications.

## Authentication and Encryption (OAuth2)

Checkfront makes use of the open standard OAuth2 to provide secure and transparent authentication with the API. OAuth allows you to make your Checkfront account available to external applications without needing to provide your login and password.

All queries to the API are required to be made over a SSL authenticated OAuth2 session. Checkfront currently supports Draft 20 of the OAuth2 spec. For those switching from OAuth1, you'll notice it's significantly easier to work with, and generally performs better.

If your app isn't going to be distributed outside of your organization, you can generate oAuth2 access keys within the app to by-pass the initial authentication routine.

Please see our SDK's or support libraries for OAuth2 in your preferred environment.

### Endpoints

Every Checkfront account operates in their own segregated environment, secure by a unique domain. In most cases, this is : https://your-company.checkfront.com. In this document this will be referred to as your Checkfront URL or your-company.checkfront.com (this may differ depending on your country).

    https://your-company.checkfront.com/api/2.1/
	https://your-company.checkfront.com/oauth/

### Response Formatting (JSON)

All response is formatted in JSON (JavaScript Object Notation).All modern languages are able to quickly parse JSON. See: PHP: json_decode, Ruby: JSON.parse, .Net Json.NET, Javascript / jQuery: jQuery.getJSON.

### Representation

* All JSON should be UTF-8 encoded.
* Date and time values ISO 8601 formatted, eg: YYYY-MM-DD, HH:MM:SS.
* Dates are specified in the timezone configured in the account unless otherwise noted.
* Booleans are either 1 (true) or 0 (false).
* Currency values are decimal formatted, e.g: 119.20.

## General Housekeeping

API throttle limit: We reserve the right to tune the limitations, but they are always set high enough to allow a well-behaving interactive program to do its job.

When the rate limit is exceeded Checkfront will send an HTTP 503 status code. The number of seconds until the throttle is lifted is sent via the "Retry-After" HTTP header, as specified in RFC 2616.

## API Request

Once authenticated, a basic request to the API will return some general information about the account you are connecting to. This header information is included in all successful calls to the API, but may be left out of examples in this document.

	GET: http://demo.checkfront.com/api/2.1/

```json
{
    "version":"2.1",
    "id":"demo.checkfront.com",
    "name":"Demo",
    "currency_id":"USD",
    "currency_symbol":"$",
    "session_id":"rmvm4jf7kh02n6dde8s9f20me1"
}
```

Error Messages

```json
{
    "error":"invalid_request",
    "error_description":"The access token was not found."
}
```

### API Notifications

API notifications provide the ability to send an automated notification when a new booking is created or updated. You can configure API notifications in your account under Manage / API / Notifications.

### Sample Code

Our SDKs (software development kit) handles most of the complex authentication (OAuth2) issues involved with consuming the Checkfront API. They also include sample code and additional documentation and up and running with the API in no time.

All of our SDKs are open source and are [available on Github](https://github.com/Checkfront).  If you have a module to contribute, please let us know.

### Terms of Use

Use of this API is strictly bound by the terms as specified in [Checkfront API Terms of Service](http://www.checkfront.com/terms/#api).


---

# Checkfront API Objects

API Objects provide read and write access to Checkfront data sets. You can access Checkfront objects through standard the REST interface.

API Objects currently include: Inventory, Booking and Customer.

# Inventory

The inventory object provides access to your master inventory. This allows you to query items, determine availability and pricing for a given period. The information returned can be used to create a booking in the system.

##Categories

Items are organized by categories in the system.  A full list of available categories can be retrived via the category object. 

<table>
<tbody>
<tr>
<th>Description:</th>
<td>Fetch list of categories</td>
</tr>
<tr>
<th>Path:</th>
<td>/api/2/category/</td>
</tr>
<tr>
<th>Methods:</th>
<td>GET</td>
</tr>
<tr>
<th>[category_id]</th>
<td>Integer: optional category to return.</td>
</tr>
</tbody>
</table>


## Items

You can query a list of available items based on search critera supplied in the API call.

When no dates are passed in the API call, a full list of enabled items in the inventory.  When a date is passed, the API will return a "**rated**" response that includes pricing and availablity. 

<table>
<tbody>
<tr>
<th>Description:</th>
<td>Query items, optionally return pricing and availability.</td>
</tr>
<tr>
<th>Path:</th>
<td>/api/2.1/item/</td>
</tr>
<th>Path:</th>
<td>/api/2.1/item/<b>item_id</b> (single item)</td>
</tr>
<tr>
<th>Methods:</th>
<td>GET</td>
</tr>
<tr>
<th>[start_date]</th>
<td>Date: Start date.</td>
</tr>
<tr>
<th>[end_date]</th>
<td>Date: End date.</td>
</tr>
<tr>
<th>[start_time]</th>
<td>Time: Start time (used in hourly bookings).</td>
</tr>
<tr>
<th>[end_time]</th>
<td>Time: End time (used in hourly bookings).</td>
</tr>
<tr>
<th>[category_id]</td>
<td>Integer: Filter items by category.</td>
</tr>
<tr>
<th>[discount_code]</th>
<td>String: discount or voucher code to be used.</td>
</tr>
<tr>
<th>[rules]</th>
<td>String: supply 'soft' to not trigger date based rule errors.</td>
</tr>
<tr>
<th>[param]</td>
<td>Array: See - Booking parameters below.</td>
</tr>
</tbody>
</table> 

###Booking Paramaters

Booking parameters are defined globally in your system, and can also be configured per item.  Your parameters specify the number of items to book, for example Child and Adult.  These are completely configurable in your account under Manage / Settings / Configure.

To query specific pricing an availability you need to pass the appropriate parameters in your api call, using the ID's specified in your system configuration.

Let's say for instance you have 2 parameters configured.  Adults (id: adults), Children (id: children).  To get pricing for 2 a adults and one child you would pass: **param[adults]=2&param[children]=1** in your API call.

	GET /api/2.1/item/19start_date=20131230&end_date=20131230&end_date=20131230&param[adults]=2&param[children]=1

Booking parameters have many options, and can be configured to control inventory in very specific ways.  See the Checkfront support centre for more information.

### Item details

To get detailed pricing and availbility on a specific item, supply the item_id in the API call **path** along with any of the filter paramaters.  For example, to get item 19:

	GET /api/2.1/item/19/?start_date=20131230&end_date=20131230&end_date=20131230


## SLIP

The item SLIP is a string returned when making a rated query to a specific item.  The slip contains the information needed to make a booking.  Do not attempt to reverse engineer this as the format changes.  It must be optioned vi a rated API call.

--- 

# Booking

The booking object can be used in conduction with the item object to create a new booking on the system.

##Sessions

When creating a booking, the details of the booking are stored in an API session.  This allows you to add multiple items to a booking, remove items and also updates the server with your intent to book the item(s) to prevent over bookings.  

To start a new session, you must have the booking SLIP returned when querying item to be booked.

Description:|Set or update a booking session.

<table>
<tr><th>Path:</th>
<td>/api/2.1/booking/session</td>
</tr>
<tr><th>Methods:</th>
<td>GET, POST</td></tr>
<tr><th>[session_id]</th>
<td>String: system generated session id.</td></tr>
<tr><th>[slip]</th>
<td>String: system generated SLIP from rated Item query.</td></tr>
</tbody>
</table> 

### Create a new session
	POST /api/2.1/session?slip=3.20130303X1-guests.1


```json
{
"version": "2.1",
"host_id": "demo.checkfront.com",
"name": "My Company",
"request": {
"obj": "booking/session",
"status": "SUCCESS",
"results": 0
},
"booking": {
"session": {
"id": "rtdv4osethqurlmqgi55mcrkm4",
"slip": {
"1": "3.20130303X1-guests.1",
}
},
```

### Getting session details

You can fetch the details of the purposed booking by accessing the session object.  The item details will be returned with any request to the booking session.

 
	GET /api/2.1/session?slip=3.20130303X1-guests.1
	

If you wish to add multiple items at once, you can supply the SLIP in the form of an array.
 

	GET /api/2.1/session?slip=[]3.20130303X1-guests.1&slip[]=2.20130303X1-guests.2


### Add more items to a session

Depending on your platform or SDK, the session can be passed in the form of a cookie or in the query string.  For the sake of documenation we'll pass it in the query string.  
 
	GET /api/2.1/session?slip=3.20130303X1-guests.1&slip=rtdv4osethqurlmqgi55mcrkm4

## Creating A Booking

When the required items are added to your booking session, you can create the booking.  


### Booking Fields
New bookings require customer information, and other fields as defined by your system configuration.

You can dynamically fetch the fields required to complete the booking by calling the booking/form object along with the session, before calling booking/create.

<table>
<tr><th>Description:</th>
<td>Fetch Booking Fields.</td></tr>
<tr><th>Path:</th>
<td>/api/2.1/booking/form</td></tr>
</tbody>
</table>

This returns an array of fields and their properties that need to be used when creating the booking.

### Booking Create

To create a booking, submit the fields from the booking/form object along with the session id.  

<table>
<tr><th>Description:</th>
<td>Create a booking.</td></tr>
<tr><th>Path:</th>
<td>/api/2.1/booking/create</td></tr>
<tr><th>Methods:</th>
<td>POST</td></tr>
<tr>
<th>[session_id]</th><td>String: system generate session_id</td></tr>
</tr>
<tr><th>[fields]</th><td>Array: populated fields in a name value pair array from the booking/fields call.</td></tr>
</tr>
</tbody>
</table>

If a booking is successfully created, you will be returned a completion url.  If payment is required, this will be the payment page, otherwise it will be the receipt page.

There is currently no way to complete a payment via the API.


##Check-in & Check-out

You can check-in, and checkout a booking.  By default, a note is created under the name of the account when a booking is checked in, or checked out.  VOID, and CANCELLED bookings can not be checked-in or out.

<table>
<tbody>
<tr><th>Description:</th>
<td>Check-in a booking.</td></tr>
<tr><th>Path:</th>
<td>/api/2.1/booking/<b>booking_id</b>/checkin</td></tr>
<tr><th>Methods:</th>
<td>POST</td></tr>
</tbody>
</table> 

<table>
<tr><th>Description:</th>
<td>Check-out a booking.</td></tr>
<tr><th>Path:</th>
<td>/api/2.1/booking/<b>booking_id</b>/checkout</td></tr>
<tr><th>Methods:</th>
<td>POST</td></tr>
</table> 

	POST /api/2.1/booking/JHLL-20131230/checkin

##Bookmark
Bookmarks are made available in the Checkfront mobile apps, and are listed under bookings while logged into the platform.  You can add or remove a bookmark to a specific booking for a specific account.

<table>
<tbody>
<tr>
<th>Description:</th>
<td>Check-out a booking.</td>
</tr>
<tr>
<th>Path:</th>
<td>/api/2.1/booking/<b>booking_id</b>/checkout</td>
</tr>
<tr>
<th>Methods:</th>
<td>POST</td>
</tr>
</tbody>
</table> 

## Notes
Notes can be added to bookings.  By default the authenticated account will be used. 

<table>
<tr><th>Description:</th>
<td>Check-out a booking.</td></tr>
<tr><th>Path:</th>
<td>/api/2.1/booking/<b>booking_id</b>/note</td></tr>
<tr>
<th>Methods:</th><td>POST</td>
</tr>
<tr>
<th>[body]</th>
<td>String: body of the note.  Upto 3000 chars.</td>
</tr>
</tbody>
</table> 

## Status
The status of an existing booking can be modified using the booking/status object.

<table>
<tbody>
<tr>
<th>Description:</th>
<td>Change booking status.</td>
</tr>
<tr>
<th>Path:</th>
<td>/api/2.1/booking/<b>booking_id</b>/status</td>
</tr>
<tr>
<th>Methods:</th>
<td>POST</td>
</tr>
<tr>
<th>[status_id]</th>
<td>String: new booking status.</td>
</tr>
</tbody>
</table> 

## Journal

The booking journal provides access to existing bookings in the system.  You can query bookings by customer id, or individually by id.

<table>
<tbody>
<tr>
<th>Description:</th>
<td>Fetch details on a booking.</td>
</tr>
<tr>
<th>Path:</th>
<td>/api/2.1/booking/journal/
</tr>
<tr>
<th>Path:</th>
<td>/api/2.1/booking/journal/UJII-20131230 (single item)
</tr>
<tr>
<th>Methods:</th>
<td>GET</td>
</tr>
<tr>
<th>[customer_id]</th>
<td>String: customer id filter.</td>
</tr>
<tr>
<th>[start_date]</th>
<td>Date: start date range.</td>
</tr>
<tr>
<th>[end_date]</th>
<td>Date: end date.</td>
</tr>
<tr>
<th>[options]</th>
<td>Array: transactions, notes - options[transactions]=1.</td>
</tr>
</table> 

You can filter bookings based on the available arguments, or fetch a single booking by supplying the booking_id in the path of the API call.  If a single booking is requested, the response is returned in a single "booking" array.  If multiple items are requested, the response is in a "bookings" array.

### Date Range
If selecting bookings based on a date range, both the start date and end dates are required.  The dates are specific to the start and end date of items in the booking. A booking contain items in the invoice not in the time range if other items match.

---

# Customers

The customer object provides access to customers in the system. Customers are created when a booking is made.  

There is currently no way to create customers via the API, outside of creating a booking. 

### Search customers

You can query customers based on indexed fields, including email address, name and customer id.

<table>
<tr><th>Description:</th>
<td>Query customer records</td></tr>
<tr><th>Path:</th><td>/api/2.1/customer/</td></tr>
<tr>
<th>Path:</th>
<td>/api/2.1/customer/XX5-037-755 (single record)</td>
</tr>
<tr>
<th>Methods:</th>
<td>GET</td>
</tr>
<tr>
<th>[customer_email]</th>
<td>String: a unique customer e-mail address.</td>
</tr>
<tr>
<th>[customer_id]</th><td>String: a unique customer id.</td></tr>
<tr>
<th>[options]</th><td>Array: optional return formatting.  Currently: bookings=1 also includes bookings made by the customer.</td></tr>
</table>

	GET /api/2.1/customer/?customer_email=test@checkfront.com&options[bookings]=1


---

Follow [@checkfront](http://twitter.com/checkfront) on Twitter for the latest news.

Have questions on the API?  E-mail us at [code@checkfront.com](code@checkfront.com).

