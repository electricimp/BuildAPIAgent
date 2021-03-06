| :warning: The Electric Imp’s Build API is not supported anymore. Please use [impCentral API](https://apidoc.electricimp.com) and impCentral Environment [Variables](https://developer.electricimp.com/tools/impcentral/environmentvariables) instead. |
| --- |


# BuildAPIAgent

**The Build API is now deprecated and will shortly be removed from service. Please do not use this library**

A very simple integration of the Electric Imp Build API.

This class provides basic interaction with Electric Imp’s Build API in order to provide agent code with extra information that is not available through the [imp API](https://developer.electricimp.com/api). Since this information is typically accessed only once during an application’s runtime, *BuildAPIAgent* operates synchronously, so requests for information such as the name of the model will block your application code until the data is returned.

**To add this library to your project, add** `#require "BuildAPIAgent.class.nut:1.1.1"` **to the top of your agent code**

## Release Notes

### 1.1.1

- Fix bug causing 'wrong number of parameters' in getLatestBuildNumber().

### 1.1.0

- Added asynchronous operation to all public methods. Each method now takes an optional callback function which itself has two parameters: *err* and *data*. The former contains an error message, but is only non-null if there has been an error. The second parameter, *data*, contains the expected result. For example, if you are requesting a device name, *data* will be a string. If you are requesting a build number, *data* will be an integer.
- Added *getAgentList()* method

### 1.0.1

- Remove unneeded extra parameter from *getDeviceName()*

### 1.0.0

- Initial release

## Class Usage

### Constructor: BuildAPIAgent(*apiKey*)

The constructor takes a single, mandatory parameter: a Build API Key associated with your account. You can generate Build API keys by logging in to the Electric Imp IDE.

#### Example

```squirrel
#require "BuildAPIAgent.class.nut:1.1.1"

const APP_NAME = "Weather";
const MY_API_KEY = "<YOUR_BUILD_API_KEY>";

local build = BuildAPIAgent(MY_API_KEY);
server.log("Running app code version " + build.getLatestBuildNumber(APP_NAME));

// Logs 'Running app code version 557'
```

## Class Methods

### getDeviceName(*deviceID[, callback]*)

Use this method to discover the name of a device from its ID. The ID of an agent’s associated device is the value of [imp.configparams.deviceid](https://developer.electricimp.com/api/imp/configparams).

#### Examples

```
server.log("This device is called \"" + build.getDeviceName(imp.configparams.deviceid) + "\"");

// Logs 'This device is called "Buster"'
```

```
build.getDeviceName(imp.configparams.deviceid, function(err, data) {
    if (err) {
        server.error(err);
    } else {
        server.log("Device name: " + data);
    }
});

// Logs 'Device name: Buster'
```

### getModelName(*deviceID[, callback]*)

Use this method to discover the name of the model that the agent and device are running. Pass in the device’s ID, which is the value of [imp.configparams.deviceid](https://developer.electricimp.com/api/imp/configparams).

#### Example

```
server.log("This agent's model is called \"" + build.getModelName(imp.configparams.deviceid) + "\"");

// Logs 'This agent's model is called "WeatherMonitor"'
```

### getModelID(*deviceID[, callback]*)

Use this method to discover the ID of the model that the agent and device are running. Pass in the device’s ID, which is the value of [imp.configparams.deviceid](https://developer.electricimp.com/api/imp/configparams).

#### Examples

```
server.log("This agent's model has ID: \"" + build.getModelID(imp.configparams.deviceid) + "\"");

// Logs 'This agent's model has ID: "A3vEOo1hIHpy"'
```

```
build.getModelID(imp.configparams.deviceid, function(err, data) {
    if (err) {
        server.error(err);
    } else {
        server.log("Model ID: " + data);
    }
});

// Logs 'Model ID: A3vEOo1hIHpy'
```

### getLatestBuildNumber(*modelName[, callback]*)

Use this method to determine the build number of the most recent version of your application code. Pass in the model’s name acquired using *getModelName()*. **Note** this may not be the version of the code your application is actually running &mdash; if you have saved code but not restarted your device(s), for example.

#### Example

```
local modelName = build.getDeviceName(imp.configparams.deviceid);
server.log("Running app code version " + build.getLatestBuildNumber(modelName));

// Logs 'Running app code version 557'
```

### getAgentList(*modelName[, callback]*)

Use this method to gain the agent IDs, ie. the agent URLs, of all of the devices running the model specified by *modelName*, which you can obtain with *getModelName()*. The list is returned as an array of agent ID strings.

**Important** The Build API currently works only with development devices, but when it is extended to production devices, the list of agent IDs may be very large and larger than available agent memory.

#### Example

```
build.getModelName(imp.configparams.deviceid, function(err, data) {
    if (err) {
        server.error(err);
    } else {
        build.getAgentList(data, function(err, data) {
            if (err) {
                server.error(err);
            } else {
                if (data.len() > 0) {
                    server.log("Agent URLs:");
                    foreach (anAgentID in data) {
                        server.log("https://agent.electricimp.com/" + anAgentID);
                    }
                }
            }
        });
    }
});
```

## License

BuildAPIAgent is released under the [MIT License](https://github.com/electricimp/BuildAPIAgent/blob/master/LICENSE).
