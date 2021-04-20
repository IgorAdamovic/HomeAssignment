# HomeAssignment

The app should contain 3 parts: 

1. Workers list – a list of all workers in the system.
2. Flights of the worker – a table with all flight information of the worker.
   Columns of the table are: 
   1. Flight Number
   2. Origin
   3. Origin Date
   4. Destination
   5. Destination Date
3. Flight information – this part will show an extra information of the flight
   a. Plane Number
   b. Duration of the flight
   c. Origin gate
   d. Destination gate
Clicking on worker name, will show all his flights in the table.
Clicking on specific flight, will show the flight details in Flight Information.
The flight information should be refreshed automatically every 1 minute.
The first row is default selected row of the table.
The duration should be displayed in prettier format. 
Example: 350 (response from API in minutes) → 5h 50m

## Endpoints
You have access to endpoints:
• List of workers: https://interview-mock.herokuapp.com/api/workers/
• Flights of the worker: https://interview-mock.herokuapp.com/api/workers/:worker_id

## Client implementation
You can use any third-party library if you wish.
It should work at least on Chrome browser.

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
