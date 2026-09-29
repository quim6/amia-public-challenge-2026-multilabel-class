# Computer-VIsion-2026
FUB module

https://www.kaggle.com/competitions/amia-public-challenge-2026/overview

### Overleaf
https://www.overleaf.com/8194827821fkqbgsbtjpnv#e1693d

### Tip from lecture
- Use transformers by introducing a no-object class.
- Mask out object of interest. If prediction changes, we can be sure, that it's causality. If not, the prediction is based on corealtion (background).
- If we use older architecture, the best we can expect is 0.3. Then he mentioned YOLO for first 40 and Transformers for first 50 in solution ranking.

- lecture 25.6: use automated mixed precision;

### Submssion:
- debug code on our machine, one epoch, two batches, train and validation
- prepare enviroment
- share github with proffesor, he'll run it on institution's server

### Before training
- get the box coordinates right (per image normalization) https://www.kaggle.com/competitions/amia-public-challenge-2026/discussion/691668

## Project Plan
- Goal: Object detection - (1) locate and (2) classify
### 1. Exploratory Data Analysis
- Train dataset consists of 17 different radiologists annotating 8573 images
- annotation format: image_id, class_name, class_id, rad_id, x_min, y_min, x_max, y_max
- Annotations per image: min=3, max=57, mean=5.36
- Annotations per radiologist: min=444, max=12624, mean=2701.47
- We handle .png files

### 2. Preprocessing & Augmentation
- normalize size per image to match annotation coords. see https://www.kaggle.com/competitions/amia-public-challenge-2026/discussion/691668
- resize to feed NN
- enhance contrast
- augment: what augmentation does make sense in medical imaging?
- **Explore coocurence of classes.**

### 3. Split
- Kaggle provides`test.csv`, consists from image_id only.
- Our personal train-validation split also with targets.
- Use Stratified K-Fold cross-validation.
    - One image multiple annotation - groupby('image_id') to keep all metadata of image in single batch
    - Compensating inbalance? (back to augmentation of underrepresented classes)

### 4. Build a Baseline Model
- choose established framework or Pretrained Backbone
    - any pretrained medical NNs?
- Specific fallback for class 14: No finidng -> coords[0,0,1,1]

### 5. Iterate and Optimize


## Recommendations
- YOLO fro object detecction
- `albumentations` for augmentation


## What was done
- all the images are 1024*1024
- BUG scaling func : swapped height and width 


## Pretrained networks:
- [RadImageNet](https://github.com/BMEII-AI/RadImageNet) - used for pneumonia 
- [MedicalNet](https://github.com/Tencent/MedicalNet) - 3D imaging
- *We utilized ResNet50 [21], as in previous related studies [10], [13], [19], with its last fully-connected layer having 14 (CheXpert).* see [paper](https://www.sciencedirect.com/science/article/pii/S0167865526000255)
- [MedClip] - seen in [different models evaluation paper](https://arxiv.org/pdf/2409.09430), foundation model for radiology
Then recommended by Gemini:
- [SwinCheX paper](https://arxiv.org/pdf/2206.04246#:~:text=This%20paper%20proposes%20a%20multi%2Dlabel%20classification%20deep,30%2C000%20patients%20with%2014%20famous%20chest%20diseases.) 
- [TorchXRayVision] as a industry standard

## Augmentation
- *In all our experiments, we used a batch size of 32, and applied data augmentation with TorchIO [23]. Data augmentation included random rotations between and 10∘, random scaling in the range [0.9, 1.1]* see [paper](https://www.sciencedirect.com/science/article/pii/S0167865526000255)
