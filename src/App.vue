<template>
  <div id="q-app">
    <router-view />
  </div>
</template>
<script>
 import {gv} from './global/common_sym' 
 export default {
   name: 'App',

   mounted: function() {
     window.onbeforeunload = function () {
       if (gv.wsocket !== undefined) {
         console.log('wsocket here')
         gv.wsocket.close()
         gv.wsocket = undefined
         // sleep(1000)
       }
       return undefined// not show dialog
     }
     

   },

   updated: function () {
     console.log("Starting connection to WebSocket Server")
     gv.wsocket = new WebSocket('ws://0.0.0.0:8000/websocket')
     console.log(gv.wsocket)
     gv.wsocket.onmessage = function(event) {
       console.log(event);
     }

     gv.wsocket.onopen = function(event) {
       console.log(event)
       console.log("Successfully connected to the echo websocket server...")
       gv.wsocket.send("Client knows connection opened!\n")
     }
     gv.wsocket.onerror = function(event) {
       console.log('WebSockets error: ' + event.data + '\n');
     }
     gv.wsocket.onclose = function(event) {
       console.log('WebSockets close: ' + event.data + '\n');
     };
     gv.wsocket.onmessage = function(event) {
       console.log('WebSockets message: ' + event.data + '\n');
       var stockObj =JSON.parse(event.data)

       // in Haskell server side ,its defition: 
       /* data StockData = StockData {
        *   code :: String,
        *   pricesL :: [Float]
        * } deriving (Generic , Show) */
       
       console.log('stock price list is: ' + stockObj.pricesL + '\n');
     };
     //gv.wsocket.send("Clinet send data to you!\n")
   },

   beforeDestroy: function (){
     console.log('beforeDestroy bye')
     console.log(gv.wsocket)
     if (gv.wsocket !== undefined) {
       console.log('wsocket here')
       gv.wsocket.send("Client out!\n")
       gv.wsocket.close()
       gv.wsocket = undefined
     }
     //ws.send('Client exit \n');
   }
 }
</script>
