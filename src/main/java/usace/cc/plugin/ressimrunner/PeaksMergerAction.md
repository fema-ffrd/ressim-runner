# PeaksMergerAction

# Description
Supports aggregating exported peak datasets into a summary across many events. Since the ResSim compute is computing at massive horizontal scale with each compute element being one event the peaks that are exported during the event would either need to be written to a datastore that suports multiwrite capability to avoid race conditions and overwrites or we can summarize outputs at the end of the process. This is to support the post process merging of event level data.

# Implementation Details

# Process Flow
This action assumes the csv files being aggregated are in a recognizable pattern. 
# Configuration

   ## Environment

   ## Attributes

   ### Action
   * start-event -> the event number to start aggregating on
   * end-event -> the event number to end aggregating on (inclusive)
   * exported-peak-durations -> the durations that were exported duringn the event level compute that are desired to be aggregated

    ### Global

   ## Inputs
   This action requires the exported csv files to be remote and follow the path structure defined in the input source and for all specified dss pathnames in the exported_peak_paths to be present in the DSS file (and be the same for all event level files for a given duration).
    ### Action Level Input Data Sources
      * source
        * default -> the keyword $eventnumber is used to substitute event numbers to range over the output csv files

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
      "payload_attributes": {
        "scenario": "production",
        "outputroot": "simulations",
        "ressim_output": "reservoir-operations",
      },
      "data_sources": []
    },
    "outputs":[],
    "actions": [
        {
            "type": "peaks_merger",
            "description": "merge peaks to csv per eventrange",
            "attributes": {
                "start-event": 1,
                "end-event": 20000,
                "exported-peak-durations": [
                    1,
                    24,
                    48,
                    72
                ]
            },
            "stores": [{
                "name": "FFRDP",
                "store_type": "S3",
                "profile": "FFRD",
                "params": {
                    "root": "model-library/ffrd-trinity"
                }
            }],
            "inputs": [{
                "name": "source",
                "paths": {
                    "default":"{ATTR::scenario}/{ATTR::outputroot}/event-data/$eventnumber/{ATTR::ressim_output}/$durationh_peaks.csv"
                },
                "store_name": "FFRDP"
            }],
            "outputs": [{
                "name": "destination",
                "paths": {
                    "1":"{ATTR::scenario}/{ATTR::outputroot}/summary-data/{ATTR::ressim_output}/realization_1_1h_peaks.csv",
                    "24":"{ATTR::scenario}/{ATTR::outputroot}/summary-data/{ATTR::ressim_output}/realization_1_24h_peaks.csv",
                    "48":"{ATTR::scenario}/{ATTR::outputroot}/summary-data/{ATTR::ressim_output}/realization_1_48h_peaks.csv",
                    "72":"{ATTR::scenario}/{ATTR::outputroot}/summary-data/{ATTR::ressim_output}/realization_1_72h_peaks.csv"
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