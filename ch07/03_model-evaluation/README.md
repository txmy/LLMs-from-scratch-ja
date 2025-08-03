# 第7章：指示に従うためのファインチューニング

このフォルダには、モデル評価に使用できるユーティリティコードが含まれています。



&nbsp;
## OpenAI APIを使用した指示応答の評価


- [llm-instruction-eval-openai.ipynb](llm-instruction-eval-openai.ipynb)ノートブックは、OpenAIのGPT-4を使用して指示ファインチューニングモデルによって生成された応答を評価します。以下の形式のJSONファイルで動作します：

```python
{
    "instruction": "What is the atomic number of helium?",
    "input": "",
    "output": "The atomic number of helium is 2.",               # <-- テストセットで提供される目標
    "model 1 response": "\nThe atomic number of helium is 2.0.", # <-- LLMによる応答
    "model 2 response": "\nThe atomic number of helium is 3."    # <-- 2番目のLLMによる応答
},
```

&nbsp;
## Ollamaを使用したローカルでの指示応答評価

- [llm-instruction-eval-ollama.ipynb](llm-instruction-eval-ollama.ipynb)ノートブックは、Ollama経由でローカルにダウンロードしたLlama 3モデルを利用して、上記の代替手段を提供します。