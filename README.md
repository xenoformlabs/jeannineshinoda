Jeannine Shinoda Stopover Residency
=====================================

## Project Description
Speculative Gastronomy uses food, the language of food, and machine learning as a vehicle to investigate creativity, inspiration, technology, ethics, politics, bias, and culture.

### Pushing changes
Open Terminal Windows
cd into your directory [drag from desktop, etc]
git add .   [this will add all files into repo] 
git commit -m "commit message"
git push -u origin master

### Pulling changes
git pull origin master

## uploading output data
after sampling uploading the data from our training

### file format 

files are named like this:

[checkpoint directory]-cp-[checkpointnum]-<versionnum>.txt

eg. solo_cookbooks-cp6000-01.txt

## INSTALLATION OF TORCH-RNN ON MAC OS

### General Tutorial

We followed these diretions from Jeffrey Thompson for Mac OS X to install torch-rnn on the command line (Terminal):

http://www.jeffreythompson.org/blog/2016/03/25/torch-rnn-mac-install/

If you get stuck, you can use Docker, which is less efficient and creates a box for the Machine Learning Code
Link to tutorial: https://www.literai.com/tutorial/


###Fixes for the Mac OS X install via JT's tutorial


#### prep
python scripts/preprocess.py --input_txt data/tiny-shakespeare.txt --output_h5 data/tiny_shakespeare.h5 --output_json data/tiny_shakespeare.json


This is the training command without the GPU

#### train
th train.lua -input_h5 data/tiny_shakespeare.h5 -input_json data/tiny_shakespeare.json -gpu -1

	
#### output
 th sample.lua -checkpoint cv/checkpoint_10000.t7 -length 2000 -gpu -1

##### checkpoints
the deeper the checkpoint

(1)
CHANGED congif.lua

from
hdf5._config = {
    HDF5_INCLUDE_PATH = "/usr/local/Cellar/hdf5/1.10.4/include;/usr/local/opt/szip/include",
    HDF5_LIBRARIES = "/usr/local/Cellar/hdf5/1.10.4/lib/libhdf5.dylib;/usr/local/opt/szip/lib/libsz.dylib;/usr/lib/libz.dylib;/usr/lib/libdl.dylib;/usr/lib/libm.dylib"
}


to

hdf5._config = {
    HDF5_INCLUDE_PATH = "/usr/local/include/",
    HDF5_LIBRARIES = "/usr/local/Cellar/hdf5/1.10.4/lib/libhdf5.dylib;/usr/local/opt/szip/lib/libsz.dylib;/usr/lib/libz.dylib;/usr/lib/libdl.dylib;/usr/lib/libm.dylib"
}


(2)
in ffi.lua

using recommendation here: https://github.com/karpathy/neuraltalk2/issues/149
Changed line 44 

local process = io.popen("gcc -E " .. headerPath) -- TODO pass -I


local process = io.popen("gcc -D '_Nullable=' -E " .. headerPath) -- TODO pass -I

(3)
in ffi.lua
Changed line 72

if maj[0] ~= 1 or min[0] ~= 8 then

to
if maj[0] ~= 1 or min[0] ~= 10 then


(4)
/Users/username/torch/install/bin/luajit: ...s/username/torch/install/share/lua/5.1/hdf5/file.lua:10: HDF5File.__init() requires a fileID - perhaps you want HDF5File.create()?
stack traceback:
	[C]: in function 'assert'
	...s/username/torch/install/share/lua/5.1/hdf5/file.lua:10: in function '__init'
	.../username/torch/install/share/lua/5.1/torch/init.lua:91: in function <.../username/torch/install/share/lua/5.1/torch/init.lua:87>
	[C]: in function 'open'
	./util/DataLoader.lua:17: in function '__init'
	.../username/torch/install/share/lua/5.1/torch/init.lua:91: in function <.../username/torch/install/share/lua/5.1/torch/init.lua:87>
	[C]: in function 'DataLoader'
	train.lua:76: in main chunk
	[C]: in function 'dofile'
	...dall/torch/install/lib/luarocks/rocks/trepl/scm-1/bin/th:150: in main chunk
	[C]: at 0x0105e11350


Refer to:
https://github.com/deepmind/torch-hdf5/issues/81

In file.lua, line 147

Added this:
local fileID = hdf5.C.H5Fopen(filename, access, hdf5.H5P_DEFAULT)
fileID = tonumber(fileID)


(5) Then. this error

/Users/username/torch/install/bin/luajit: .../username/torch/install/share/lua/5.1/hdf5/group.lua:88: attempt to concatenate 'int64_t' and 'string'
stack traceback:
	.../username/torch/install/share/lua/5.1/hdf5/group.lua:88: in function 'tostring'
	.../username/torch/install/share/lua/5.1/hdf5/group.lua:39: in function '__init'
	.../username/torch/install/share/lua/5.1/torch/init.lua:91: in function <.../username/torch/install/share/lua/5.1/torch/init.lua:87>
	[C]: in function 'HDF5Group'
	...s/username/torch/install/share/lua/5.1/hdf5/init.lua:74: in function '_loadObject'
	...s/username/torch/install/share/lua/5.1/hdf5/file.lua:19: in function '__init'
	.../username/torch/install/share/lua/5.1/torch/init.lua:91: in function <.../username/torch/install/share/lua/5.1/torch/init.lua:87>
	[C]: in function 'open'
	./util/DataLoader.lua:17: in function '__init'
	.../username/torch/install/share/lua/5.1/torch/init.lua:91: in function <.../username/torch/install/share/lua/5.1/torch/init.lua:87>
	[C]: in function 'DataLoader'
	train.lua:76: in main chunk
	[C]: in function 'dofile'
	...dall/torch/install/lib/luarocks/rocks/trepl/scm-1/bin/th:150: in main chunk
	[C]: at 0x0109bf7350


In group.lua, change line 88
Change:
  return "[HDF5Group " .. self._groupID .. " " .. hdf5._getObjectName(self._groupID) .. "]"

To this:
   return "[HDF5Group " .. tostring(self._groupID) .. " " .. hdf5._getObjectName(self._groupID) .. "]"





## Copyright and License

Copyright (c) 2017-2018, Xenoform Labs LLC

Licensed under Creative Commons: Attribution-NonCommercial-ShareAlike

CC BY-NC-SA

