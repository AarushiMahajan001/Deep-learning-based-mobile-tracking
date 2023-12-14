# Deep-learning-based-mobile-tracking
ECE 535 Project
TartanVO is a learning-based visual odometry trained on TartanAir dataset. It generalizes to multiple datasets and real-world scenarios, and outperforms geometry-based methods in challenging scenes. You can check the python3 branch if you are using python3.
Running without docker
This repo has the following dependencies:

Python 2 / 3
numpy
matplotlib
scipy
pytorch >= 1.3
opencv-python
cupy
You can install the above dependencies manually, or use the following command:

$ pip install numpy matplotlib scipy torch==1.4.0 opencv-python==4.2.0.32 cupy==6.7.0
Our code has been tested on Ubuntu 18.04 and 16.04. An nvidia-driver and a Cuda version of 9.2/10.2 are required to run the code.

Testing with a pretrained model
Download the pretrained model
$ mkdir models
$ wget https://cmu.box.com/shared/static/t1a5u4x6dxohl89104dyrsiz42mvq2sz.pkl -O models/tartanvo_1914.pkl
Download the testing data
Download KITTI-10 testing trajectory
$ mkdir data
$ wget https://cmu.box.com/shared/static/nw3bi7x5vng2xy296ndxt19uozpk64jq.zip -O data/KITTI_10.zip
$ unzip -q data/KITTI_10.zip -d data/KITTI_10/
You can also download other trajectories from the KITTI website. First, each trajectory may have different intrinsics, make sure you change the values in Datasets/utils.py. Second, we are using the same format of pose file with the TartanAir dataset. You can use the Datasets/transformation/kitti2tartan() to convert the KITTI pose files.

Download EuRoC-V102 testing trajectory
$ mkdir data
$ wget https://cmu.box.com/shared/static/1ctocidptdv1xev6pjxdj0b782mrstps.zip -O data/EuRoC_V102.zip
$ unzip -q data/EuRoC_V102.zip -d data/EuRoC_V102/
You can download other trajectories from the EuRoC dataset. What we did for the sample data EuRoC_V102 was the following:

Undistort the image according to their calibration results.
Match the images with the GT poses according to the timestamp.
Change the intrinsics parameters in the code.
Note the poses outputed by TartanVO are in the NED frame.

Alternative download links:
In case using the above links is slow, please try the following links from Azure. You can replace the links with the following ones. Instead of wget, using the azcopy tool usually is much faster.

Model: https://tartanair.blob.core.windows.net/tartanvo/models/tartanvo_1914.pkl
KITTI-10: https://tartanair.blob.core.windows.net/tartanvo/data/KITTI_10.zip
EuRoC-V102: https://tartanair.blob.core.windows.net/tartanvo/data/EuRoC_V102.zip
Try Baidu cloud or Google drive if neither the Box nor the Azure works.

Baidu cloud: https://pan.baidu.com/s/1votDi0zbDXOKK4-4gtgczQ Code: vbtb
Google drive: https://drive.google.com/drive/folders/1kzj2ZR4AxnDyR8elFhfnS2yX1wIxQaZO?usp=sharing
Run the testing script
The vo_trajectory_from_folder.py script shows an example of running TartanVO on a sequence of images out of a folder. Because TartanVO outputs up-to-scale translation, the script also reads a pose file for adjusting the translation scale. If the pose file is not provided, the default scale of 1.0 will be used. The results are stored in the results folder.

Testing on KITTI
$ python vo_trajectory_from_folder.py  --model-name tartanvo_1914.pkl \
                                       --kitti \
                                       --batch-size 1 --worker-num 1 \
                                       --test-dir data/KITTI_10/image_left \
                                       --pose-file data/KITTI_10/pose_left.txt 
Testing on EuRoC

$ python vo_trajectory_from_folder.py  --model-name tartanvo_1914.pkl \
                                       --euroc \
                                       --batch-size 1 --worker-num 1 \
                                       --test-dir data/EuRoC_V102/image_left \
                                       --pose-file data/EuRoC_V102/pose_left.txt
Running the above commands with the --save-flow tag, allows you to save intermediate optical flow outputs into the results folder.

Adjust the batch size and the worker number by --batch-size 10, --worker-num 5.
