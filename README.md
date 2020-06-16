# End-to-End Audiovisual Speech Recognition

## Introduction

This is the respository of [End-to-End Audiovisual Speech Recognition](https://sites.google.com/view/audiovisual-speech-recognition). Our paper can be found [here](https://arxiv.org/pdf/1802.06424.pdf). 

The video-only stream is based on T. Stafylakis and G. Tzimiropoulos's implementation. The paper can be found [here](https://arxiv.org/pdf/1703.04105.pdf).

This implementation includes 2-layer BGRU which consists of 1024 cells in each layer while Themos's implementation uses 2-layer BLSTM with 512 cells.

## Update

*2020-06-06*: Please check https://github.com/mpc001/Lipreading_using_Temporal_Convolutional_Networks for our lipreading model which can easily achieve 85.5% on LRW dataset.

## Dependencies

* python 2.7
* pytorch 0.3.1
* opencv-python 3.4.0

## Dataset

The results obtained with the proposed model on the [LRW dataset](http://www.robots.ox.ac.uk/~vgg/data/lip_reading/lrw1.html). The coordinates for cropping mouth ROI are suggested as (x1, y1, x2, y2) = (80, 116, 175, 211) in Matlab. Please note that the fixed cropping mouth ROI (FxHxW) = [:, 115:211, 79:175] in python.

## Training

This is the suggested order to train models including video-only model, audio-only model and audiovisual models:

i) Start by training with temporal convolutional backend, you can run the script:

```
CUDA_VISIBLE_DEVICES='' python main.py --path '' --dataset <dataset_path> \
                                       --mode 'temporalConv' \
                                       --batch_size 36 --lr 3e-4 \
                                       --epochs 30
```

ii)Throw away the temporal convolutional backend, freeze the parameters of the frontend and the ResNet and train the LSTM backend, then run the script:

```
CUDA_VISIBLE_DEVICES='' python main.py --path './temporalConv/temporalConv_x.pt' --dataset <dataset_path> \
                                       --mode 'backendGRU' --every-frame \
                                       --batch_size 36 --lr 3e-4 \
                                       --epochs 5
```

iii)Train the whole network end-to-end. You can run the script:

```
CUDA_VISIBLE_DEVICES='' python main.py --path './backendGRU/backendGRU_x.pt' --dataset <dataset_path> \
                                       --mode 'finetuneGRU' --every-frame \
                                       --batch_size 36 --lr 3e-4 \
                                       --epochs 30
```

**Notes**

`every-frame` is activated when the backend module is recurrent neural network.

`dataset` need be correctly specified before running. Code has strong assumptions on the dataset organisation.

`temporalConv_x.pt` or `backendGRU_x.pt` are the models with best validation performance on step ii) or step iii).

## Models&Accuracy

|Stream        |Accuracy    |
|--------------|------------|
|video-only    |83.39       |
|audio-only    |97.72       |
|audiovisual   |98.38       |

The results are slightly better than ones reported in [the ICASSP paper](https://ieeexplore.ieee.org/document/8461326) due to further fine-tuning of the models. Please send email at pingchuan.ma16 \<AT\> imperial.ac.uk with name and affiliation for the pre-trained models.

## Reference

If the code of this repository was useful for your research, please cite our work:

```
@article{petridis2018end,
  title={End-to-end audiovisual speech recognition},
  author={Petridis, Stavros and Stafylakis, Themos and Ma, Pingchuan and Cai, Feipeng and Tzimiropoulos, Georgios and Pantic, Maja},
  booktitle={ICASSP},
  pages={6548--6552},
  year={2018},
  organization={IEEE}
}
```
