# ROAST 3.0 MultiPrior Upgrade

To call MultiPrior, call `roast(subj, 'multiprior', 'on')`. This will not work if a previous segmentation was done before the upgrade because the options and option files have changed.

`'multiprior'` -- advanced options of ROAST, for controlling multiprior segmentation
`'on' | 'off' (default)`  
The most recent ROAST update will have a new feature installed: MultiPrior. The purpose of MultiPrior is to provide a more accurate MRI segmentation when dealing with abnormalities such as a lesion or an enlarged brain stem that the original ROAST does not consider while segmenting. The MultiPrior AI associates tissue type with neighboring tissue to make predictions which will successfully segment important tissue types as accurately as possible. The goal is to simply add multiprior as simply as possible as it runs using Python. Scripts will be added to roast-3.0 which allow MultiPrior to run seamlessly. A conda enviornment will have to be created using the system specific .yml file.
(see [Example](#example)).  

#### Example 

    roast('example/subject1.nii',[],'multiprior','on')

Run simulation on subject1 with default recipe, but use multiprior segmentations instead of spm.

## Installing MultiPrior

To add MultiPrior to ROAST you will need to download the MultiPrior zip file and make a few changes to the `roast.m` file and other files.
The MultiPrior zip file contains the following:

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
  -  Create MultiPriors Enviornment using the batch file specific to your operating system:

```
WINDOWS

Double click and the 'setupWindows.bat' file and it will automatically download the enviornment.
It will use the 'MultiPriors_env.yml' file 
*Anconda must already be installed for this to work.
```

```
LINUX (Ubuntu 22.04)

Run the 'setupLinux.sh' file in bash and it will download the enviornment.
It will use the 'MultiPriors_env_Linux.yml' file 
*Anconda must already be installed for this to work.
```

```
macOS

Run the 'setupMac.sh' file in bash and it will download the enviornment.
It will use the 'MultiPriors_env_Mac.yml' file 
*Anconda must already be installed for this to work.
```

```
Alternative

Create your own conda enviornment named 'MultiPriors_env' with the proper OS tag (None, Linux, or Mac) 
Place the enviornment into your MultiPriors_WEB Folder
Conda Install:
-tensorflow
-scikit-image
-nibabel
```

## Adding MultiPrior Options to Existing Files

### 'roast.m' located in roast-3.0 folder
  -  Add on line 60    
```
% Update Provided by Andrew Birnbaum 
% Application and MultiPrior Option
% MultiPrior Segmentation Provided by Lucas Hirsch 
% November 2023
 ```
  - Add and Replace on line 137 
 ```
 case 'multiprior'
            multiprior = varargin{indArg+1};
            indArg = indArg+2;
        otherwise
            error('Supported options are: ''capType'', ''elecType'', ''elecSize'', ''elecOri'', ''T2'', ''meshOptions'',''multiprior'',''conductivities'', ''simulationTag'', ''resampling'', and ''zeroPadding''.');
 ```
   - Add on line 614
 ```
if ~exist('multiprior','var')
   multiprior = 0;
else
    if ~ischar(multiprior), error('Unrecognized option value. Please enter ''on'' or ''off'' for option ''multiprior''.'); end
    if strcmpi(multiprior,'off')
        multiprior = 0;
    elseif strcmpi(multiprior,'on')
        multiprior = 1;
    else
        error('Unrecognized option value. Please enter ''on'' or ''off'' for option ''multiprior''.');
    end
end
 ```
- Replace on line 735
 ```
options = struct('configTxt',configTxt,'elecPara',elecPara,'T2',T2,'meshOpt',meshOpt,'multiprior',multiprior,'conductivities',conductivities,'uniqueTag',simTag,'resamp',doResamp,'zeroPad',paddingAmt,'isNonRAS',isNonRAS);
  ```
- Replace on line 814
```
    if (isempty(T2) && ~exist([dirname filesep baseFilenameRasRSPD '_T1orT2_masks.nii'],'file')) ||...
            (~isempty(T2) && ~exist([dirname filesep baseFilenameRasRSPD '_T1andT2_masks.nii'],'file'))
       
        if multiprior
        disp('======================================================')
        disp('     STEP 2 (out of 6): MultiPrior SEGMENTATION...    ')
        disp('======================================================')
        MultiPrior(subjRasRSPD);

        else 
        disp('======================================================')
        disp('     STEP 2 (out of 6): SEGMENTATION TOUCHUP...       ')
        disp('======================================================')
        segTouchup(subjRasRSPD,T2);
        end
    
    elseif multiprior && ((~(isempty(T2) && ~exist([dirname filesep baseFilenameRasRSPD '_T1orT2_masks.nii'],'file')) ||...
        (~isempty(T2) && ~exist([dirname filesep baseFilenameRasRSPD '_T1andT2_masks.nii'],'file'))) &&...
         ~exist([dirname '\' baseFilenameRasRSPD '_T1orT2_masks_MultiPrior_Segmentation.nii'],'file'))
        
        disp('======================================================')
        disp('  STEP 2 (out of 6): MultiPrior SEGMENTATION...       ')
        disp('======================================================')
        MultiPrior(subjRasRSPD);

    elseif ~multiprior && (~(isempty(T2) && ~exist([dirname filesep baseFilenameRasRSPD '_T1orT2_masks.nii'],'file'))&&...
             ~exist([dirname '\' baseFilenameRasRSPD '_T1orT2_masks_Roast_Segmentation.nii'],'file'))||...
            ((~isempty(T2) && ~exist([dirname filesep baseFilenameRasRSPD '_T1andT2_masks.nii'],'file')) && ...
            ~exist([dirname '\' baseFilenameRasRSPD '_T1andT2_masks_Roast_Segmentation.nii'],'file'))
     
        disp('======================================================')
        disp('     STEP 2 (out of 6): SEGMENTATION TOUCHUP...       ')
        disp('======================================================')
        segTouchup(subjRasRSPD,T2);
        
          
    else
        disp('======================================================')
        disp('    SEGMENTATION ALREADY DONE, SKIP STEP 2            ')
        disp('======================================================')
    end
    
else
    
    disp('======================================================')
    disp(' NEW YORK HEAD SELECTED, GOING TO STEP 3 DIRECTLY...  ')
    disp('======================================================')
    warning('New York head is a 0.5 mm model so is more computationally expensive. Make sure you have a decent machine (>50GB memory) to run ROAST with New York head.')
    [~,baseFilenameRasRSPD] = fileparts(subjRasRSPD);
    
end

renameFiles(dirname, baseFilenameRasRSPD, multiprior)
```
### 'writeRoastLog.m' located in roast-3.0 folder
- Add on line 136
```
fprintf(fid,'multiprior:\t');
        if opt.multiprior
            fprintf(fid,'on');
        else
            fprintf(fid,'off');
        end
        fprintf(fid,'\n');
 ```
### 'isNewOptions.m' located in roast-3.0 folder
- Add on line 190
```
if optNew.multiprior ~= optOld.multiprior
            isNewOpt = 1;
            return
        end
```
### 'segTouchUp.m' located in roast-3.0 folder
- Replace on line 330
```
save_untouch_nii(white,[dirname filesep baseFilename '_masks_Roast_Segmentation.nii']);
```


That's it! You should now have MultiPrior integrated into your ROAST 3.0 installation.

