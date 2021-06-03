# XVFI
**This is the official repository of XVFI (eXtreme Video Frame Interpolation), https://arxiv.org/abs/2103.16206**

Last Update: 20210603

We provide the training and test code along with the trained weights and the dataset (train+test) used for XVFI. 
If you find this repository useful, please consider citing our [paper](https://arxiv.org/abs/2103.16206).

## Table of Contents
1. [X4K1000FPS](#X4K1000FPS)
1. [Requirements](#Requirements)
1. [TestCode](#Test Code)
1. [TrainingCode](#Training Code)
1. [Reference](#Reference)


## X4K1000FPS
#### Dataset of high-resolution, high-fps video frames with extreme motion
![003](/figures/003.gif "003") ![004](/figures/004.gif "004") ![045](/figures/045.gif "045")
![078](/figures/078.gif "078") ![081](/figures/081.gif "081") ![146](/figures/146.gif "146")\
<Some examples of X4K1000FPS dataset, which are frames of 1000-fps and 4K-resolution. Our dataset contains the various scenes with extreme motions. (Displayed in spatiotemporally subsampled .gif files)>

We provide our X4K1000FPS dataset which consists of X-TEST and X-TRAIN. Please refer to our main/suppl. [paper](https://arxiv.org/abs/2103.16206) for the details of the dataset. You can download the dataset from this dropbox [link](https://www.dropbox.com/sh/duisote638etlv2/AABJw5Vygk94AWjGM4Se0Goza?dl=0).

`X-TEST` consists of 15 video clips with 33-length of 4K-1000fps frames. It follows the below directory format:
```
├──── YOUR_DIR/
    ├──── test/
       ├──── Type1/
          ├──── TEST01/
             ├──── 0000.png
             ├──── ...
             └──── 0032.png
          ├──── TEST02/
             ├──── 0000.png
             ├──── ...
             └──── 0032.png
          ├──── ...
       ├──── ...
```

`X-TRAIN` consists of 4,408 clips from various types of 110 scenes. The clips are 65-length of 1000fps frames. Each frame is the size of 768x768 cropped from 4K frame. It follows the below directory format:
```
├──── YOUR_DIR/
    ├──── train/
       ├──── 002/
          ├──── occ008.320/
             ├──── 0000.png
             ├──── ...
             └──── 0064.png
          ├──── occ008.322/
             ├──── 0000.png
             ├──── ...
             └──── 0064.png
          ├──── ...
       ├──── ...
```

After downloading the files from the link, decompress the `encoded_test.tar.gz` and `encoded_train.tar.gz`. The resulting .mp4 files can be decoded into .png files via running `mp4_decoding.py`. Please follow the instruction written in `mp4_decoding.py`.


## Requirements
Our code is implemented using PyTorch1.7, and was tested under the following setting:  
* Python 3.7 
* PyTorch 1.7.1
* CUDA 10.2  
* cuDNN 7.6.5  
* NVIDIA TITAN RTX GPU
* Ubuntu 16.04 LTS

**Caution**: since there is "align_corners" option in "nn.functional.interpolate" and "nn.functional.grid_sample" in PyTorch1.7, we recommend you to follow our settings.
Especially, if you use the other PyTorch versions, it may lead to yield a different performance.



## Test Code
### Quick Start for X-TEST (x8 Multi-Frame Interpolation as in Table 2)
1. Download the source codes in a directory of your choice **\<source_path\>**.
2. First download our X-TEST test dataset by following the above section 'X4K1000FPS'.
3. Download the pre-trained weights, which was trained by X-TRAIN, from [this link](https://www.dropbox.com/s/xj2ixvay0e5ldma/XVFInet_X4K1000FPS_exp1_latest.pt?dl=0) to place in **\<source_path\>/checkpoint_dir/XVFInet_X4K1000FPS_exp1**.
```
XVFI
└── checkpoint_dir
   └── XVFInet_X4K1000FPS_exp1
       ├── XVFInet_X4K1000FPS_exp1_latest.pt           
```
4. Run **main.py** with the following options in parse_args: 
```bash
python main.py --gpu 0 --phase 'test' --exp_num 1 --dataset 'X4K1000FPS' --module_scale_factor 4 --S_tst 5 --multiple 8 
```
==> It would yield **(PSNR/SSIM/tOF) = (30.12/0.870/2.15)**.
```bash
python main.py --gpu 0 --phase 'test' --exp_num 1 --dataset 'X4K1000FPS' --module_scale_factor 4 --S_tst 3 --multiple 8 
```
==> It would yield **(PSNR/SSIM/tOF) = (28.86/0.858/2.67)**.

### Description
* After running with the above test option, you can get the result images in **\<source_path\>/test_img_dir/XVFInet_X4K1000FPS_exp1**, then obtain the PSNR/SSIM/tOF results per each test clip as "total_metrics.csv" in the same folder. 
* Our proposed XVFI-Net can start from any downscaled input upward by regulating '--S_tst', which is adjustable in terms of
the number of scales for inference according to the input resolutions or the motion magnitudes.
* You can get any Multi-Frame Interpolation (x M) result by regulating '--multiple'.



### Quick Start for Vimeo90K (as in Fig. 8)
1. Download the source codes in a directory of your choice **\<source_path\>**.
2. First download Vimeo90K dataset from [this link](http://toflow.csail.mit.edu/) (including 'tri_trainlist.txt') to place in **\<source_path\>/vimeo_triplet**.
```
XVFI
└── vimeo_triplet
       ├──  sequences
       readme.txt
       tri_testlist.txt
       tri_trainlist.txt
```
3. Download the pre-trained weights (XVFI-Net_v), which was trained by Vimeo90K, from [this link](https://www.dropbox.com/s/5v4dp81bto4x9xy/XVFInet_Vimeo_exp1_latest.pt?dl=0) to place in **\<source_path\>/checkpoint_dir/XVFInet_Vimeo_exp1**.
```
XVFI
└── checkpoint_dir
   └── XVFInet_Vimeo_exp1
       ├── XVFInet_Vimeo_exp1_latest.pt           
```
4. Run **main.py** with the following options in parse_args: 
```bash
python main.py --gpu 0 --phase 'test' --exp_num 1 --dataset 'Vimeo' --module_scale_factor 2 --S_tst 1
```
==> It would yield **PSNR = 35.07** on Vimeo90K. 

### Description
* After running with the above test option, you can get the result images in **\<source_path\>/test_img_dir/XVFInet_Vimeo_exp1**.
* There are certain code lines in front of the 'def main()' for a convenience when running with the Vimeo option.
* The SSIM result of 0.9760 as in Fig. 8 was measured by matlab [ssim](https://arxiv.org/pdf/2103.12340.pdf) function for a fair comparison after running the above guide because other SOTA methods did so. We also upload "compare_psnr_ssim.m" matlab file to obtain it. 
* It should be noted that there is a typo "S_trn
and S_tst are set to 2" in the current version of XVFI paper, which should be modified to 1 (not 2), sorry for inconvenience.

## Training Code
### Quick Start for X-TRAIN
1. Download the source codes in a directory of your choice **\<source_path\>**.
2. First download our X-TRAIN train/val/test datasets by following the above section 'X4K1000FPS' and place them as belows:
 ```
XVFI
└── X4K1000FPS
       ├──  train
           ├── 002
           ├── ...
           └── 172
       ├──  val
           ├── Type1
           ├── Type2
           ├── Type3
       ├──  test
           ├── Type1
           ├── Type2
           ├── Type3

```
3. Run **main.py** with the following options in parse_args:  
```bash
python main.py --phase 'train' --exp_num 1 --dataset 'X4K1000FPS' --module_scale_factor 4 --S_trn 3 --S_tst 5
```
### Quick Start for Vimeo90K
1. Download the source codes in a directory of your choice **\<source_path\>**.
2. First download Vimeo90K dataset from [this link](http://toflow.csail.mit.edu/) (including 'tri_trainlist.txt') to place in **\<source_path\>/vimeo_triplet**.
```
XVFI
└── vimeo_triplet
       ├──  sequences
       readme.txt
       tri_testlist.txt
       tri_trainlist.txt
```
3. Run **main.py** with the following options in parse_args:  
```bash
python main.py --phase 'train' --exp_num 1 --dataset 'Vimeo' --module_scale_factor 2 --S_trn 1 --S_tst 1
```
### Description
* You can freely regulate other arguments in the parser of **main.py**.


<!-- **Reference**:   -->
## Reference
> Hyeonjun Sim*, Jihyong Oh*, and Munchurl Kim "XVFI: eXtreme Video Frame Interpolation", https://arxiv.org/abs/2103.16206, 2021. (* *equal contribution*)
> 
**BibTeX**
```bibtex
@article{sim2021xvfi,
  title={XVFI: eXtreme Video Frame Interpolation},
  author={Sim, Hyeonjun and Oh, Jihyong and Kim, Munchurl},
  journal={arXiv preprint arXiv:2103.16206},
  year={2021}
}
```



## Contact
If you have any question, please send an email to either flhy5836@kaist.ac.kr or jhoh94@kaist.ac.kr.


