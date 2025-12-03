# ComputeAction

# Description
Supports the computation of a compute in HEC-ResSim.

# Implementation Details

# Process Flow
This action assumes the project files exist within the container before computing. The default behavior of the ressim-runner is to load all global input data sources prior to action execution, so specify all model files as payload level input datasources and the files will be copied to the container before this action is executed.
The copy of input data sources from remote to local uses the name of the input datasource as the root path for all incoming files, and uses the name of the file specified by each path in the datasource to define the destination locally for the incoming datasource paths. This means that the names of the data sources should be constructed based on the folder structure and file names of the HEC-ResSim model structure. 
# Configuration

   ## Environment

   ## Attributes

   ### Action
   * project_file

    ### Global

   ## Inputs
   This action requires an HEC-ResSim model with all of its files to be local to the container prior to computing, that could be accomplished through specification of inputs at the global level. All global inputs are copied locally prior to action execution. The input datasources must be broken into major components by folder in the HEC-ResSim project structure, the HEC-ResSim base directory, the parent rss directory, the shared directory, the alternative directory /rss/<watershedname>, and the alternative rss diretctory rss/<watershedname>/rss (per standard HEC-ResSim file storage conventions).
    ### Input Data Sources
      * "/model/{ATTR::watershed_name}/"
        * wksp
        * projection
        * stream.align
        * dam_ids
        * fragility-samples
      * "/model/{ATTR::watershed_name}/rss/"
        * simperiod
        * rss.conf
        * Obs.fits
      * "/model/{ATTR::watershed_name}/rss/{ATTR::simulation_name}/"
        * wksp
        * stream.align
        * simulation.dss (only necessary if rerunning a previously computed event, if the desire is to run a new set of inputs, use the dss_to_dss action to create the simulation.dss based on an externally provided dss file)
      * "/model/{ATTR::watershed_name}/rss/{ATTR::simulation_name}/rss/"
        * simrun
        * rssrun
        * dss
        * rsys
        * fits
        * igv
        * malt
        * ralt
        * Op.dbf
        * Op.dbt
        * Op.mbx
        * SysOp.dbf
        * SysOp.dbt
        * SysOp.mdx
        * resop.dbf
        * resop.dbt
        * resop.mdx
        * ressysop.dbf
        * ressysop.dbt
        * ressysop.mdx
        * rss.conf
      * "/model/{ATTR::watershed_name}/shared/"
        * breach-tracker
        * hms-output (only necessary if using this as the inputs for the simulation dss file to define a new set of inflows to have ResSim operate on, if the desire is to rerun a previously run simulation, just specify the input simulation dss file as described above.)
   ## Outputs
   The primary output from HEC-ResSim is typically a simulation.dss file, if other output files are necessary, specify them as global output files and they will be copied to remote at the conclusion of the actions (all default path keys will be copied to remote from local by default).
     ### Output Data Sources
      * "/model/{ATTR::watershed_name}/rss/{ATTR::simulation_name}/"
        * simulation.dss
      * other user specified files (log files, other files of interest)
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
        "outputroot": "simulations/event-data",
        "watershed_name": "ffrd_trinity",
        "wname": "Trinity_FFRD",
        "alternative_name": "fema_ffrd",
        "run_name": "fema_ffrd-0",
        "simulation_name": "fema_ffrd",
        "network_name": "Trinity_CWMS_Extended",
        "base_ressim_directory": "reservoir-operations/ffrd_trinity",
        "ressim_output": "reservoir-operations",
        "system_response_directory": "system-response",
        "hms_directory": "hydrology",
        "hms_simulation": "SST"
      },
      "data_sources": [
        {
            "name": "/model/{ATTR::watershed_name}/",
            "paths": {
                "wksp": "{ATTR::scenario}/{ATTR::base_ressim_directory}/{ATTR::watershed_name}.wksp",
                "projection": "{ATTR::scenario}/{ATTR::base_ressim_directory}/{ATTR::wname}.projection",
                "stream.align": "{ATTR::scenario}/{ATTR::base_ressim_directory}/stream.align",
                "fragility-samples": "{ATTR::scenario}/{ATTR::outputroot}/{ENV::CC_EVENT_IDENTIFIER}/{ATTR::system_response_directory}/failure_elevations.json",
                "dam_ids": "{ATTR::scenario}/{ATTR::base_ressim_directory}/dam_ids.json"
            },
            "store_name": "FFRD"
        },
        {
            "name": "/model/{ATTR::watershed_name}/rss/",
            "paths": {
                "simperiod": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}.simperiod",
                "rss.conf": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/rss.conf",
                "Obs.fits": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::alternative_name}Obs.fits"
            },
            "store_name": "FFRD"
        },
        {
            "name": "/model/{ATTR::watershed_name}/rss/{ATTR::simulation_name}/",
            "paths": {
                "wksp": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/{ATTR::simulation_name}.wksp",
                "stream.align": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/stream.align"
            },
            "store_name": "FFRD"
        },
        {
            "name": "/model/{ATTR::watershed_name}/rss/{ATTR::simulation_name}/rss/",
            "paths": {
                "simrun": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::simulation_name}-.simrun",
                "rssrun": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}.rssrun",
                "dss": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}_{ATTR::network_name}.dss",
                "rsys": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}_{ATTR::network_name}.rsys",
                "fits": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}_{ATTR::alternative_name}.fits",
                "igv": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}_{ATTR::alternative_name}.igv",
                "malt": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}_{ATTR::alternative_name}.malt",
                "ralt": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}_{ATTR::alternative_name}.ralt",
                "Op.dbf": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}_{ATTR::network_name}Op.dbf",
                "Op.dbt": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}_{ATTR::network_name}Op.dbt",
                "Op.mdx": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}_{ATTR::network_name}Op.mdx",
                "SysOp.dbf": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}_{ATTR::network_name}SysOp.dbf",
                "SysOp.dbt": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}_{ATTR::network_name}SysOp.dbt",
                "SysOp.mdx": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/{ATTR::run_name}_{ATTR::network_name}SysOp.mdx",
                "resop.dbf": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/resop.dbf",
                "resop.dbt": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/resop.dbt",
                "resop.mdx": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/resop.mdx",
                "ressysop.dbf": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/ressysop.dbf",
                "ressysop.dbt": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/ressysop.dbt",
                "ressysop.mdx": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/ressysop.mdx",
                "rss.conf": "{ATTR::scenario}/{ATTR::base_ressim_directory}/rss/{ATTR::simulation_name}/rss/rss.conf"
            },
            "store_name": "FFRD"
        },
        {
            "name": "/model/{ATTR::watershed_name}/shared/",
            "paths": {
                "breach-tracker": "{ATTR::scenario}/{ATTR::base_ressim_directory}/shared/breach_tracker_config.csv",
                "hms-output": "{ATTR::scenario}/{ATTR::outputroot}/{ENV::CC_EVENT_IDENTIFIER}/{ATTR::hms_directory}/{ATTR::hms_simulation}.dss"
            },
            "store_name": "FFRD"
        }
    ]
    },
    "outputs":[
        {
            "name": "/model/{ATTR::watershed_name}/rss/{ATTR::simulation_name}/",
            "paths": {
                "simulation.dss": "{ATTR::scenario}/{ATTR::outputroot}/{ENV::CC_EVENT_IDENTIFIER}/{ATTR::ressim_output}/simulation.dss"
            },
            "store_name": "FFRD"
        }
    ],
    "actions": [
        {
            "type": "compute_simulation",
            "description": "computing a simulation",
            "attributes": {
                "project_file": "/model/ffrd_trinity/ffrd_trinity.wksp"
            },
            "stores": [],
            "inputs": [],
            "outputs": []
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