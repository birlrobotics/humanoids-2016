# Efficient Reinforcement Learning for Humanoid Whole-Body Control

#### Ryan Lober, Olivier Sigaud, Vincent Padois


### Abstract

Whole-body control of humanoid robots permits the execution of multiple simultaneous tasks but combining tasks can often result in unexpected overall behaviors. These discrepancies arise from a variety of internal and external factors and modeling them explicitly would be impractical.Reinforcement learning can be used to eliminate the effects of the deleterious factors through trial and error but generally requires many trials to converge on a solution. In humanoid robotics such improvidence can be costly. In this paper we show how the efficiency of the learning can be improved through use of Bayesian optimization. This is accomplished by intelligently exploring a model of the latent cost function derived from the quality of the task executions. We demonstrate the efficacy of the technique through two different simulated scenarios where various factors impede the robot from accomplishing its objectives.

## Overview

This is a sort of pseudo-repo which provides the instructions for downloading, compiling, and running the scenarios presented in our submission for the 2016 IEEE-RAS International Conference on Humanoid Robots.

**WARNING**

The code needed to run these examples is experimental and may cause system errors/conflicts. We have done our best to render the code stable but make NO guarantee of its reliability and take NO responsibility for any damage it may cause to your machine. If you know what you are doing then everything should be fine. Additionally, these examples have only been tested on Ubuntu 16/14.04, and OSX. Windows remains to be tested.  

## Getting the Code

The examples shown in the article rely on many different libraries and the easiest way to install everything is to use the [`codyco-superbuild`](https://github.com/robotology/codyco-superbuild) tools.

### 1. Installing the dependencies

These dependencies are solid widely used libs which you can confidently install on your system. It is recommended that you use the precompiled installation methods. If you decide to compile from sources then you will need to update your environment variables accordingly. See [Set up environment variables](#env-vars) for an example.

#### Ubuntu

Install [`yarp`](http://www.yarp.it/) and [`icub-main`](http://wiki.icub.org/iCub/main/dox/html/index.html) from apt repositories:

http://wiki.icub.org/wiki/Linux:Installation_from_binaries


Alternatively, you can install from sources:

http://wiki.icub.org/wiki/Linux:Installation_from_sources


Install the [`codyco-superbuild`](https://github.com/robotology/codyco-superbuild) dependencies:

https://github.com/robotology/codyco-superbuild#system-dependencies






#### OSX

Install [`yarp`](http://www.yarp.it/) and [`icub-main`](http://wiki.icub.org/iCub/main/dox/html/index.html) via [`Homebrew`](http://brew.sh/):

http://wiki.icub.org/wiki/MacOSX:_installation


Install the [`codyco-superbuild`](https://github.com/robotology/codyco-superbuild) dependencies:

https://github.com/robotology/codyco-superbuild#system-dependencies-1


### 2. Set up environment variables  <a id="env-vars"></a>

First we are going to create a local folder where everything goes and is installed. In this way we will minimize the impact on your OS.

We first create the directory where all the code will be stored and the installation directory.

```shell
mkdir -p ~/humanoids-2016-repos/install
```

Now we need to point our enviroment variables to the installation directory so that we can access the compiled code from other libs and binaries.

#### Ubuntu

At the bottom of your `~/.bashrc` file add the following:

```shell
export HUMANOIDS_2016=~/humanoids-2016-repos
export PATH=$PATH:$HUMANOIDS_2016/install/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HUMANOIDS_2016/install/libs
# For codyco-superbuild...
export CODYCO_SUPERBUILD_ROOT=$HUMANOIDS_2016/codyco-superbuild
export PATH=$PATH:$CODYCO_SUPERBUILD_ROOT/build/install/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$CODYCO_SUPERBUILD_ROOT/build/install/lib
export YARP_DATA_DIRS=$YARP_DATA_DIRS:$CODYCO_SUPERBUILD_ROOT/build/install/share/codyco
```

Now open a terminal and run:
```shell
source ~/.bashrc
```
You will probably get some errors because the directories don't exist yet but don't worry.

#### OSX

At the bottom of your `~/.bash_profile` file (might be `~/.bashrc`) add the following:

```shell
export HUMANOIDS_2016=~/humanoids-2016-repos
export PATH=$PATH:$HUMANOIDS_2016/install/bin
export DYLD_LIBRARY_PATH=$DYLD_LIBRARY_PATH:$HUMANOIDS_2016/install/libs
# For codyco-superbuild...
export CODYCO_SUPERBUILD_ROOT=$HUMANOIDS_2016/codyco-superbuild
export PATH=$PATH:$CODYCO_SUPERBUILD_ROOT/build/install/bin
export DYLD_LIBRARY_PATH=$DYLD_LIBRARY_PATH:$CODYCO_SUPERBUILD_ROOT/build/install/lib
export YARP_DATA_DIRS=$YARP_DATA_DIRS:$CODYCO_SUPERBUILD_ROOT/build/install/share/codyco
```

Now open a terminal and run:
```shell
source ~/.bash_profile
```
You will probably get some errors because the directories don't exist yet but don't worry.

### 3. Get SMLT

SMLT, or Stochastic Machine Learning Toolbox, is used for the Bayesian optimization.

```shell
cd $HUMANOIDS_2016
git clone https://github.com/rlober/smlt.git
cd smlt
mkdir build
cd build
cmake .. -DCMAKE_INSTALL_PREFIX=$HUMANOIDS_2016/install
make install
```

### 4. Get codyco-superbuild

Now we are going to build codyco-superbuild:
```shell
cd $HUMANOIDS_2016
git clone https://github.com/robotology/codyco-superbuild.git
cd codyco-superbuild
mkdir build
cd build
cmake .. -DCODYCO_BUILD_OCRA_MODULES=TRUE
make -j$(nproc)
```
At this point if you run `source ~/.bashrc`(Ubuntu) or `source ~/.bash_profile`(OSX) you shouldn't get any errors.

The line `make -j$(nproc)` simply multi-threads the compilation process making it faster. Assuming everything went according to plan we now have to do a little trickiness to get the right branch of the underlying Whole-Body controller.

First we will delete everything we just built... :sad: I know.

```shell
cd $CODYCO_SUPERBUILD_ROOT/build
rm -rf *
```
Now let's checkout the `humanoids_2016_demo` branches.
```shell
cd $CODYCO_SUPERBUILD_ROOT/libraries/ocra-recipes
git checkout -b humanoids_2016_demo origin/humanoids_2016_demo
cd $CODYCO_SUPERBUILD_ROOT/main/ocra-recipes
git checkout -b humanoids_2016_demo origin/humanoids_2016_demo
```
And we can rebuild.
```shell
cd $CODYCO_SUPERBUILD_ROOT/build
make -j$(nproc)
```




### 4. Get simulation tools

#### [Gazebo 7](http://gazebosim.org/)
You need Gazebo 7.0 or greater to run the simulations. You can find the download instructions here:

http://gazebosim.org/download

#### [`icub-gazebo`](https://github.com/robotology/icub-gazebo)
The iCub simulation files and worlds are all here. All we have to do is download the files and extend some paths.

```shell
cd $HUMANOIDS_2016
git clone https://github.com/robotology/icub-gazebo.git
```

At the bottom of your `~/.bashrc` (Ubuntu) or `~/.bash_profile`(OSX) file add the following:

```shell
if [ -z "$GAZEBO_MODEL_PATH" ]; then
    export GAZEBO_MODEL_PATH=$HUMANOIDS_2016/icub-gazebo
else
    export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:$HUMANOIDS_2016/icub-gazebo
fi
```

#### [`humanoids-2016-models`](https://github.com/rlober/humanoids-2016-models)

To run the article simulation scenarios we need some custom models.

```shell
cd $HUMANOIDS_2016
git clone https://github.com/rlober/humanoids-2016-models.git
```

Again we need to extended the model path in the `~/.bashrc` (Ubuntu) or `~/.bash_profile`(OSX) file. Add the following to the end of your file:

```shell
export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:$HUMANOIDS_2016/humanoids-2016-models
```

#### [`gazebo-yarp-plugins`](https://github.com/robotology/gazebo-yarp-plugins)

To enable the iCub/YARP software to control the iCub models in gazebo we need to download the plugins.

```shell
cd $HUMANOIDS_2016
git clone https://github.com/robotology/gazebo-yarp-plugins.git
cd gazebo-yarp-plugins
mkdir build
cd build
cmake .. -DCMAKE_INSTALL_PREFIX=$HUMANOIDS_2016/install
make install
```


#### [`humanoids-2016-plugins`](https://github.com/rlober/humanoids-2016-plugins)

These gazebo plugins will allow the specific scenario simulations to function properly.

```shell
cd $HUMANOIDS_2016
git clone https://github.com/rlober/humanoids-2016-plugins.git
cd humanoids-2016-plugins
mkdir build
cd build
cmake .. -DCMAKE_INSTALL_PREFIX=$HUMANOIDS_2016/install
make install
```

At the bottom of your `~/.bashrc` (Ubuntu) or `~/.bash_profile`(OSX) file add the following:

```shell
export GAZEBO_PLUGIN_PATH=$GAZEBO_PLUGIN_PATH:$HUMANOIDS_2016/install/lib
```

Now open a terminal and run:
```shell
source ~/.bashrc
```
or on OSX:

```shell
source ~/.bash_profile
```


### 4. Get the [`taskOptimizer`](https://github.com/rlober/taskOptimizer)

This is a binary which you can run to optimize any generic task. Here is where the Bayesian optimization occurs.


```shell
cd $HUMANOIDS_2016
git clone https://github.com/rlober/taskOptimizer.git
cd taskOptimizer
mkdir build
cd build
cmake .. -DCMAKE_INSTALL_PREFIX=$HUMANOIDS_2016/install
make install
```

## Running the examples

### Obstacle avoidance

**Terminal 1**
```shell
yarpserver
```

**Terminal 2**
```shell
gazebo $HUMANOIDS_2016/humanoids-2016-models/obstacle_avoidance.world
```

**Terminal 3**
```shell
taskOptimizer
```

**Terminal 4**
```shell
wocraController --sequence ObstacleAvoidance
```


### Move a Heavy Weight

**Terminal 1**
```shell
yarpserver
```

**Terminal 2**
```shell
gazebo $HUMANOIDS_2016/humanoids-2016-models/move_weight.world
```

**Terminal 3**
```shell
taskOptimizer
```

**Terminal 4**
```shell
wocraController --floatingBase --sequence MoveWeight
```
