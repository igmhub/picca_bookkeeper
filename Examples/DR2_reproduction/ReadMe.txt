###############################################################################

IMPORTANT!!
    Updated: 8/24/2026

    This folder contains examples / instructions for reproducing the DR2 BAO
    and FS analyses.

###############################################################################

Instructions:

    Installation:
        - You will need to set up a clean conda environment and install
          the necessary packages.
        - Please see the home page ReadMe file for installation instructions
          for the picca_bookkeeper, as well as Picca, Vega, and Polychord /
          PocoMC if running the Sampler (not necessary for fitter alone).
        - Note that you may need specific version of codes when running old 
          analyses, see below for tagged version used in each. 


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
        - Download the appropriate software versions:
		  - picca_bookkeeper version: 5.1.3 (DR2_FS)
		  - picca version: 9.12.0
            - note: you will need to downgrade setup tools for backwards compatibility:
				"pip install "setuptools<81"
		  - vega version: 1.3.1
            - note: you will also need to downgrade scipy for backwards compatibility: 
                "pip install scipy=1.4.1"
	
	  - If you have cloned the repositories, you can install the desired version 
        to your conda environment by first using "git checkout v0.0.0", where 0.0.0 is 
        the desired tagged version. E.g. for DR2 BAO, you will install the version 
        used at the time using: 
        e.g. picca: 
		  - git clone https://github.com/igmhub/picca.git
		  - cd picca
		  - git checkout v9.12.0
		  - pip install -e .
		Or for the bookkeeper: 
		  - git clone https://github.com/igmhub/picca_bookkeeper
		  - cd picca_bookkeeper
		  - git checkout DR2_FS
		  - pip install -e .

        - Copy the DR2 BAO baseline bookkeeper.yaml file
              GitHub: /Examples/configs/data/loa_BAO_baseline.yaml

        - Copy the DR2 BAO defaults.yaml file
              GitHub: /picca_bookkeeper/resources/default_configs/loa_v2.yaml

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
            picca_bookkeeper_run_full_analysis /path_to/bookkeeper_conifg.yaml --only-write
            """

        - This will write all config files and schedule jobs for Picca and Vega,
          from delta extraction to the final fit, but not run them. 
          This is useful to check that config files and paths are correct before 
          submitting as batch jobs.
        - Before submitting the jobs, you will need to manually update the write 
          and smooth covariance scripts to ensure backwards compatibility. 
		- In "run_write_full_covariance.sh" located under /fits/scripts/, change 
			"""
			picca_write_full_covariance.py
			"""
			to 
			"""
			/global/cfs/cdirs/desicollab/science/lya/y1-kp6/iron-tests/correlations/scripts/write_full_covariance_matrix_flex_size.py
			"""
			You will also need to add "--lya-lya", "--lya-lyb", "--lya-qso", 
            and "--lyb-qso" before the corresponding fits.gz files. For example, 
            in front of "/.../lyalya_lyalya.fits.gz", add "--lya-lya". 
            Similarly, before "/.../qso_lyalyb.fits.gz", add "--lyb-qso". 
		- In "run_smooth_covariance.sh" located under /fits/scripts/, change 
			"""
			picca_write_smooth_covariance.py
			"""
			to 
			"""
			/global/cfs/cdirs/desicollab/science/lya/y1-kp6/iron-tests/correlations/scripts/write_smooth_covariance_flex_size.py
			"""
			and change
			"""
			--correlation-types
			"""
			to
			"""
			--block-types
			"""
            
        - Once you have made these necessary changes, you can submit the jobs. 
          Because of backwards compatibility issues, it is recommended to do this
          manually rather than using the bookkeeper's scheduling. You can submit 
		  batch jobs using "--sbatch" from the terminal.
          For example: 
              """
              sbatch /...deltas/scripts/run_delta_extraction_ciii_calib_step_1.sh
              """
          
          The order that the jobs should be submitted in are as follows: 
            1) Deltas: ciii calib, lya + lyb
            2) Correlations: cf lyalya + cf lyalyb + xcf lyaqso + xcf lybqso
            3) Distortions: dmat lyalya + dmat lyalyb, xdmat qsolya + xdmat qsolyb
            4) Export CF and XCF
            5) Covariance: write, smooth
            6) Fit

        - You can compare but DO NOT change config files and output to the DR2 BAO analysis at
          NERSC: /global/cfs/cdirs/desi/science/lya/y3/loa/validation_tests/3-0-0-0/baseline/



    Reproducing DR2 Full-Shape:
        - Download the appropriate software versions (picca, bookkeeper, vega)
		  - picca_bookkeeper version: 6.0.1 (DR2_FS_v2)
		  - Picca version: 9.19.0
		  - vega version: 1.7.4
	
	  - If you have cloned the repositories, you can install the desired version 
        to your conda environment by first using "git checkout v0.0.0", where 0.0.0 is 
        the desired tagged version. E.g. for DR2 Full Shape, you will install the 
        version used at the time using: 
        e.g. picca: 
            - git clone https://github.com/igmhub/picca.git
    	    - cd picca
    	    - git checkout v9.19.0
    	    - pip install -e .
    	Or for the bookkeeper: 
            - git clone https://github.com/igmhub/picca_bookkeeper
    	    - cd picca_bookkeeper
            - git checkout DR2_FS_v2
    	    - pip install -e .

        - Copy the DR2 FS baseline bookkeeper.yaml file
              GitHub: /Examples/configs/data/loa_FS_baseline.yaml

        - Copy the DR2 FS defaults.yaml file
              GitHub: /picca_bookkeeper/resources/default_configs/loa_fs_v8.yaml

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
          NERSC: /global/cfs/cdirs/desi/science/lya/y3-fs/validation-tests/v1-4-0-0/baseline/
