# Now it works on AMD HIP/ROCm? This need more tests but I actually manage to create something.

# Flux Gym

Dead simple web UI for training FLUX LoRA **with LOW VRAM (12GB/16GB/20GB) support.**

- **Frontend:** The WebUI forked from [AI-Toolkit](https://github.com/ostris/ai-toolkit) (Gradio UI created by https://x.com/multimodalart)
- **Backend:** The Training script powered by [Kohya Scripts](https://github.com/kohya-ss/sd-scripts)

FluxGym supports 100% of Kohya sd-scripts features through an [Advanced](#advanced) tab, which is hidden by default.

![screenshot.png](screenshot.png)

---

# Supported Models

1. Flux1-dev
2. Flux1-dev2pro (as explained here: https://medium.com/@zhiwangshi28/why-flux-lora-so-hard-to-train-and-how-to-overcome-it-a0c70bc59eaf)
3. Flux1-schnell (Couldn't get high quality results, so not really recommended, but feel free to experiment with it)
4. More?

The models are automatically downloaded when you start training with the model selected.

You can easily add more to the supported models list by editing the [models.yaml](models.yaml) file. If you want to share some interesting base models, please send a PR.

---

## Install Manually

First clone Fluxgym-rocm and wasd-tech/sd-scripts-rocm:

```
git clone https://github.com/wasd-tech/fluxgym-rocm
cd fluxgym
git clone -b sd3 https://github.com/wasd-tech/sd-scripts-rocm.git sd-scripts
```

Your folder structure will look like this:

```
/fluxgym
  app.py
  requirements_amd.txt
  /sd-scripts
```

Now activate a venv from the root `fluxgym-rocm` folder:

On Linux:

```
python -m venv env
source env/bin/activate
```

This will create an `env` folder right below the `fluxgym-rocm` folder:

```
/fluxgym-rocm
  app.py
  requirements_amd.txt
  /sd-scripts
  /env
```

Now go to the `sd-scripts` folder and install dependencies to the activated environment:

```
cd sd-scripts
pip install -r requirements_amd.txt
```

Now come back to the root folder and install the app dependencies:

```
cd ..
pip install -r requirements_amd.txt
```

Finally, install pytorch Nightly:

```
pip install --pre torch torchvision torchaudio --index-url https://download.pytorch.org/whl/nightly/rocm6.4
```


# Start

Go back to the root `fluxgym-rocm` folder, with the venv activated, run:

```
python app.py
```

> Make sure to have the venv activated before running `python app.py`.
>
> Linux: `source env/bin/activate`

![flow.gif](flow.gif)

# Configuration

## Sample Images

By default fluxgym doesn't generate any sample images during training.

You can however configure Fluxgym to automatically generate sample images for every N steps. Here's what it looks like:

![sample.png](sample.png)

To turn this on, just set the two fields:

1. **Sample Image Prompts:** These prompts will be used to automatically generate images during training. If you want multiple, separate teach prompt with new line.
2. **Sample Image Every N Steps:** If your "Expected training steps" is 960 and your "Sample Image Every N Steps" is 100, the images will be generated at step 100, 200, 300, 400, 500, 600, 700, 800, 900, for EACH prompt.

![sample_fields.png](sample_fields.png)

## Advanced Sample Images

Thanks to the built-in syntax from [kohya/sd-scripts](https://github.com/kohya-ss/sd-scripts?tab=readme-ov-file#sample-image-generation-during-training), you can control exactly how the sample images are generated during the training phase:

Let's say the trigger word is **hrld person.** Normally you would try sample prompts like:

```
hrld person is riding a bike
hrld person is a body builder
hrld person is a rock star
```

But for every prompt you can include **advanced flags** to fully control the image generation process. For example, the `--d` flag lets you specify the SEED.

Specifying a seed means every sample image will use that exact seed, which means you can literally see the LoRA evolve. Here's an example usage:

```
hrld person is riding a bike --d 42
hrld person is a body builder --d 42
hrld person is a rock star --d 42
```

Here's what it looks like in the UI:

![flags.png](flags.png)

And here are the results:

![seed.gif](seed.gif)

In addition to the `--d` flag, here are other flags you can use:


- `--n`: Negative prompt up to the next option.
- `--w`: Specifies the width of the generated image.
- `--h`: Specifies the height of the generated image.
- `--d`: Specifies the seed of the generated image.
- `--l`: Specifies the CFG scale of the generated image.
- `--s`: Specifies the number of steps in the generation.

The prompt weighting such as `( )` and `[ ]` also work. (Learn more about [Attention/Emphasis](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Features#attentionemphasis))

## Publishing to Huggingface

1. Get your Huggingface Token from https://huggingface.co/settings/tokens
2. Enter the token in the "Huggingface Token" field and click "Login". This will save the token text in a local file named `HF_TOKEN` (All local and private).
3. Once you're logged in, you will be able to select a trained LoRA from the dropdown, edit the name if you want, and publish to Huggingface.

![publish_to_hf.png](publish_to_hf.png)


## Advanced

The advanced tab is automatically constructed by parsing the launch flags available to the latest version of [kohya sd-scripts](https://github.com/kohya-ss/sd-scripts). This means Fluxgym is a full fledged UI for using the Kohya script.

> By default the advanced tab is hidden. You can click the "advanced" accordion to expand it.

![advanced.png](advanced.png)


## Advanced Features

### Uploading Caption Files

You can also upload the caption files along with the image files. You just need to follow the convention:

1. Every caption file must be a `.txt` file.
2. Each caption file needs to have a corresponding image file that has the same name.
3. For example, if you have an image file named `img0.png`, the corresponding caption file must be `img0.txt`.
