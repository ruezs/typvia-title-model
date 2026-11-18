# Typvia Title Model (0.3B)

Ranks candidate titles for text snippets on-device.

This is a modified version of [taobao-mnn/MNN-Pocket-0.3B](https://huggingface.co/taobao-mnn/MNN-Pocket-0.3B). Typvia fine-tuned the model with LoRA, merged the adapter, quantized the weights to Q4_1 GGUF, and added a learned scoring head.

## Download

Download these six files from [Releases](https://github.com/ruezs/typvia-title-model/releases/tag/v1) and keep them in the same directory. Verify each file using the SHA-256 shown next to its asset.

- `typvia-title.gguf`
- `readout_head.json`
- `runtime_config.json`
- `tokenizer.json`
- `tokenizer_config.json`
- `chat_template.jinja`

## Inference

1. Run the app’s input privacy checks. Load `typvia-title.gguf` with causal attention and request unpooled, unnormalized token embeddings.

2. Use the bundled chat template to render the `system` prompt from `runtime_config.json` and a user JSON message with `正文` and `候选文本`. Set `add_generation_prompt=True`. Tokenize the prefix and each query separately with `add_special_tokens=False`, then concatenate them in candidate order, with the fallback query last.

   ```text
   "\n[标题判断] " + JSON-quoted title + "\n评分："
   "\n[回退判断] " + null_query from runtime_config.json + "\n评分："
   ```

3. Apply `readout_head.json` to the final token vector of each query. Add `null_bias` only to the fallback score. Return the top title if it passes the app’s title checks and beats the fallback score by more than the configured `margin`.

## License

Apache-2.0. The release ZIP includes the original `LICENSE.txt`.
