# PostPeaksAction

# Description
Supports exporting peak data from dss at user specified durations to csv on a per event basis.

# Implementation Details

# Process Flow
This action assumes source dss file is local to the container (typically this action is added directly after the compute action). 
# Configuration

   ## Environment

   ## Attributes

   ### Action
   * exported_peak_paths
   * exported_peak_durations

    ### Global

   ## Inputs
   This action requires the DSS file to be local and for all specified dss pathnames in the exported_peak_paths to be present in the DSS file.
    ### Action Level Input Data Sources
      * source
        * default

    ### Action Level Output Data Sources
      * destination
        * a key for each exported_peak_duration specified* simperiod
   ## Outputs
   The output from this action is a csv for each duration containing the event number and the computed peak value for each exported path for the given duration. The output is posted to remote as part of the action and does not rely on global output posting.
      
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
                "dss": "simulation.dss"
            },
            "store_name": "FFRD"
        }
    ]
    },
    "outputs":[],
    "actions": [
        {
            "type": "post_peaks",
            "description": "post peaks to csv per event",
            "attributes": {
                "exported-peak-paths": [
                    "//AT_SF_Railroad/Flow//1Hour/fema_ffrd-0/",
                    "//Above_JohnsonCk_Lag/Flow//1Hour/fema_ffrd-0/",
                    "//Above_Rosser_Below_EastFork/Flow//1Hour/fema_ffrd-0/",
                ],
                "exported-peak-durations": [
                    1,
                    24,
                    48,
                    72
                ]
            },
            "stores": [{
                "name": "FFRDP",
                "id": "6ba7b810-9dad-11d1-80b4-00c04fd430c8",
                "store_type": "S3",
                "profile": "FFRD",
                "params": {
                    "root": "model-library/ffrd-trinity"
                }
            }],
            "inputs": [{
                "name": "source",
                "paths": {
                    "default": "/model/simulation.dss"
                },
                "store_name": "FFRD"
            }],
            "outputs": [{
                "name": "destination",
                "paths": {
                    "1":"1h_peaks.csv",
                    "24":"24h_peaks.csv",
                    "48":"48h_peaks.csv",
                    "72":"72h_peaks.csv"
                },
                "store_name": "FFRDP"
            }]
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