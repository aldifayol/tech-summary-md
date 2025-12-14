# Tinker: General Availability and Vision Input

**Thinking Machines Lab**  
December 12, 2025

Today we are announcing four updates to Tinker:

- No more waitlist
- New reasoning model: Kimi K2 Thinking
- New inference interface that is compatible with the OpenAI API
- Vision input support with Qwen3-VL

## General availability

The waitlist is over! Everybody can use Tinker now; [sign up here](https://auth.thinkingmachines.ai/sign-up) to get started. See the [Tinker homepage](https://thinkingmachines.ai/tinker/) for available models and pricing, and check out the [Tinker cookbook](https://github.com/thinking-machines-lab/tinker-cookbook) for code examples.

## More reasoning with Kimi K2 Thinking

Users can now fine-tune Kimi K2 Thinking on Tinker. With a trillion parameters, Kimi K2 is the largest model in our lineup so far. It is built for long chains of reasoning and tool use.

## OpenAI API-compatible sampling

Tinker has a standard function for inference:

```python
prompt = types.ModelInput.from_ints(tokenizer.encode("The capital of France is",))
params = types.SamplingParams(max_tokens=20, temperature=0.0, stop=["\n"])
future = sampling_client.sample(prompt=prompt, sampling_params=params)
```

With this release, we have added OpenAI API-compatible scaffolding for quickly sampling from a model by specifying a path, even while it's still training. This also means Tinker can now plug-and-play with any OpenAI API-compatible platform. See more information in our [Tinker documentation](https://tinker-docs.thinkingmachines.ai/compatible-apis/openai).

```python
response = openai_client.completions.create(
    model="tinker://0034d8c9-0a88-52a9-b2b7-bce7cb1e6fef:train:0/sampler_weights/000080",
    prompt="The capital of France is",
    max_tokens=20,
    temperature=0.0,
    stop=["\n"],
)
```

## Vision input with Qwen3-VL

We've added two vision models to Tinker: Qwen3-VL-30B-A3B-Instruct and Qwen3-VL-235B-A22B-Instruct. With these, users can process pictures, screenshots, and diagrams for a variety of applications.

To input images, just interleave together an [ImageChunk](https://tinker-docs.thinkingmachines.ai/api-reference/types#imagechunk-objects) – consisting of your image, saved as bytes – with text chunks. For example:

```python
model_input = tinker.ModelInput(chunks=[
  tinker.types.ImageChunk(data=image_data, format="png"),
  tinker.types.EncodedTextChunk(tokens=tokenizer.encode("What is this?")),
])
```

These vision inputs can be used in a variety of applications out-of-the-box, including SFT and RL finetuning.

To demonstrate vision understanding in action, we are sharing [a new cookbook recipe for fine-tuning VLMs as image classifiers](https://github.com/thinking-machines-lab/tinker-cookbook/tree/main/tinker_cookbook/recipes/vlm_classifier). Qwen3-VL-235B-A22B-Instruct obtains reasonable accuracy even given just one example per class; performance improves with more labeled data.

## Training image classifiers with Tinker

To showcase Tinker's new vision capabilities, we finetuned [Qwen3-VL-235B-A22B-Instruct](https://huggingface.co/Qwen/Qwen3-VL-235B-A22B-Instruct) to classify images on four classic datasets:

- [Caltech 101](https://data.caltech.edu/records/mzrjq-6wc02), a dataset of 101 general object categories.
- [Stanford Cars](https://www.kaggle.com/datasets/eduardo4jesus/stanford-cars-dataset), a dataset of car makes, models, and years.
- [Oxford Flowers](https://www.robots.ox.ac.uk/~vgg/data/flowers/102/), a dataset of flower species.
- [Oxford Pets](https://www.robots.ox.ac.uk/~vgg/data/pets/), a dataset of pet breeds.

Since Qwen3-VL is a language model, we frame classification as text generation: given an image, the model outputs the class name. We compare this approach against a traditional vision baseline of finetuning a vision-only model — DINOv2-base. [DINOv2](https://arxiv.org/pdf/2304.07193) is a self-supervised vision transformer that was trained to encode images, and is commonly used as a backbone for pure computer vision tasks. For DINOv2, we add a classification head that predicts a distribution over all N classes. Both models are fine-tuned with LoRA.

Labeled image data is scarce for many real-world use cases, so data efficiency is the primary measure we look at. We show the classification accuracy when sweeping across the number of labeled examples per class, starting with just a single one.

![Comparison of fine-tuned Qwen3-VL-235-A22B and DINOv2 performance on simple image classification tasks](images/vlm-graphs.png)

*Comparison of fine-tuned Qwen3-VL-235-A22B and DINOv2 performance on simple image classification tasks.*

In the limited-data regime, Qwen3-VL-235-A22B outperforms DINOv2. Not only is it a bigger model, but as a VLM, it also comes with language knowledge out-of-the-box (i.e. what a "golden retriever" or "sunflower" is). This general language-and-vision capability of Qwen3-VL makes it readily available for vision tasks beyond classification.

## Happy Holidays

Tinker exists to enable builders and researchers to train and customize state-of-the-art models. As always, we look forward to seeing what you build with Tinker. Happy holidays!

---

*[Thinking Machines Lab](https://thinkingmachines.ai/) © 2025*
