# ROAST 3.0 MultiPrior Upgrade

To call MultiPrior, call `roast(subj, 'multiprior', 'on')`. This will not work if a previous segmentation was done before the upgrade because the options and option files have changed.

`'multiprior'` -- advanced options of ROAST, for controlling multiprior segmentation
`'on' | 'off' (default)`  
The most recent ROAST update will have a new feature installed: MultiPrior. The purpose of MultiPrior is to provide a more accurate MRI segmentation when dealing with abnormalities such as a lesion or an enlarged brain stem that the original ROAST does not consider while segmenting. The MultiPrior AI associates tissue type with neighboring tissue to make predictions which will successfully segment important tissue types as accurately as possible. The goal is to simply add multiprior without the need for python environment creations, which might be difficult.
(see [Example](#example)).  


## Installing MultiPrior

To add MultiPrior to ROAST you will need to download the MultiPrior zip file and make a few changes to the `roast.m` file. The MultiPrior zip file contains the following:

- `MultiPrior.m`: Move this to the `roast-3.0` folder.
- `renameFiles.M`: Move this to the `roast-3.0` folder.

- `Defs.m`: Move this to the `spm12` folder in the `lib` folder in `roast-3.0`.

- `MultiPriors_WEB` folder, which includes the following files:

  - `WARP_indiTPM.m`
  - `SEGMENT.m`
  - `SEGMENT.py`
  - `Segmentation_config.py`
  - `best_model_tf2_singleGPUepoch100.h5`
  - `scripts` folder
    
  - After cloning the repository, Create MultiPriors Enviornment using the .yml file specific to your computer and move to MultiPriors_WEB folder

```
WINDOWS
conda env create -f MultiPriors_env.yml -n MultiPriors_env
move C:\path\to\anaconda3\envs\MultiPriors_env C:\path\to\roast-3.0\lib\MultiPriors_WEB
```

```
LINUX (Ubuntu 22.04)
conda env create -f  MultiPriors_env_linux.yml -n MultiPriors_env_linux
mv /path/to/anaconda3/envs/MultiPriors_env_linux /path/to/roast-3.0/lib/MultiPriors_WEB
```

```
macOS
conda env create -f  MultiPriors_env_mac.yml -n MultiPriors_env_mac
mv /path/to/anaconda3/envs/MultiPriors_env_mac /path/to/roast-3.0/lib/MultiPriors_WEB
```

- `ROAST_GUI.mlappinstall`: Move this to the `roast-3.0` folder.

That's it! You should now have MultiPrior integrated into your ROAST 3.0 installation.

