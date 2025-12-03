# UpdateTimeWindowFromDssAction

# Description
Supports setting the simperiod file in ResSim (which contains the simulation time window) lookback, start and end date times based on an incoming DSS file and record.

# Implementation Details

# Process Flow
This action assumes source dss file and simperiod file are local to the container. 
# Configuration

   ## Environment

   ## Attributes

   ### Action
   * lookback_duration -> sets the start time this many duration units forward from the first time period of the DSS record.
   * lookback_units -> can be "hours" "minutes' or "days".

    ### Global

   ## Inputs
   This action requires the simperiod and dssfiles are local to the container prior to computing, that could be accomplished through specification of inputs at the global level. All global inputs are copied locally prior to action execution. The input datasources will be copied over based on the combination of the datasource name, and the name of the file in the file path to create the local file path.
    ### Action Level Input Data Sources
      * simeriod
        * default
      * dssfile
        * default
    ### Action Level Output Data Sources
      * simperiod
        * default
   ## Outputs
   The primary output from this action is the outputdatasource simperiod file. To post this from local to remote, the output must be specified as am action level output datasource.
      
# Configuration Examples
```json
{
    "manifest_name":     "RESSIM-runner-trinity",
    "plugin_definition": "FFRD-RESSIM-RUNNER-TRINITY",
    "stores": [
        {
            "name": "FFRD",
            "store_type": "S3",
            "profile": "FFRD",
            "params": {
                "root": "model-library/ffrd-trinity"
            }
        }
    ],
    "inputs":{
      "payload_attributes": {},
      "data_sources": [
        {
            "name": "/model/",
            "paths": {
                "simperiod": "ffrd-trinity.simperiod",
                "hms-output": "SST.dss"
            },
            "store_name": "FFRD"
        }
    ]
    },
    "outputs":[
        {
            "name": "/model/",
            "paths": {
                "simulation.dss": "simulation.dss"
            },
            "store_name": "FFRD"
        }
    ],
    "actions": [
        {
            "type": "updated_timewindow_from_dss",
            "description": "using input hms dss file to define simperiod lookback, start and end date times",
            "attributes": {
                "lookback_duration": 1,
                "lookback_units": "hours"
            },
            "stores": [{
                "name": "FFRDP",
                "store_type": "S3",
                "profile": "FFRD",
                "params": {
                    "root": "model-library/ffrd-trinity"
                }
            }],
            "inputs": [
                {
                    "name": "simperiod",
                    "paths": {
                        "default": "/model/ffrd-trinity.simperiod"
                    },
                    "store_name": "FFRD"
                },{
                    "name": "dssfile",
                    "paths": {
                        "default": "/model/SST.dss"
                    },
                    "data_paths": {
                        "default": "//chambers-ck_j010/FLOW-LOCAL//1Hour/RUN:SST/"
                    },
                    "store_name": "FFRD"
                }
            ],
            "outputs": [
                {
                    "name": "simperiod",
                    "paths": {
                        "default": "ffrd-trinity.simperiod"
                    },
                    "store_name": "FFRDP"
                }
            ]
        }
    ]
}
```

# Outputs

   - Format

    - fields

    - field definitions

# Error Handling

# Usage Notes

# Future Enhancements

# Patterns and best practices