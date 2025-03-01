# GCNv2_SLAM

This is a refactoring of the GCNv2_SLAM repository. It uses up-to-date cmake, up-to-date DBoW2 and g2o libraries, supports all static and all dynamic libraries (for debugging) and 2011 era C++ for sleep, threads, namespaces, etc. It also allows direct switching between ORB and GCN feature detectors in mono, stereo and RGB-D examples.

You should be able to build by running:

./Build.sh <BUILD_TYPE>

BUILD_TYPE can be Debug or Release (default).

## Installation

The code is very particular about the versions of CUDA, Python, PyTorch and cuDNN. Other versions will not work for various cryptic and undecipherable reasons. The requirements are:

* CUDA 9.2
* Python 3.7
* PyTorch 1.0.1
* cuDNN 7

The other dependencies (DBoW2, DLib, g2o, Pangolin) are included as part of the source tree. For simplicity the source code is directly included and not linked as a sub-module.

The libraries are all built statically by default to simplify debugging / linking / running.

## Difference on execution against original GCNv2_SLAM:

1.  All executables are installed in `./Install/bin` and a suffix is used to denote debug builds.

   For instance, if you want to run mono_kitti with the default Build.sh file setting, the executable is located at `./Install/bin/mono_kitti_d`

2. The standard vocabulary (ORB or GCN, depending upon feature type) is loaded by default and does not have to be specified on the command line. (It is installed in `./Install/var/lib/gcnv2_slam` subdirectory). The system will try to load the binary version of the vocabulary.

3. The standard settings files are installed in `./Install/etc/gcnv2_slam/`. You do not need to specify the directory. Therefore, a example command for running TUM1 (mono) is:
 
 `./Install/bin/mono_tum TUM1.yaml ${your_tum_dataset_folder}/rgbd_dataset_freiburg1_xyz`

On linux it is highly recommend you get used to using valgrind. See https://www.valgrind.org/docs/manual/quick-start.html

The original GCNv2 SLAM README is below.

# GCNv2 SLAM

## Introduction
GCNv2 is a high-throughput variant of the Geometric Correspondence Network for performing RGB-D SLAM online on embedded platforms. We trained the binary descriptor in the same format with ORB (32 bytes) for the convenience of integration. In this implementation, we evaluate the motion estimation using a system built on top the [ORB-SLAM2], (https://github.com/raulmur/ORB_SLAM2). Thanks to the robustness of ORB-SLAM2, our system is able to achive reliable tracking perfomance on our drone platform in real-time. 

## Example
Online running performance with ORB and GCNv2 features:

ORB:

![](orb.gif)

GCNv2:

![](gcn.gif)

## Related Publications

* **[GCNv2: Efficient Correspondence Prediction for Real-Time SLAM](https://arxiv.org/pdf/1902.11046.pdf)**, *J. Tang, L. Ericson, J. Folkesson and P. Jensfelt*, in arXiv:1902.11046, 2019
* **[Geometric Correspondence Network for Camera Motion Estimation](http://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8260906&isnumber=8214927)**, *J. Tang, J. Folkesson and P. Jensfelt*, RA-L and ICRA 2018

# Dependencies

## C++11 or C++0x Compiler
We use the new thread and chrono functionalities of C++11.

## Pytorch
We use [Pytorch](https://github.com/pytorch/pytorch) C++ api(libtorch) for deloying the GCNv2. 
The libtorch can be built as follows:
```
git clone --recursive -b v1.0.1 https://github.com/pytorch/pytorch
cd pytorch && mkdir build && cd build
python ../tools/build_libtorch.py
```
The built libtorch library is located at ```pytorch/torch/lib/tmp_install/``` in default.

**Update: Have added support for master branch of pytorch or version larger than 1.0.1. For newer version, set ```TORCH_PATH``` to ```pytorch/torch/share/cmake/Torch```**

**Required at least 1.0.1. Lower version of pytorch has cuDNN linking issue:https://github.com/pytorch/pytorch/issues/14033#issuecomment-455046353.**

**Plese avoid using the pre-built version of libtorch since it will cause linking errors (due to [CXX11 ABI issue](https://github.com/pytorch/pytorch/issues/13541)).**

## Pangolin
We use [Pangolin](https://github.com/stevenlovegrove/Pangolin) for visualization and user interface. Dowload and install instructions can be found at: https://github.com/stevenlovegrove/Pangolin.

## OpenCV
We use [OpenCV](http://opencv.org) to manipulate images and features. Dowload and install instructions can be found at: http://opencv.org. 

**Required at least 2.4.3. Tested with OpenCV 2.4.11 and OpenCV 3.2**.

## Eigen3
Required by g2o (see below). Download and install instructions can be found at: http://eigen.tuxfamily.org. 

**Required at least 3.1.0**.

## DBoW2 and g2o (Included in Thirdparty folder)
We use modified versions of the [DBoW2](https://github.com/dorian3d/DBoW2) library to perform place recognition and [g2o](https://github.com/RainerKuemmerle/g2o) library to perform non-linear optimizations. Both modified libraries (which are BSD) are included in the *Thirdparty* folder.

# Preparation
Clone the code
```
git clone https://github.com/jiexiong2016/GCNv2_SLAM.git
```
Then build the project 
```
cd GCNv2_SLAM
./build.sh
```
Make sure to edit `build.sh` pointing to your local libtorch installation. Edit `run.sh` to check out how to run with GCNv2 or vanilla ORB. Check the `Network.md` for the network structure and [link](https://drive.google.com/file/d/1MJMroL5-tl0b9__-OiCfxFP9K6X8kvTT/view) for trained models.

# Image resolution
**Update** Set "FULL_RESOLUTION=1" and use "gcn2_640x480.pt" to test with image resolution "640x480" intead. The input image size should be consitent with the model to be used.

# Demonstration video

[![YouTube video thumbnail](https://i.ytimg.com/vi/pz-gdnR9tAM/hqdefault.jpg)](https://www.youtube.com/watch?v=pz-gdnR9tAM)
