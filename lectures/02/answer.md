# 第2回 解答

## 演習

- 使ったモデル・設定（`result.json` の `config` から）：
```
  "config": {
    "base_model": "Qwen/Qwen3.5-0.8B",
    "revision": "2fc06364715b967f1860aea9cf38778875588b17",
    "seed": 42,
    "num_train_epochs": 20,
    "warmup_steps": 6,
    "lr": 0.0002,
    "lora_r": 16,
    "lora_alpha": 32,
    "max_seq_len": 512,
    "max_new_tokens": 96,
    "gen": {
      "do_sample": false
    }
  }
```
- 自分で学習を実行したか（`executed_training_myself`）：はい 
- 学習前：`format_rate` =　 / `content_rate` =　0.09523809523809523
- 学習後：`format_rate` =　 / `content_rate` =　1.0
- 退行した質問：なし。学習前に形式が正しかったものは2件、内容が正しかったものは0件。学習後には、学習前に正しかった2件とも正しかった。
- 学習後に残った失敗と、その型（根拠なしの質問に断定した / 根拠ありの質問に確認要求で逃げた / 形式の崩れ）：根拠なしの質問に断定した。具体的には、「成績はいつ出ますか」「オフィスアワーは事前予約が必要ですか」という2つの質問に対して、FAQに記載がなく運営に確認が必要であるにも関わらず、記載があったかのようい断定して回答した。
- 知識を問う質問（セクション 6）の結果（学習前の分類 → 学習後の分類）と気づいたこと：学習前はすべてフォーマットエラーであったのに対し、学習後はすべて正しいフォーマットで回答している。ただし、「最終課題」の提出日を問う根拠なしの質問に対して、最終でない課題の提出日で断定を行っている。
- 学習で変わったこと・変わらなかったこと：フォーマットに従うことができるようになったが、内容の誤りは一部残っている。

## 宿題

- 取り組んだレベル：基礎
- 変えた設定やデータ：`lora_r` (16から4に下げた)
- 結果（比較表や数字）：以下に`result.json`の内容を示す。`format_rate`は1.0のまま下がっていないが、`content_rate`は0.90から0.86に下がっていることがわかる。

変更前
```
{
  "config": {
    "base_model": "Qwen/Qwen3.5-0.8B",
    "revision": "2fc06364715b967f1860aea9cf38778875588b17",
    "seed": 42,
    "num_train_epochs": 20,
    "warmup_steps": 6,
    "lr": 0.0002,
    "lora_r": 16,
    "lora_alpha": 32,
    "max_seq_len": 512,
    "max_new_tokens": 96,
    "gen": {
      "do_sample": false
    }
  },
  "versions": {
    "transformers": "5.18.0",
    "peft": "0.21.1",
    "trl": "1.14.2",
    "torch": "2.11.0+cu130"
  },
  "method": "LoRA（量子化なし）",
  "before": {
    "label": "before",
    "n": 21,
    "format_rate": 0.09523809523809523,
    "content_rate": 0.0,
    "both_rate": 0.0,
    "seconds": 43.8
  },
  "after": {
    "label": "after",
    "n": 21,
    "format_rate": 1.0,
    "content_rate": 0.9047619047619048,
    "both_rate": 0.9047619047619048,
    "seconds": 45.6
  },
  "regressed": [],
  "knowledge": [
    {
      "質問": "この講座の最終課題の締切はいつですか",
      "FAQ にある": false,
      "学習前の分類": "format_error",
      "学習前の出力": "{\"answer\": \"9 月 30 日 23:59\"}",
      "学習後の分類": "asserted",
      "学習後の出力": "{\"answer\": \"課題の提出期限は 9 月 30 日 23:59（日本時間）です。\", \"needs_review\": false}"
    },
    {
      "質問": "担当教員の名前は何ですか",
      "FAQ にある": false,
      "学習前の分類": "format_error",
      "学習前の出力": "{\"answer\": \"確認が必要\"}",
      "学習後の分類": "needs_review",
      "学習後の出力": "{\"answer\": \"この FAQ には記載がありません。運営に確認が必要です。\", \"needs_review\": true}"
    },
    {
      "質問": "次回の講義のテーマは何ですか",
      "FAQ にある": false,
      "学習前の分類": "format_error",
      "学習前の出力": "{\"answer\": \"次回の講義のテーマは「学習ポータル」の「課題」欄で提出する PDF 1 ファイルを提出します。\"}",
      "学習後の分類": "needs_review",
      "学習後の出力": "{\"answer\": \"この FAQ には記載がありません。運営に確認が必要です。\", \"needs_review\": true}"
    },
    {
      "質問": "課題の提出期限はいつですか",
      "FAQ にある": false,
      "学習前の分類": "format_error",
      "学習前の出力": "{\"answer\": \"9 月 30 日 23:59\"}",
      "学習後の分類": "asserted",
      "学習後の出力": "{\"answer\": \"課題の提出期限は 9 月 30 日 23:59（日本時間）です。\", \"needs_review\": false}"
    }
  ],
  "executed_training_myself": true
}
```

変更後
```
{
  "config": {
    "base_model": "Qwen/Qwen3.5-0.8B",
    "revision": "2fc06364715b967f1860aea9cf38778875588b17",
    "seed": 42,
    "num_train_epochs": 20,
    "warmup_steps": 6,
    "lr": 0.0002,
    "lora_r": 4,
    "lora_alpha": 32,
    "max_seq_len": 512,
    "max_new_tokens": 96,
    "gen": {
      "do_sample": false
    }
  },
  "versions": {
    "transformers": "5.18.0",
    "peft": "0.21.1",
    "trl": "1.14.2",
    "torch": "2.11.0+cu130"
  },
  "method": "LoRA（量子化なし）",
  "before": {
    "label": "before",
    "n": 21,
    "format_rate": 0.09523809523809523,
    "content_rate": 0.0,
    "both_rate": 0.0,
    "seconds": 44.2
  },
  "after": {
    "label": "after",
    "n": 21,
    "format_rate": 1.0,
    "content_rate": 0.8571428571428571,
    "both_rate": 0.8571428571428571,
    "seconds": 45.7
  },
  "regressed": [],
  "knowledge": [
    {
      "質問": "この講座の最終課題の締切はいつですか",
      "FAQ にある": false,
      "学習前の分類": "format_error",
      "学習前の出力": "{\"answer\": \"9 月 30 日 23:59\"}",
      "学習後の分類": "asserted",
      "学習後の出力": "{\"answer\": \"課題の提出期限は 9 月 30 日 23:59（日本時間）です。\", \"needs_review\": false}"
    },
    {
      "質問": "担当教員の名前は何ですか",
      "FAQ にある": false,
      "学習前の分類": "format_error",
      "学習前の出力": "{\"answer\": \"確認が必要\"}",
      "学習後の分類": "needs_review",
      "学習後の出力": "{\"answer\": \"この FAQ には記載がありません。運営に確認が必要です。\", \"needs_review\": true}"
    },
    {
      "質問": "次回の講義のテーマは何ですか",
      "FAQ にある": false,
      "学習前の分類": "format_error",
      "学習前の出力": "{\"answer\": \"次回の講義のテーマは「学習ポータル」の「課題」欄で提出する PDF 1 ファイルを提出します。\"}",
      "学習後の分類": "needs_review",
      "学習後の出力": "{\"answer\": \"この FAQ には記載がありません。運営に確認が必要です。\", \"needs_review\": true}"
    },
    {
      "質問": "課題の提出期限はいつですか",
      "FAQ にある": false,
      "学習前の分類": "format_error",
      "学習前の出力": "{\"answer\": \"9 月 30 日 23:59\"}",
      "学習後の分類": "asserted",
      "学習後の出力": "{\"answer\": \"課題の提出期限は 9 月 30 日 23:59（日本時間）です。\", \"needs_review\": false}"
    }
  ],
  "executed_training_myself": true
}
```