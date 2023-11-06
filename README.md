# READ ME: ROAST 3.0 MultiPrior Upgrade

The most recent ROAST update will have a new feature installed: MultiPrior. 
The purpose of MultiPrior is to provide a more accurate MRI segmentation when dealing with abnormalities such as a lesion or an enlarged brain stem that the original ROAST does not consider while segmenting.
The MultiPrior AI associates tissue type with neighboring tissue to make predictions which will successfully segment important tissue types as accurately as possible. 
The goal is to simply add multiprior without the need for python enviornment creations which might be difficult. 

To call MultiPrior, call roast(subj,‘multiprior’,’on’)
This will not work if a previous segmentation was done before the upgrade because the options and option files have changed.

To add MultiPrior to ROAST you will need to download the MultiPrior zip file and make a few changes to the roast.m file.
MultiPrior zip file contains:
•	MultiPrior.m, this should be moved to the roast-3.0 folder
•	Defs.m, this should be moved to the spm12 folder in the lib folder in roast-3.0
•	MultiPriors_WEB folder, this should be moved to the lib folder in roast-3.0
  o	WARP_indiTPM.m
  o	SEGMENT.m
  o	SEGMENT.py
  o	Segmentation_config.py’
  o	best_model_tf2_singleGPUepoch100.h5
  o	scripts folder
  o	MultiPriors_env folder
  	Unzip MultiPriors_env.rar in the same file location 
•	ROAST_GUI.mlappinstall, this should be moved to the roast-3.0 folder
•	renameFiles.M, , this should be moved to the roast-3.0 folder
