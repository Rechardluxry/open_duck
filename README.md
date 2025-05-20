# Open Duck Playground

# Installation 

Install uv

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```


cd ~/Open_Duck_Playground
pip install -e .
进行安装


我的conda虚拟环境：

(open_duck) lxr@lxr-System-Product-Name:~/lxr/Open_Duck_Playground$ python
Python 3.12.2 | packaged by conda-forge | (main, Feb 16 2024, 20:50:58) [GCC 12.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
>>> # 查看 CUDA 版本
>>> print(f"CUDA version: {torch.version.cuda}")
CUDA version: 12.8
>>> 
>>> # 查看 cuDNN 版本
>>> print(f"cuDNN version: {torch.backends.cudnn.version()}")
cuDNN version: 90701
>>> 
>>> # 查看 PyTorch 版本
>>> print(f"PyTorch version: {torch.__version__}")
PyTorch version: 2.7.0+cu128
>>> 
>>> # 检查 CUDA 是否可用
>>> print(f"CUDA is available: {torch.cuda.is_available()}")
CUDA is available: True
>>> # 检查当前使用的 GPU 设备
>>> if torch.cuda.is_available():
...     print(f"Current device: {torch.cuda.current_device()}")
...     print(f"Device count: {torch.cuda.device_count()}")
...     print(f"Device name: {torch.cuda.get_device_name(torch.cuda.current_device())}")
... 
Current device: 0
Device count: 1
Device name: NVIDIA GeForce RTX 4060


# Training

If you want to use the [imitation reward](https://la.disneyresearch.com/wp-content/uploads/BD_X_paper.pdf), you can generate reference motion with [this repo](https://github.com/apirrone/Open_Duck_reference_motion_generator)

Then copy `polynomial_coefficients.pkl` in `playground/<robot>/data/`

You'll also have to set `USE_IMITATION_REWARD=True` in it's `joystick.py` file

Run: 

```bash
uv run playground/<robot>/runner.py 
```

## Tensorboard

```bash
uv run tensorboard --logdir=<yourlogdir>
```

# Inference 

Infer mujoco

(for now this is specific to open_duck_mini_v2)

```bash
uv run playground/open_duck_mini_v2/mujoco_infer.py -o <path_to_.onnx> (-k)
```

# Documentation

## Project structure : 

```
.
├── pyproject.toml
├── README.md
├── playground
│   ├── common
│   │   ├── export_onnx.py
│   │   ├── onnx_infer.py
│   │   ├── poly_reference_motion.py
│   │   ├── randomize.py
│   │   ├── rewards.py
│   │   └── runner.py
│   ├── open_duck_mini_v2
│   │   ├── base.py
│   │   ├── data
│   │   │   └── polynomial_coefficients.pkl
│   │   ├── joystick.py
│   │   ├── mujoco_infer.py
│   │   ├── constants.py
│   │   ├── runner.py
│   │   └── xmls
│   │       ├── assets
│   │       ├── open_duck_mini_v2_no_head.xml
│   │       ├── open_duck_mini_v2.xml
│   │       ├── scene_mjx_flat_terrain.xml
│   │       ├── scene_mjx_rough_terrain.xml
│   │       └── scene.xml
```

## Adding a new robot

Create a new directory in `playground` named after `<your robot>`. You can copy the `open_duck_mini_v2` directory as a starting point.

You will need to:
- Edit `base.py`: Mainly renaming stuff to match you robot's name
- Edit `constants.py`: specify the names of some important geoms, sensors etc
  - In your `mjcf`, you'll probably have to add some sites, name some bodies/geoms and add the sensors. Look at how we did it for `open_duck_mini_v2`
- Add your `mjcf` assets in `xmls`. 
- Edit `joystick.py` : to choose the rewards you are interested in
  - Note: for now there is still some hard coded values etc. We'll improve things on the way
- Edit `runner.py`



# Notes

Inspired from https://github.com/kscalelabs/mujoco_playground


## Current win
- train for 300 000 000 steps
- train with backlash (takes longer)
- Flat terrain
- Infer on the robot with kp = 22 instead of 32

## TODO
- Understand why head does not move very much
- low pass filter ? 37.5 hz ? 
