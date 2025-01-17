# FONDUE
Fast Optimized Network for Denoising through Residual Unified Ensembles

Paper: https://doi.org/10.1101/2023.06.04.543602

FONDUE currently supports only Windows and Linux. Apple Silicon support is underway. 

FONDUE is a Convolutional Neural Network intended for denoising anatomical MR Images of any resolution. This method was built with resolution-invariant capabilities and was trained with a varied and robust corpus with images from datasets of both healthy and diseased individuals.
FONDUE has been thoroughly tested on T1w in-vivo images with resolutions ranging from 1.5mm to 0.25mm of voxel size, GE, Philips, and Siemens scanners, and Magnetic Fields of 1.5T to 7T. Our tests suggest that the best results will be obtained when using input images with voxel sizes close to isotropic.

# Usage of the method
The following are the possible inputs that the evaluation pipeline "fondue_eval_simpleitk.py" can get:

`--in_name`: string containing the full path to the image to be denoised. Valid input extensions: ".nii", ".nii.gz", ".mnc".

`--suffix`: string containing the suffix of the denoised image. By default, it is the name of the network to be used (default network is 'fondue_a').

`--ext`: extension that will be used for the output files. By default, it will use the same as the input file specified in `--in_name`. Valid output extensions: ".nii", ".nii.gz", ".mnc"

`--out_name`: string containing the full path to the file where the image will be stored WITHOUT EXTENSION. By default, it will be the base name of `in_name` + "_"`suffix` + `ext`.

`--keep_dims`: boolean that indicates how the pre-processing of the image will be. If True then it will keep the original image size (HxWxC) and voxel size (v1xv2xv3). If False then it will reshape the image to (M,M,M) where M=max(H,W,C) and voxel size to (v,v,v) where v=min(v1,v2,v3). The performance when this flag is True has not been tested, but preliminary tests show that the performance should not be far from the case where this flag is False. 

`--in_name_new`: string containing the full path without extension of the input image indicated in "in_name" after the resampling and change of zooms. It will only be used if "keep_dims" is False. By default, it will be the base name of "in_name" + "_orig" + "ext".

`--intensity_range_mode`: integer between 0 and 2 that indicates the desired intensity range of the output (denoised) and the resampled input (file indicated in in_name_new). 0 for [0 - 255], 1 for [original_min - original_max] (not recommended due to lack of testing), and 2 for original range (default).

`--order`: integer from 0 to 3 indicating the order of interpolation in case an interpolation is performed during processing. (0=nearest,1=linear(default),2=quadratic,3=cubic)

`--no_cuda`: avoid using CUDA devices.

`--batch_size`: integer indicating the batch size for processing. The default is 1.

`--conform_type`: integer indicating how the reshaping of the input volume will be (when "keep_dims" is False). 0 (default): conform to regular volume size to the largest available and voxel size to smallest available || 1: conform to 256^3 and 1mm isotropic voxel size 

`--name`: a string indicating the name of the FONDUE version to be used. This should not be changed unless a different model is available to use (NOTE: fondue_b1 and fondue_b2 should only be used on the outputs of fondue_b, therefore, one should process first the input volume with fondue_b, and then use the output file as input to these two networks. Automation will be added in upcoming versions). 

`--model_path`: a string that indicates the folder containing the trained weights of the network. By default is the folder "model_checkpoints".

# Installing FONDUE inside a conda environment:
```
conda create -n fondue python=3.9
conda activate fondue
git clone https://github.com/waadgo/FONDUE.git
cd FONDUE
wget https://zenodo.org/records/11403900/files/model_checkpoints.zip
unzip model_checkpoints.zip
pip install -r requirements.txt -U
pip3 install torch==1.13.1+cu117 torchvision==0.14.1+cu117 torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/cu117
```

# Execution example in Windows Subsystem for Linux 2 (WSL2):
`python fondue_eval_simpleitk.py --in_name /full/path/to/mri_image.nii --keep_dims True --intensity_range_mode 0`


