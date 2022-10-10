# sjtu-openmmlab-tutorial

## Notes on SJTU Cluster

**Activate Conda Environment in terminal**

```sh
module load anaconda3/2019.07
source activate openmmlab
```

**Select openmmlab kernel in jupyter notebook**

## Set up Environment Locally

**Step 0**. Set up Python and install PyTorch correctly, using either pip or conda

**Step 1**. Install [MIM](https://github.com/open-mmlab/mim)

```sh
pip install openmim
which mim  # to check mim installation
# On windows, in Powershell, use
# gcm mim
```

### For OpenMMLab v1.0

**Step 2**. Install MMCV-full

```sh
mim install mmcv-full
```

**Step 3**. Install MMClassification and MMDetection

```sh
mim install mmcls mmdet
```

### For OpenMMLab v2.0

**Step 2**. Install MMCV

```sh
mim install mmcv>=2.0.0rc0
```

**Step 2**. Install MMClassification and MMDetection

```sh
mim install mmcls>=1.0.0rc0 mmdet>=3.0.0rc0
```

## License

## Credit

This repository use some dataset and model from [Zihao](https://github.com/TommyZihao/MMClassification_Tutorials).

[Fruit dataset](https://zihao-openmmlab.obs.myhuaweicloud.com/20220716-mmclassification/dataset/fruit30/fruit30_split.zip)

[Fruit model](https://zihao-openmmlab.obs.myhuaweicloud.com/20220716-mmclassification/checkpoints/fruit30_mmcls/fruit30_mmcls.pth)
