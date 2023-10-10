<h1 align="center">NanoOWL</h1>

<p align="center"><a href="#usage"/>👍 Usage</a> - <a href="#performance"/>⏱️ Performance</a> - <a href="#setup">🛠️ Setup</a> - <a href="#examples">🤸 Examples</a> <br> - <a href="#acknowledgement">👏 Acknowledgment</a> - <a href="#see-also">🔗 See also</a></p>

NanoOWL is a project that optimizes [OWL-ViT](https://huggingface.co/docs/transformers/model_doc/owlvit) to run 🔥 ***real-time*** 🔥 on [NVIDIA Jetson Orin Platforms](https://store.nvidia.com/en-us/jetson/store) with [NVIDIA TensorRT](https://developer.nvidia.com/tensorrt).  NanoOWL also introduces a new "tree detection" pipeline that combines OWL-ViT and CLIP to enable nested detection and classification of anything, at any level.

<p align="center"><img src="assets/basic_usage_out.jpg" height="256px"/></p>

> Interested in detecting object masks as well?  Try combining NanoOWL with
> [NanoSAM](https://github.com/NVIDIA-AI-IOT/nanosam) for zero-shot open-vocabulary 
> instance segmentation.

<a id="usage"></a>
## 👍 Usage

You can use NanoOWL in Python like this

```python3
from nanoowl.owl_predictor import OwlPredictor

predictor = OwlPredictor(
    "google/owlvit-base-patch32",
    image_encoder_engine="data/owlvit-base-patch32-image-encoder.engine"
)

image = PIL.Image.open("assets/owl_glove_small.jpg")

output = predictor.predict(image=image, text=["an owl", "a glove"], threshold=0.1)

print(output)
```

Or better yet, to use OWL-ViT in conjunction with CLIP to detect and classify anything,
at any level, check out the tree predictor example below!

> See [Setup](#setup) for instructions on how to build the image encoder engine.

<a id="performance"></a>
## ⏱️ Performance

NanoOWL runs real-time on Jetson Orin Nano.

<table style="border-top: solid 1px; border-left: solid 1px; border-right: solid 1px; border-bottom: solid 1px">
    <thead>
        <tr>
            <th rowspan=1 style="text-align: center; border-right: solid 1px">Model †</th>
            <th colspan=1 style="text-align: center; border-right: solid 1px">Image Size</th>
            <th colspan=1 style="text-align: center; border-right: solid 1px">Patch Size</th>
            <th colspan=1 style="text-align: center; border-right: solid 1px">⏱️ Jetson Orin Nano (FPS)</th>
            <th colspan=1 style="text-align: center; border-right: solid 1px">⏱️ Jetson AGX Orin (FPS)</th>
            <th colspan=1 style="text-align: center; border-right: solid 1px">🎯 Accuracy (mAP)</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="text-align: center; border-right: solid 1px">OWL-ViT (ViT-B/32)</td>
            <td style="text-align: center; border-right: solid 1px">768</td>
            <td style="text-align: center; border-right: solid 1px">32</td>
            <td style="text-align: center; border-right: solid 1px">TBD</td>
            <td style="text-align: center; border-right: solid 1px">95</td>
            <td style="text-align: center; border-right: solid 1px">28</td>
        </tr>
        <tr>
            <td style="text-align: center; border-right: solid 1px">OWL-ViT (ViT-B/16)</td>
            <td style="text-align: center; border-right: solid 1px">768</td>
            <td style="text-align: center; border-right: solid 1px">16</td>
            <td style="text-align: center; border-right: solid 1px">TBD</td>
            <td style="text-align: center; border-right: solid 1px">25</td>
            <td style="text-align: center; border-right: solid 1px">31.7</td>
        </tr>
    </tbody>
</table>

<a id="setup"></a>
## 🛠️ Setup

1. Install the dependencies

    1. Install PyTorch

    2. Install [torch2trt](https://github.com/NVIDIA-AI-IOT/torch2trt)
    3. Install NVIDIA TensorRT
    4. Install the Transformers library

        ```bash
        python3 -m pip install transformers
        ```
    5. (optional) Install NanoSAM (for the instance segmentation example)

2. Install the NanoOWL package.

    ```bash
    git clone https://github.com/NVIDIA-AI-IOT/nanoowl
    cd nanoowl
    python3 setup.py develop --user
    ```

3. Build the TensorRT engine for the OWL-ViT vision encoder

    ```bash
    python3 -m nanoowl.build_image_encoder_engine \
        --model="google/owlvit-base-patch32" \
        --image_encoder_engine="data/owlvit_image_encoder_patch32.engine"
    ```
    

4. Run an example prediction to ensure everything is working

    ```bash
    python3 owl_predict.py \
        --prompt="[an owl, a glove]" \
        --threshold=0.15 \
        --image_encoder_engine=../data/owl_image_encoder_engine_patch32.engine
    ```

That's it!  If everything is working properly, you should see a visualization saved to ``data/owl_predict_out.jpg``.  

<a id="examples"></a>
## 🤸 Examples

### Example 1 - Owl Predictor


```bash
python3 owl_predict.py \
    --prompt="[an owl, a glove]" \
    --threshold=0.15 \
    --image_encoder_engine=../data/owl_image_encoder_engine_patch32.engine
```

### Example 2 - Tree Predictor


```bash
python3 tree_predict.py \
    --prompt="[an owl [a wing, an eye], a glove]" \
    --threshold=0.15 \
    --image_encoder_engine=../data/owl_image_encoder_engine_patch32.engine
```

```bash
python3 tree_predict.py \
    --prompt="(indoors, outdoors)" \
    --threshold=0.15 \
    --image_encoder_engine=../data/owl_image_encoder_engine_patch32.engine
```

```bash
python3 tree_predict.py \
    --prompt="(indoors, outdoors [an owl])" \
    --threshold=0.15 \
    --image_encoder_engine=../data/owl_image_encoder_engine_patch32.engine
```

### Example 3 - Tree Predictor (Live Camera)

```bash
python3 tree_demo.py ../../data/owl_image_encoder_patch32.engine
```

<a id="acknowledgement"></a>
## 👏 Acknowledgement

Thanks to the authors of [OWL-ViT](https://huggingface.co/docs/transformers/model_doc/owlvit) for the great open-vocabluary detection work.

<a id="see-also"></a>
## 🔗 See also

- [NanoSAM](https://github.com/NVIDIA-AI-IOT/nanosam) - A real-time Segment Anything (SAM) model variant for NVIDIA Jetson Orin platforms.
- [Jetson Introduction to Knowledge Distillation Tutorial](https://github.com/NVIDIA-AI-IOT/jetson-intro-to-distillation) - For an introduction to knowledge distillation as a model optimization technique.
- [Jetson Generative AI Playground](https://nvidia-ai-iot.github.io/jetson-generative-ai-playground/) - For instructions and tips for using a variety of LLMs and transformers on Jetson.
- [Jetson Containers](https://github.com/dusty-nv/jetson-containers) - For a variety of easily deployable and modular Jetson Containers
