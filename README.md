# CASED-Tensorflow
Tensorflow implementation of [Curriculum Adaptive Sampling for Extreme Data Imbalance](https://www.researchgate.net/publication/319461093_CASED_Curriculum_Adaptive_Sampling_for_Extreme_Data_Imbalance) with **multi GPU** using [*LUNA16*](https://luna16.grand-challenge.org/)

## Preprocessing Tutorial
* [convert_luna_to_npy](/preprocessing/README/convert_luna_to_npy_README.md)
* [create patch](preprocessing/README/h5py_patch_README.md)
```python
 > all_in_one.py = convert_luna_to_npy + create_patch
```


## Usage for preprocessing
```python
> python all_in_one.py
```
* Check `src_root` and `save_path`

## Usage for train
```python
> python main_train.py
```
* See `main_train.py` for other arguments.

## Usage for test
```python
> python main_test.py
```

## Issue
* *The hyper-parameter information is not listed in the paper, so I'm still testing it.*
* Use ***[Snapshot Ensemble](https://arxiv.org/pdf/1704.00109.pdf)*** (M=10, init_lr=0.1)
* Or Fix learning rate **0.01**

![snapshot](./assests/lr.JPG)
```python
def Snapshot(t, T, M, alpha_zero) :
    """
    t = # of current iteration
    T = # of total iteration
    M = # of snapshot
    alpha_zero = init learning rate
    """

    x = (np.pi * (t % (T // M))) / (T // M)
    x = np.cos(x) + 1

    lr = (alpha_zero / 2) * x

    return lr
 ```

## Summary
### Preprocessing
* Resample
```bash
> 1.25mm
```

* Hounsfield
```python
> minHU = -1000
> maxHU = 400
```

* Zero centering
```python
> Pixel Mean = 0.25
```

### Data augmentation
If you want to do augmentation, see this [link](https://github.com/aleju/imgaug)

* Affine rotate
```python
-2 to 2 degree
```

* Scale
```python
0.9 to 1.1
```

### Network Architecture
![network](./assests/network.JPG)

### Algorithm
![framework](./assests/framework.JPG)
```python
p_x = 1.0

for i in iteration :
    p = uniform(0,1)
    
    if p <= p_x :
        g_n_index = np.random.choice(N, size=batch_size, replace=False)
        batch_patch = nodule_patch[g_n_index]
        batch_y = nodule_patch_y[g_n_index]
    
    else :
        predictor_dict = Predictor(all_patch) # key = index, value = loss
        g_r_index = nlargest(batch_size, predictor_dict, key=predictor_dict.get)
        
        batch_patch = all_patch[g_r_index]
        batch_y = all_patch_y[g_r_index]
    
    p_x *= pow(1/M, 1/iteration)
```

## Result
![result2](./assests/result2.JPG)


## Author
Junho Kim / [@Lunit](http://lunit.io/)
