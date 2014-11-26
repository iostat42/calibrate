Calibrate [![Build Status](https://travis-ci.org/johnbrett/calibrate.svg?branch=master)](https://travis-ci.org/johnbrett/calibrate) [![Dependency Status](https://david-dm.org/johnbrett/calibrate.svg)](https://david-dm.org/johnbrett/calibrate)
=========

Micro library for providing uniform json output for RESTful APIs, with error handling. 

Feel free to raise an issue or contact me on twitter if you have any questions @\_johnbrett\_. Feature requests and bug reports are welcomed.

*Please star if you using this module so I know where to focus my time spent on open source work.*

**Usage:**
```javascript
var Calibrate = require('calibrate')

/**
* Checks whether data is an error, and calls Calibrate.error or Calibrate.reponse
**/
Calibrate(data)          // returns calibrated json object

// Valid response structure:
{
    "statusCode": ...,
    "data":       ...,
    "meta":       ...
}

// Error response structure:
{
    "statusCode": ...,
    "error":      ...,
    "message":    ...
}

/**
* If data is non-null and defined:
*  -  wraps value in object with statusCode and meta properties
* If null or undefined
*  -  returns a Boom notFound error object
**/
Calibrate.response(data) // assumes valid response, returns calibrated object or 404

/**
* If is a Boom Error object
*  - returns object as is
* If is a non Boom Error Object
*  - returns a Boom badImplementaion Error Object
**/
Calibrate.error(data)    // assumes error, 
```

Example in Hapijs:
```javascript
var Hapi = require('hapi');
var Calibrate = require('calibrate');
var server = new Hapi.Server(3000);

server.route([
    {
        method: 'GET',
        path: '/user/{id}',
        handler: function(request, reply) {  // Using Promises
            User
                .findById(request.params.id) 
                .then(Calibrate.response) // Formats Response
                .catch(Calibrate.error)   // Errors caught and wrapped
                .then(reply)              // Return Calibrated Response
        }
    },
    {
        method: 'GET',
        path: '/team/{id}',
        handler: function (request, reply) { // Using Callbacks
            Team.findbyId(request.params.id, function returnUser(err, team) {
                if(err) {                 // Catch any errors
                    reply(Calibrate(err)  // Errors caught and wrapped 
                } else {
                    reply(Calibrate(team) // Return Calibrate Response
                }
            })
        }
    }
]);

server.start(function () {
    console.log('Server running at:', server.info.uri);
});
```

License MIT @ John Brett
