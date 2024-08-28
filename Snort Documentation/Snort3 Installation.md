### This Snort 3 installation guide references Snort's official documentation. For a comprehensive overview, please refer to their offical website. 
https://www.snort.org/documents <br>
https://snort-org-site.s3.amazonaws.com/production/document_files/files/000/012/147/original/Snort_3.1.8.0_on_Ubuntu_18_and_20.pdf?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAU7AK5ITMMOXGB2W5%2F20240828%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240828T101155Z&X-Amz-Expires=172800&X-Amz-SignedHeaders=host&X-Amz-Signature=584fd1c4f7bc141a9b3e434b6308bd8136c6efef2ce4e7c499af4578891a8035

## Installation

Before we start installing Snort, make sure we update and upgrade our repository by running:
```
apt get update && apt get upgrade -y
```

Make sure your system has the correct time and the correct time zone. (This is important for later ingesting alerts into Splunk.)
```
sudo dpkg-reconfigure tzdata
```
![image01](https://github.com/user-attachments/assets/d8c3b223-bd4f-453d-92e0-d4a414702c64)


After updating and upgrading our repository, create a Snort directory to install prerequisites:
```
mkdir ~/snort_src 
cd ~/snort_src
```
![image02](https://github.com/user-attachments/assets/a35e83ad-16ee-435b-9770-29e2dc94a0b9)

## Step 0: Install the Snort 3 prerequisites
```
sudo apt-get install -y build-essential autotools-dev libdumbnet-dev libluajit-5.1-dev libpcap-dev \ zlib1g-dev pkg-config libhwloc-dev cmake liblzma-dev openssl libssl-dev cpputest libsqlite3-dev \ libtool uuid-dev git autoconf bison flex libcmocka-dev libnetfilter-queue-dev libunwind-dev \ libmnl-dev ethtool libjemalloc-dev
```
![image03](https://github.com/user-attachments/assets/2519a7fd-84fe-4cb2-87e7-b831ae6e4a1a)

### Step 1: Download and Install **safec** (for runtime bounds checks on certain legacy C-library calls)
```
wget https://github.com/rurban/safeclib/releases/download/v02092020/libsafec-02092020.tar.gz 

tar -xzvf libsafec-02092020.tar.gz 

cd libsafec-02092020.0-g6d921f

./configure

make 

sudo make install
```
![image04](https://github.com/user-attachments/assets/895b0ea2-d7ce-4a77-9597-e4d448089f61)

![image05](https://github.com/user-attachments/assets/85fe8da6-591a-439a-afd9-f45a0f5beced)

![image06](https://github.com/user-attachments/assets/cf9c6e5c-7838-4c0b-a421-ec7fa8dceaa2)

![image07](https://github.com/user-attachments/assets/1f44fb2c-d1c3-4e80-b6fe-61d2ada569ac)

![image08](https://github.com/user-attachments/assets/8126f42e-740f-4a86-b653-7e9cbf3e3fba)


### Step 2: Install **pcre** (Pearl Compatible Regular Expression)
```
cd ~/snort_src/ 

wget https://sourceforge.net/projects/pcre/files/pcre/8.45/pcre-8.45.tar.gz 

tar -xzvf pcre-8.45.tar.gz 

cd pcre-8.45

./configure 

make 

sudo make install
```

### Step 3: Download and install gperftools 2.9
```
cd ~/snort_src 

wget https://github.com/gperftools/gperftools/releases/download/gperftools-2.9.1/gperftools-2.9.1.tar.gz 

tar xzvf gperftools-2.9.1.tar.gz

cd gperftools-2.9.1 

./configure 

make 

sudo make install
```

### Step 4: Download and install Ragel

```
cd ~/snort_src

wget http://www.colm.net/files/ragel/ragel-6.10.tar.gz

tar -xzvf ragel-6.10.tar.gz

cd ragel-6.10 

./configure 

make 

sudo make install
```

### Step 5: Download (but don’t install) the Boost C++ Libraries
```
cd ~/snort_src 

wget https://boostorg.jfrog.io/artifactory/main/release/1.77.0/source/boost_1_77_0.tar.gz 

tar -xvzf boost_1_77_0.tar.gz
```

### Step 6: Install Hyperscan 5.4 from source, referencing the location of the Boost source directory
```
cd ~/snort_src
wget https://github.com/intel/hyperscan/archive/refs/tags/v5.4.0.tar.gz 
tar -xvzf v5.4.0.tar.gz 

mkdir ~/snort_src/hyperscan-5.4.0-build 
cd hyperscan-5.4.0-build/ 

cmake -DCMAKE_INSTALL_PREFIX=/usr/local -DBOOST_ROOT=~/snort_src/boost_1_77_0/ ../hyperscan-5.4.0 

make 
sudo make install
```

### Step 7: Install flatbuffers
```
cd ~/snort_src 
wget https://github.com/google/flatbuffers/archive/refs/tags/v2.0.0.tar.gz -O flatbuffers-v2.0.0.tar.gz 
tar -xzvf flatbuffers-v2.0.0.tar.gz 

mkdir flatbuffers-build 
cd flatbuffers-build 

cmake ../flatbuffers-2.0.0 

make 
sudo make install
```

### Step 8: Download and install latest Data Acquisition library (DAQ) from the Snort website
```
cd ~/snort_src
wget https://api.github.com/repos/snort3/libdaq/tarball/v3.0.16 -O libdaq-3.0.16.tar.gz

mkdir libdaq-3.0.16
tar -xzvf libdaq-3.0.16.tar.gz -C ~/snort_src/libdaq-3.0.16

cd libdaq-3.0.16/snort3-libdaq-2ffe084

./bootstrap 
./configure 

make
sudo make install
```

### Step 9: Update shared libraries
```
sudo ldconfig
```
Now we are ready to download, compile, and install latest version of Snort 3 from the snort website.

## Download and install snort, with default settings
```
cd ~/snort_src 
wget https://api.github.com/repos/snort3/snort3/tarball/3.3.4.0 -O snort3-3.3.4.0.tar.gz 
```
![image09](https://github.com/user-attachments/assets/57f8ac79-b446-4273-8b61-381f7d4a4c28)


```
mkdir snort3-3.3.4.0
tar -xzvf snort3-3.3.4.0.tar.gz -C  ~/snort_src/snort3-3.3.4.0
```
![image10](https://github.com/user-attachments/assets/c97afcba-ea5c-4c98-a4c5-68818f8623e6)

```
cd snort3-3.3.4.0/snort3-snort3-5598e8d
./configure_cmake.sh
```
![image11](https://github.com/user-attachments/assets/f98b5b15-2679-49c7-9977-c6abad677955)


```
cd build 
make 
```
![image12](https://github.com/user-attachments/assets/a386a3d4-d71a-4f24-9ab9-c9760dd7008a)

```
sudo make install
```
![image13](https://github.com/user-attachments/assets/262564f7-dec0-4e53-b828-e53fe9412257)

### Verify Snort installation (Snort is installed under /usr/local/)
![image14](https://github.com/user-attachments/assets/052c2b16-365b-4999-95d1-ad039ddd10ef)

### Verify Snort running correctly or not
```
cd /usr/local/snort/bin
./snort -V
```
![image15](https://github.com/user-attachments/assets/d6ca6524-11de-4de5-bae2-3d31151e57ee)

### Test Snort with the default configuration file
```
snort -c /usr/local/snort/etc/snort/snort.lua
```
![image16](https://github.com/user-attachments/assets/98eebab7-a17f-40ae-a832-12c2b473492b)
![image17](https://github.com/user-attachments/assets/dacfadab-acb8-4122-a734-838ef11fd9c0)

**Snort is finally installed and working!**

