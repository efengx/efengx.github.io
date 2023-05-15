---
title: "rjxai Model Card"
date: 2023-05-15T09:40:06+08:00
tags: [rjxai]
description: "rjxai"
draft: false
---
## overview

The model is composed of: LLaMA + loRA

<!--more-->

## use configuration

1.LLaMA 7b config

```base
LlamaConfig {
  "_name_or_path": "rjx/rjxai-zh-llama-7b-v1",
  "architectures": [
    "LlamaForCausalLM"
  ],
  "bos_token_id": 0,
  "eos_token_id": 1,
  "hidden_act": "silu",
  "hidden_size": 4096,
  "initializer_range": 0.02,
  "intermediate_size": 11008,
  "max_position_embeddings": 2048,
  "max_sequence_length": 2048,
  "model_type": "llama",
  "num_attention_heads": 32,
  "num_hidden_layers": 32,
  "pad_token_id": -1,
  "rms_norm_eps": 1e-06,
  "tie_word_embeddings": false,
  "torch_dtype": "float16",
  "transformers_version": "4.28.1",
  "use_cache": true,
  "vocab_size": 49953
}
```

2.LoRA（fine-tune）config

```base
LoraConfig(
  peft_type=<PeftType.LORA: 'LORA'>, 
  base_model_name_or_path=None, 
  task_type=<TaskType.CAUSAL_LM: 'CAUSAL_LM'>, 
  inference_mode=False, 
  r=8, 
  target_modules=['q_proj', 'v_proj'], 
  lora_alpha=32, 
  lora_dropout=0.1, 
  fan_in_fan_out=False, 
  bias='none', 
  modules_to_save=['embed_tokens', 'lm_head'], 
  init_lora_weights=True
)
```

## Running training

![1684115796058](/en/post/image/ajxai-model-card/1684115796058.png)

## Train metrics

![1684115897402](/en/post/image/ajxai-model-card/1684115897402.png)

## Running Evaluation

![1684115758469](/en/post/image/ajxai-model-card/1684115758469.png)
