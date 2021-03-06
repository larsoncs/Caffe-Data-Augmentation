@@ -1,2 +1,93 @@

# Caffe-Data-Augmentation

Image data augmentation util for Caffe

# Introduction

Data augmentation is the best
  trick when you are training a deep network. the project implements several frequently-used methods for image task. Caffe's prefetching method makes my method costless with time. During training, u can try combinations of multiple diffrent processing , they do boost the performance. the most important things is that it is easy to set up and do any modification by yourself.
# Realtime data augmentation utils

Now, the methods in the utils include :

  1) Color

  2) Contrast

  3) Brightness

  4) Rotation 

# how to setup

1  add two files

    1)  data_augment.hpp --> include/caffe/util/

    2)  data_augment.cpp --> src/caffe/util/

2  modify include/caffe/data_transformer.hpp

    1) + #include "caffe/util/data_augmenter.hpp"

    2)  protected: 

        +  DataAugmenter<Dtype> aug_;

3  modify src/caffe/data_transformer.cpp

    1)  template<typename Dtype> DataTransformer<Dtype>::
    DataTransformer(const TransformationParameter& param,Phase phase) : param_(param), phase_(phase)

        + ,aug_( param) {

    2)  template<typename Dtype> void DataTransformer<Dtype>::
     Transform(const cv::Mat& cv_img,Blob<Dtype>* transformed_blob){ 

      ...

      +  if ( phase_ == TRAIN) {

      +  aug_.Transform(cv_cropped_img);

      +  }

    CHECK(cv_cropped_img.data);

4  modify src/proto/caffe.proto

    1)  message TransformationParameter{

      ...

      +  optional bool color = 9 [default = false];

      +  optional bool contrast = 10 [default = false];

      +  optional bool brightness = 11 [default = false];

      +  optional int32 rotation_angle_interval = 12 [default = 0];

      +  optional bool show_augment_info = 13 [default = false];

      +  optional string dir_to_save_augmented_imgs = 15;

5  compile

    1)  cd build

    2)  cmake ..

    3)  make -j8

# how to use

  layer
  {

    name: "data"
  
    type: "ImageData"
  
    top:  "data"
  
    top: "label"
  
    include {
  
      phase: TRAIN
      
    }
  
    transform_param
    {
      mirror: true
    
      crop_size: 227
    
      mean_file: "imagenet_mean.binaryproto"
    
      color: true
    
      contrast: true
    
      brightness: true
    
      rotation_angle_interval: 10
    
      # show_augment_info: true
    
      # dir_to_save_augmented_imgs: "path"
    
  }

    image_data_param
  
    {

      source: "/home/your/image/list.txt"

      batch_size: 32

      shuffle:true

      new_height:256

      new_width: 256
    
    }
  
}

# Acknowledgment

    the implementation of rotation is based on @kevinlin311tw 's caffe-augmentation

# PS:

    if u think it's a nice project, just Star it!
