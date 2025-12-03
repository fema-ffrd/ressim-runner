# DssToDSSAction

# Description
Supports transfer of dss data from an input dss file into a new output dss file.

# Implementation Details

# Process Flow
This action assumes source dss file is local to the container. 
# Configuration

   ## Environment

   ## Attributes

   ### Action
   * fill_empty_values -> if true any empty values in the dataset will be filled with either the minimum value specified by dss convention, or repeat the previous value.

    ### Global

   ## Inputs
   This action requires the source dss file is local to the container prior to computing, that could be accomplished through specification of inputs at the global level. All global inputs are copied locally prior to action execution. The input datasources will be copied over based on the combination of the datasource name, and the name of the file in the file path to create the local file path.
    ### Action Level Input Data Sources
      * source
        * default
    ### Action Level Output Data Sources
      * destination
        * default
   ## Outputs
   The primary output from this action is the outputdatasource destination DSS file. To post this from local to remote, the output must be specified as a global output destination.
     ### Output Data Sources
      * "/model/" (or where ever the file is stored local to the container)
        * simulation.dss (or what ever the file is named in the local container)
      
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
                "hms-output": "incoming.dss"
            },
            "store_name": "FFRD"
        }
    ]
    },
    "outputs":[
        {
            "name": "/model/",
            "paths": {
                "simulation.dss": "outgoing.dss"
            },
            "store_name": "FFRD"
        }
    ],
    "actions": [
        {
            "type": "dss_to_dss",
            "description": "moving data from one dss file to another",
            "attributes": {
                "fill_empty_values": true
            },
            "stores": [],
            "inputs": [
                {
                    "name": "source",
                    "paths": {
                        "default": "/model/incoming.dss"
                    },
                    "data_paths":{
                        "0": "//chambers-ck_j010/FLOW-LOCAL//1Hour/RUN:SST/",
                        "1": "//nid_tx00009_in_j010/FLOW//1Hour/RUN:SST/",
                        "2": "//nid_tx00001_in_j010/FLOW//1Hour/RUN:SST/"
                    },
                    "store_name": "FFRD"
                }
            ],
            "outputs": [
                {
                    "name": "destination",
                    "paths": {
                        "default": "/model/outgoing.dss"
                    },
                    "data_paths":{
                        "0": "//chambers-ck_j010/FLOW-LOCAL//1Hour/RUN:SST/",
                        "1": "//nid_tx00009_in_j010/FLOW//1Hour/RUN:SST/",
                        "2": "//nid_tx00001_in_j010/FLOW//1Hour/RUN:SST/"
                    },
                    "store_name": "FFRD"
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