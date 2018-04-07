---
layout: post
title: "CUDA 8.0 삭제하고 9.0 설치하는법"
category: install
tags: tensorflow cuda cudnn install
excerpt: Tensorflow 1.7 update 로 인한 CUDA 8.0 삭제하고 9.0 설치하는법
mathjax: true
author: J. H. Park
sitemap :
    changefreq : daily
    priority : 0.5
---

* content
{:toc}

물체인식 알고리즘중에서 yolo 를 이용한 방식을 정리합니다. (이론 x, 돌리는법 o)

순서는

1. YOLO 소개
2. dataset, 자동차 번호판
3. annotation, label 만드는 작업
4. darkflow, yolo for tensorflow
5. training, 학습
6. detection, test 하기

## [You Only Look Once](https://pjreddie.com/darknet/yolo/)

YOLO, object detection - 물체인식  
찾으려는 물체를 찾아 `bounding box` 로 표시해주는 알고리즘 입니다.  
아래 사진은 YOLO 를 설명하는 대표적인 개, 자전거를 인식한 결과입니다.   

![이미지](https://github.com/Park-Ju-hyeong/Park-Ju-hyeong.github.io/blob/master/_posts/2018_images/20180407_01.png?raw=true)  

YOLO 와 비슷한 선상에 있는 알고리즘으로는 `Fast/Faster R-CNN` 과 `SSD` 가 있는데 이것들과의 차이점으로는 다음과 같은 특징이 있습니다.  

|속도 : speed |정학도 : accuracy|
|:--:|:--:|
|`YOLO` > <br> `Fast/Faster R-CNN`, `SSD`| `Fast/Faster R-CNN`, `SSD` > <br> `YOLO` |  

![이미지](https://github.com/Park-Ju-hyeong/Park-Ju-hyeong.github.io/blob/master/_posts/2018_images/20180407_03.png?raw=true)  

각자 필요에 맞게, 상황에 맞게 알고리즘을 가져다 쓰면 됩니다.  
real-time (예를 들어, gpu 1장 기준 초당 30장 이상 처리要 : `30fps` ) 을 해야하는 상황이면 YOLO 를 쓰면 되고  
시간과 비용에 구애받지 않는다면, Faster R-CNN 을 쓰면 됩니다.  

## [DATASET](https://www.google.co.kr/search?q=%EC%9E%90%EB%8F%99%EC%B0%A8+%EB%B2%88%ED%98%B8%ED%8C%90&safe=off&source=lnms&tbm=isch&sa=X&ved=0ahUKEwjkz5u9iajaAhVJtpQKHfnVDe0Q_AUICigB&biw=1440&bih=900)

![이미지](https://github.com/Park-Ju-hyeong/Park-Ju-hyeong.github.io/blob/master/_posts/2018_images/20180407_02.png?raw=true)

이번 포스트를 위해서 자동차 번호판 인식을 진행해보려 하는데, google 에 `자동차 번호판` 을 검색해서 나온 사진들을 이용하였습니다.  (29장 구할 수 있었습니다.)  

![이미지](https://github.com/Park-Ju-hyeong/Park-Ju-hyeong.github.io/blob/master/_posts/2018_images/20180407_04.png?raw=true)

자동차 번호판은 `[0-9]`숫자와 `[가-힣]`한글의 조합으로 구성되어있습니다.  
이번 포스트에서 인식한 클레스는 숫자 10종류와 한글 1종류로 제한하겠습니다.  
숫자는 `MNIST` 처럼 얕은 네트워크도 0~9 를 잘 인식하지만, 한글은 종류가 너무 많아서 통으로 `kr` 이라는 class로 정의했습니다.

## [Annotation][Annotation]
[Annotation] : https://github.com/tzutalin/labelImg





## darkflow

### flow --help : parameters
```
Example usage: flow --imgdir sample_img/ --model cfg/yolo.cfg --load bin/yolo.weights

Arguments:
  --help, --h, -h  show this super helpful message and exit
  --imgdir         path to testing directory with images
  --binary         path to .weights directory
  --config         path to .cfg directory
  --dataset        path to dataset directory
  --labels         path to labels file
  --backup         path to backup folder
  --summary        path to TensorBoard summaries directory
  --annotation     path to annotation directory
  --threshold      detection threshold
  --model          configuration of choice
  --trainer        training algorithm
  --momentum       applicable for rmsprop and momentum optimizers
  --verbalise      say out loud while building graph
  --train          train the whole net
  --load           how to initialize the net? Either from .weights or a checkpoint, or even from scratch
  --savepb         save net and weight to a .pb file
  --gpu            how much gpu (from 0.0 to 1.0)
  --gpuName        GPU device name
  --lr             learning rate
  --keep           Number of most recent training results to save
  --batch          batch size
  --epoch          number of epoch
  --save           save checkpoint every ? training examples
  --demo           demo on webcam
  --queue          process demo in batch
  --json           Outputs bounding box information in json format.
  --saveVideo      Records video from input video or camera
  --pbLoad         path to .pb protobuf file (metaLoad must also be specified)
  --metaLoad       path to .meta file generated during --savepb that corresponds to .pb file
```

## 마무리

