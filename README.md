# Bamba

<p align="center">
  <img src="/bamba.jpeg" width="400"/>
</p>

<p align="center">
        🤗 <a href="https://huggingface.co/collections/ibm-fms/bamba-674f1388b9bbc98b413c7bab"> Bamba on Hugging Face</a>&nbsp | <a href="https://github.com/foundation-model-stack/bamba/blob/main/blog/bamba-9b-release.md"> Bamba Blog</a>&nbsp
<br>

Bamba is a repository for training and using [Bamba](https://huggingface.co/ibm-fms/Avengers-Mamba2-9B) models which are based on [Mamba](https://github.com/state-spaces/mamba) models.


## Installation

Besides [PyTorch](https://pytorch.org/), you would need a few [extra dependencies](https://github.com/state-spaces/mamba?tab=readme-ov-file#installation) for
Mamba models.

We found some of these dependencies picky on PyTorch versions when doing pip install, so 
the best way is to build from source for all Mamba dependencies if you hit dependency 
issue with your env:
```bash
git clone https://github.com/Dao-AILab/causal-conv1d.git
cd causal-conv1d && pip install . && cd ..
git clone https://github.com/state-spaces/mamba.git
cd mamba && pip install . && cd ..
git clone https://github.com/Dao-AILab/flash-attention.git
cd flash-attention && pip install . && cd ..
```


## Models

### Overview
TODO: add model card here

### Checkpoints
We have published our model checkpoints here: TODO: add mamba HF page once public


## Inference
You can utilize our newly contributed HF integration to run inference on our Bamba models:
```python
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained("ibm-fms/Avengers-Mamba2-9B-hf")
tokenizer = AutoTokenizer.from_pretrained("ibm-fms/Avengers-Mamba2-9B-hf")

message = ["TODO: find a prompt here"]
inputs = tokenizer(message, return_tensors='pt', return_token_type_ids=False)
response = model.generate(**inputs, max_new_tokens=100, do_sample=True, top_k=50, top_p=0.95)
print(tokenizer.batch_decode(response, skip_special_tokens=True)[0])

```


## Training

We trained our Bamba model with FSDP using our training repo [here](https://github.com/foundation-model-stack/fms-fsdp/tree/mamba-new).
Note that this training effort was started before FSDP2 and also long before we contributed
`Mamba2-Hybrid` to HF, so we were doing FSDP1 training with [official Mamba implementation](https://github.com/state-spaces/mamba).
For users trying to reproduce the training you now have much more options with our newly
contributed [HF-version of Mamba2-Hybrid]() (TODO: add link once live).


## Fine-tuning

This [example](./tuning/Fine-tuning.md) shows how to fine tune the bamba model for a specific task using [SFT Trainer](https://huggingface.co/docs/trl/en/sft_trainer#supervised-fine-tuning-trainer).

                           
## Quantization
We can create a (FP8) quantized model using [`fms-model-optimizer`](https://github.com/foundation-model-stack/fms-model-optimizer/), which will make the storage and inference even more efficient.
```python
python -m fms_mo.run_quant \
    --model_name_or_path <"path_to_original_model"> \
    --quant_method fp8 \
    --torch_dtype bfloat16 \
    --output_dir <"path_to_save_new_model">
```
Model size comparison before and after FP8:
||original|quantized |
|:----:|----:|----:|
|memory (total)|39.12 GB|10.83 GB| 
|memory (break-down)|`torch.float32` 39.12 GB|`torch.bfloat16` 2.10 GB<br>`torch.float8_e4m3fn`    8.73 GB|

More details about `fms-model-optimizer` can be found [here](https://github.com/foundation-model-stack/fms-model-optimizer/tree/main/examples/FP8_QUANT#quickstart).

## Evaluation



