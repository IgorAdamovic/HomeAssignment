# HomeAssignment

The app should contain 3 parts: 

Workers list – a list of all workers in the system.

Flights of the worker – a table with all flight information of the worker.

Columns of the table are: 

   • Flight Number   
   • Origin   
   • Origin Date   
   • Destination 
   • Destination Date
   
Flight information – this part will show an extra information of the flight
   • Plane Number.    
   • Duration of the flight  
   • Origin gate  
   • Destination gate 
   
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
