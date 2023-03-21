# sjtu-openmmlab-tutorial

This repository maintains codes of OpenMMLab tutorial delivered in SJTU. 
Tutorial codes of OpenMMLab version 1.0 and 2.0 are organized in `v1.0` and `v2.0` respectively. 

For a full list of OpenMMLab courses and tutorials, see [OpenMMLabCourse](https://github.com/open-mmlab/OpenMMLabCourse) (in Chinese). 

## Notes on SJTU Cluster

### For OpenMMLab v2.0

See [this](v2.0/1-SetupEnvironment.md)

### For OpenMMLab v1.0

1. To activate conda environment in **terminal**

    ```sh
    module load anaconda3/2019.07
    source activate openmmlab
    ```

2. Remember to select **openmmlab** kernel when using jupyter notebook

3. Remember to check PATH in terminal

    PATH should include `/cluster/apps/anaconda3/2019.07/envs/openmmlab/bin` at the first entry.
    Otherwise `mim` will not work correctly.

## Set up Environment Locally

**Step 0**. Set up Python and install PyTorch correctly, using either pip or conda

**Step 1**. Install [MIM](https://github.com/open-mmlab/mim)

```sh
pip install openmim
which mim  # to check mim installation
# On windows, in Powershell, use
# gcm mim
```

### For OpenMMLab v2.0

**Step 2**. Install MMCV

```sh
mim install "mmcv>=2.0.0rc0"
```

**Step 2**. Install MMClassification and MMDetection

```sh
mim install "mmcls>=1.0.0rc0" "mmdet>=3.0.0rc0"
```

### For OpenMMLab v1.0

**Step 2**. Install MMCV

```sh
mim install mmcv-full
```

**Step 3**. Install MMClassification and MMDetection

```sh
mim install mmcls mmdet
```

## License

Apache-2.0 license

## Credit

This repository uses some datasets and models from [Zihao](https://github.com/TommyZihao/MMClassification_Tutorials)](https://github.com/TommyZihao/MMClassification_Tutorials).

[Fruit dataset](https://zihao-openmmlab.obs.myhuaweicloud.com/20220716-mmclassification/dataset/fruit30/fruit30_split.zip)

[Fruit model](https://zihao-openmmlab.obs.myhuaweicloud.com/20220716-mmclassification/checkpoints/fruit30_mmcls/fruit30_mmcls.pth)

and MMDetection and KITTI.

[KITTI-tiny](https://download.openmmlab.com/mmdetection/data/kitti_tiny.zip)