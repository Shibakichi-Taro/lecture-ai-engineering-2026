# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：もっともらしい答えを返そうとするのではなく、用語集を検索して答えられるようになった。
```
uv run python -m app.agent "AIE2026の講座の宿題にある「3レベル」とは何ですか?"
[model] gemini-3.5-flash-lite
AIE2026における「3レベル」とは、宿題の難易度や取り組み方の構成を指しています。具体的には以下の3段階に分かれています。

1. **基礎**：講義内容の確認を行うレベル
2. **実装**：自分で実際にコードを書くレベル
3. **発展**：より深く調査したり試行したりするレベル

すべてのレベルに取り組む必要はなく、**どれか1つ**に取り組めばその回の宿題は完了となります。また、原則として宿題の採点は行われません。

第1回（関係する回：第1回）
```

- `--trace` で見えたツール呼び出し（ツール名と引数）：`search_glossary({'query': 'LoRA'})`と`search_glossary({'query': 'QLoRA'})`の2つのツール呼び出しがあった。
```
uv run python -m app.agent --trace "LoRA とQLoRAの違いは?"
[model] gemini-3.5-flash-lite
LoRAとQLoRAの違いは以下の通りです。

* **LoRA（Low-Rank Adaptation）**
  事前学習済みの重みを固定したまま、重みの更新分を2つの小さな行列（低ランク行列）の積で表し、その小さな行列だけを学習するPEFT（Parameter-Efficient Fine-Tuning）手法です。学習するパラメータを大幅に削減できます。

* **QLoRA（Quantized LoRA）**
  LoRAの手法をベースにしつつ、**ベースモデルの重みを4ビットなどに量子化**してメモリをさらに節約したうえで学習を行う手法です。これにより、より大きなモデルを比較的小さなGPUメモリでも効率よく扱えるようになります。

関係する回：第2回

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0008613'), details={'text_prompt_tokens': 1196}, input_text_tokens=1196, input_tokens=1196, output_tokens=201, requests=2, tool_calls=2)
```
- 1つの質問での LLM 呼び出し回数（`requests`）：`requests=2`から、2回のLLM呼び出しがあったことがわかる。
- 追加したツールと、docstring に書いた説明：`data/tools.py`に以下を追加した。
```
def make_quiz(lecture: int) -> str:
    """指定した回の用語から1つ選び、復習用の確認問題と答えを返す。

    「第2回の確認問題を出して」のように、復習用の問題を求められたときに使う。
    """
```

## 宿題

- 取り組んだレベル：基礎
- やったこと：`search_glossary`のdocstringに`中学生が理解できるように、例えを使って平易な言葉で説明する。`という一文を追加した。
- 結果：`uv run python -m app.agent --trace "LoRA とQLoRAの違いは?"`の結果を比較した。

追加前: 上記の演習の結果  
追加後: 以下の結果
```
[model] gemini-3.5-flash-lite
**LoRA（ロラ）**と**QLoRA（キューロラ）**は、どちらもAIモデル（LLMなど）を効率よくチューニングするための技術ですが、メモリの使いかたに大きな違いがあります。

### 1. LoRAとは？
AIモデルの頭脳全体をそのままにしておき、横に「メモ帳（小さな追加パラメータ）」をペタッと貼るようなイメージです。
AI全体を動かすのは大変なので、このメモ帳の部分だけを書き換えて（学習して）AIをカスタマイズします。学習する量がグッと減るため、少ないメモリや時間で効率よく学習できます。

### 2. QLoRAとは？
LoRAのやり方をさらに進化させ、もともとのAIモデルの頭脳（重み）を**4ビットという超軽量サイズにギュッと圧縮（量子化）**したうえで、LoRAの学習を行う手法です。
AIのベースを小さくスリムにしているため、本来なら大きなGPUが必要なほど巨大なAIモデルでも、家庭用のパソコンにあるような小さなGPUで無理なく学習させることができます。

---

**まとめると：**
* **LoRA**：AIのベースを固定し、追加の小さなパラメータだけを効率よく学習する方法。
* **QLoRA**：LoRAに「ベースモデルを4ビットに圧縮する技術」を組み合わせて、さらに省メモリで学習できるようにした方法。

関係する回：第2回

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0013086'), details={'text_prompt_tokens': 1412}, input_text_tokens=1412, input_tokens=1412, output_tokens=354, requests=2, tool_calls=2)
```

- 気づき・疑問：「中学生が理解できる」には若干難しいところも残っているように思うが、「例えを使って平易な言葉で」という指示には概ね従っていると言える。