###############################################################################

IMPORTANT!!
    Updated: 5/8/2026

    This folder contains examples / instructions for reproducing the DR2 BAO
    and FS analyses.

    NERSC access is required for the specified paths and directories.

###############################################################################

Instructions:

    Installation:
        - You will need to set up a clean conda environment and install
          the necessary packages.
        - Please see the home page ReadMe file for installation instructions
          for the picca_bookkeeper, as well as Picca, Vega, and Polychord /
          PocoMC if running the Sampler (not necessary for fitter alone).

    Config File Set-up:
        - To run the bookkeeper, you will need 2 main files: bookkeeper_config.yaml,
          and defaults.yaml

        - bookkeeper_config.yaml:
            - This is the main and first file that the bookkeeper will read to
              set up conifgs and schedule jobs.
            - For KPs, the main file should be kept as clean / simple as possible,
              with most configuration settings residing in the defaults.yaml file.
            - Analysis variations and data splits will use the same defaults.yaml
              file as the baseline, with differences specified in the main
              bookkeeper_config.yaml file.
            - Example bookkeeper_config.yaml files can be found under
              /Examples/configs/

        - defaults.yaml
            - This file contains all other configuration settings not specified in
              the main bookkeeper_config.yaml file. To make changes a given analysis,
              please use the defaults.yaml file as reference, and apply changes to
              the main bookkeeper_config.yaml.
            - Example defaults.yaml files can be found under
              /picca_bookkeeper/resources/default_configs/


    Reproducing DR2 BAO:
        - Copy the DR2 BAO baseline bookkeeper.yaml file
              GitHub: /Examples/configs/data/loa_BAO_baseline.yaml
              NERSC: /global/cfs/cdirs/desi/science/lya/y3/loa/validation_tests/3-0-0-0/configs/baseline.yaml

        - Copy the DR2 BAO defaults.yaml file
              GitHub: /picca_bookkeeper/resources/default_configs/loa_v2.yaml
              NERSC: /global/cfs/cdirs/desi/science/lya/y3/loa/validation_tests/3-0-0-0/configs/loa-3-0-0-0.yaml

        - Change arguments in bookkeeper_config.yaml:
            - "conda environment" -> the path to your own conda environment
            - "mail-user" -> your own email address (notifies if jobs fail)
            - "bookkeeper dir" -> your desired output path location
            - "defaults file" -> make sure this path matches your desired defaults.yaml

        - Change arguments in defaults.yaml:
            - If using file from NERSC, add the following under 'fits/extra args': 
                smooth_covariance:
                  general:
                    correlation-types: auto auto cross cross  
            - (ignore if using file from GitHub)

        - To run the full end-to-end analysis, use the following command in a terminal
          from your active conda environment:
            """
            picca_bookkeeper_run_full_analysis /path_to/bookkeeper_conifg.yaml
            """

        - This will write all config files and schedule jobs for Picca and Vega,
          from delta extraction to the final fit.
        - Suggestion: use "--only-write" in the above command, to write all files
          without submitting jobs. This is useful to check that config files and
          paths are correct before submitting as batch jobs.
        - Compare but DO NOT change config files and output to the DR2 BAO analysis at
          NERSC: /global/cfs/cdirs/desi/science/lya/y3/loa/validation_tests/3-0-0-0/baseline


    Reproducing DR2 Full-Shape:
        - Copy the DR2 FS baseline bookkeeper.yaml file
              GitHub: /Examples/configs/data/loa_FS_baseline.yaml
              NERSC: /global/cfs/cdirs/desi/science/lya/y3-fs/validation-tests/v1-4-0-0/configs/baseline.yaml

        - Copy the DR2 FS defaults.yaml file
              GitHub: /picca_bookkeeper/resources/default_configs/loa_fs_v8.yaml
              NERSC: /global/cfs/cdirs/desi/science/lya/y3-fs/validation-tests/v1-4-0-0/configs/loa_fs_v8.yaml

        - Change arguments in bookkeeper_config.yaml:
            - "conda environment" -> the path to your own conda environment
            - "mail-user" -> your own email address (notifies if jobs fail)
            - "bookkeeper dir" -> your desired output path location
            - "defaults file" -> make sure this path matches your desired defaults.yaml

        - To run the full end-to-end analysis, use the following command in a terminal
          from your active conda environment:
            """
            picca_bookkeeper_run_full_analysis /path_to/bookkeeper_conifg.yaml
            """

        - This will write all config files and schedule jobs for Picca and Vega,
          from delta extraction to the final fit.
        - Suggestion: use "--only-write" in the above command, to write all files
          without submitting jobs. This is useful to check that config files and
          paths are correct before submitting as batch jobs.
        - Compare but DO NOT change config files and output to the DR2 FS analysis at
          NERSC: /global/cfs/cdirs/desi/science/lya/y3-fs/validation-tests/v1-4-0-0/baseline
