## Installing CUDA 10.0 and cuDNN 7.6.0.64 along with the GPU version of tensorflow 1.12 on ubuntu 18.04

This will show you how to install tensorflow with NVIDIA's Cuda 10.0 GPU environment on Ubuntu 18.04. It will install all > required packages and then compile Tensorflow from source. 

These steps also work with a completely fresh Ubuntu 18.04 install.

###  Installing NVIDIA video driver and Cuda 10.0

Always first update to the latest versions of your packages:


<pre>sudo apt update
sudo apt upgrade
sudo apt install build-essential
</pre>

###  First uninstall Nouveau video driver:

Create a blacklist configuration file with:

<pre>sudo nano /etc/modprobe.d/blacklist-nouveau.conf</pre>

with the following contents:

<pre>blacklist nouveau
options nouveau modeset=0</pre>

Regenerate the kernel with initramfs:

<pre>sudo update-initramfs -u</pre>

and finally reboot machine:

<pre>sudo reboot</pre>

###  Installing Nvidia Driver
Download Nvidia Driver install files,download address [NVIDIA DRIVERS Linux x64 ](https://www.nvidia.com/Download/driverResults.aspx/138959/en-us)
Open the terminal and change directories using cd to the directory containing the NVIDIA-Linux-x86_64-410.66.run file that you downloaded.

Install NVIDIA Linux x64 Display Driver version 410.6.
<pre>sudo chmod +x NVIDIA-Linux-x86_64-410.66.run  
sudo ./NVIDIA-Linux-x86_64-410.66.run  
sudo reboot</pre>  
 
###  Install NVIDIA CUDA 10.0
Download CUDA install files,download address [CUDA Toolkit 10.0 Download](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=runfilelocal)


excute the installation:

<pre>sudo ./cuda_10.0.130_410.48_linux.run
echo "/usr/local/cuda/lib64" | sudo tee -a /etc/ld.so.conf
echo "/usr/local/cuda-10.0/extras/CUPTI/lib64" | sudo tee -a /etc/ld.so.conf
sudo ldconfig
</pre>

Reboot the system to load the NVIDIA drivers.

###  Install nccl
<pre>sudo tar -xf  nccl_2.4.7-1+cuda10.0_x86_64.txz 
cd nccl_2.4.7-1+cuda10.0_x86_64/
sudo mkdir -p /usr/local/nccl-2.4.7
sudo cp -vRf * /usr/local/nccl-2.4.7
</pre>

###  Install cuDNN
Download at: (https://developer.nvidia.com/rdp/cudnn-download) (login required)

Select cuDNN for CUDA 10.0 -> Download latest cuDNN Runtime Library, cuDNN Developer Library, cuDNN Code Samples and User Guide

And Install

<pre>sudo dpkg -i libcudnn7_7.6.0.64-1+cuda10.0_amd64.deb
sudo dpkg -i libcudnn7-dev_7.6.0.64-1+cuda10.0_amd64.deb
sudo dpkg -i libcudnn7-doc_7.6.0.64-1+cuda10.0_amd64.deb</pre>

###  Building Tensorflow from source
####  Installing Bazel before compiling Tensorflow

To build Tensorflow you will first need Bazel. The easiest way to install, is to add the Bazel repo and install the package.
<pre>
sudo apt --yes install curl
curl -O http://storage.googleapis.com/bazel-apt/pool/jdk1.8/b/bazel/bazel_0.18.0_amd64.deb
sudo apt-get -y --no-install-recommends install \g++    pkg-config    python    unzip    zip    zlib1g-dev
sudo dpkg -i bazel_0.18.0_amd64.deb
</pre>
<pre>
sudo apt install python-dev python-pip
sudo -H pip install -U pip six numpy wheel mock
sudo -H pip install -U keras_applications==1.0.5 --no-deps
sudo -H pip install -U keras_preprocessing==1.0.3 --no-deps
sudo pip install matplotlib
sudo pip install numpy
sudo apt-get install python-tk
</pre>
<pre>
sudo apt --yes install git
git clone https://github.com/tensorflow/tensorflow.git
cd tensorflow
git checkout r1.12
./configure
</pre>
Don't forget to type Y for for Cuda, Cuda version 10.0 and CuDNN version 7.6.0.64 and CuDNN library location /usr/lib. For all others you can leave the default values.
<pre>
bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package
</pre>
Once you have built Tensorflow, you will need to build the Python package and install it:
<pre>
./bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
sudo -H pip install /tmp/tensorflow_pkg/tensorflow*.whl
</pre>





</pre>
