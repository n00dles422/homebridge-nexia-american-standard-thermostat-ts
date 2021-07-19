# homebridge-nexia-thermostat-ts

This repo is a from-scratch implementation of the Homebridge interface for Nexia Thermostats.

Steps to get this working:

**Note**: Props to [u/DOOManiac](https://www.reddit.com/user/DOOManiac/) for his posting his instructions on getting started. I shamelessly stole his instructions from [here](https://www.reddit.com/r/homebridge/comments/ecp58p/howto_connect_nexia_thermostat_to_homebridge/) and adapted to work with my plugin.

## 1. Install the plugin
## 2. Copy the configuration
Copy from [accessory-config.json](accessory-config.json) into the ```accessories[]``` in the Homebridge configuration
## 3. Install nexia-api npm Module
You need another npm module, nexia-api, to manually connect to the MyNexia API and dump the info of your thermostat. This module also has no documentation, but don't worry - this guide has everything you need. NOTE: This is a regular nodejs module and is NOT a homebridge module. It cannot be added via Homebridge Config UI X!
Open a terminal or SSH in to your server and install the npm module:
$ sudo npm install -g @cdmnky/nexia-api
(I did this and steps 5 & 6 in a Virtual Machine I was going to throw away, so I didn't care where it went. YMMV though.)
## 4. Activate a new mobile device on MyNexia.com and get HouseId
Homebridge uses the same API as Nexia's mobile apps, so to start using it we need to register a new "mobile" device on MyNexia.com. Open MyNexia.com and log in using the same credentials you used on your mobile app.
On the left side, there is a picture of a house and an "Edit" button to edit your home.
- Click Edit
- Click Advanced
- Under the "Thermostats" section, save the Model and AUID for your thermostat somewhere.
- While you're here, look at your URL and note save the number after /houses/; that's your unique House ID and you'll need it later.

Now click the "Mobile" button in the header to add a new device (which is really Homebridge).
- Click "Add Mobile Device"
- Scroll to the bottom of the page, because the UI here is garbage
- Name the connection something like "API" or "Homebridge" - it doesn't really matter
- For Mobile Connection, select "Wifi" so you don't have to enter a number
- Click Save Changes
- The mobile device page will reload. Find your new API one and click "Get Activation Code". Copy this 12 digit number into the script below in step 5.

## 5. Get the API Key and Mobile ID
Next, we'll use a script I wrote called nexia.js to activate this new mobile connection:
```JS
const nexiaApi = require('@cdmnky/nexia-api');

// Replace this with the code you got from MyNexia.com in Step 4
const activation_code = 123456789012;
nexiaApi.connect(activation_code).then((api) => {
    console.log(api);
}).catch((e) => {
    console.error(e);
});
```

Run the script with this command:
```$ nodejs nexia.js```

If everything goes right, you'll see a simple JSON dump to your terminal window showing your API key and Mobile Id. Save these somewhere, because they are very important and you can't ever get them again without doing step 3 again. And keep them private, because otherwise someone else can mess with your thermostat.

## 6. Update Homebridge Config
Time to update the Homebridge config to replace the placeholders from step 2.
"accessory" must be "NexiaThermostat"
"name" can be anything, such as "Thermostat"
"houseId" is the numeric houseId you got from Step 4
"thermostatIndex" is 0 unless you have more than 1 of them in your house
"xMobileId" is the Mobile Id you got in Step 5. Yes keep the x in front of the key name.
"xApiKey" is the API Key you got in Step 5. Yes keep the x in front of the key name.
"manufacturer", "model" and "serialNumber" can be anything.

If you are on the new American Standard Home service, make sure you set "X-AppVersion" to the latest app version (`5.16.0` at the time of writing) and set "X-AssociatedBrand" to `asair` in your config.

## 7. Cleanup
You can remove the nexia-api npm module and nexia.js once you have the Mobile ID and API keys.


