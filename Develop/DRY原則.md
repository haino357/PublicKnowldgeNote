# DRY原則（Don't Repeat Yourself）

## 概要

DRY原則は「**同じ知識やロジックをシステム内で繰り返してはならない**」というソフトウェア開発の原則。Andy Hunt と Dave Thomas が著書『達人プログラマー（The Pragmatic Programmer）』で提唱した。

> Every piece of knowledge must have a single, unambiguous, authoritative representation within a system.
>
> — *The Pragmatic Programmer*

## DRYが対象とするもの

DRY原則は **コードの重複だけでなく、知識（knowledge）の重複** を対象とする。

| 対象 | 例 |
|------|------|
| **コード** | 同じロジックが複数箇所にコピペされている |
| **データ定義** | 同じバリデーションルールがフロントとバックで別々に定義されている |
| **ドキュメント** | コードのコメントがコードと同じことを繰り返しているだけ |
| **設定** | 同じ値がconfig・環境変数・定数に散在している |

## よくある違反パターン

### 1. コピペコード

```python
# Bad: 同じ計算が2箇所に
def get_order_total(order):
    return sum(item.price * item.quantity for item in order.items) * 1.1

def get_invoice_total(invoice):
    return sum(item.price * item.quantity for item in invoice.items) * 1.1
```

```python
# Good: 共通ロジックを抽出
def calc_total_with_tax(items, tax_rate=1.1):
    return sum(item.price * item.quantity for item in items) * tax_rate
```

### 2. マジックナンバーの散在

```python
# Bad
if len(password) < 8:  # ここにも8
    ...
error_msg = "パスワードは8文字以上"  # ここにも8

# Good
MIN_PASSWORD_LENGTH = 8
if len(password) < MIN_PASSWORD_LENGTH:
    ...
error_msg = f"パスワードは{MIN_PASSWORD_LENGTH}文字以上"
```

### 3. コメントによる重複

```python
# Bad: コードを日本語で繰り返しているだけ
# ユーザーの年齢が18以上かチェックする
if user.age >= 18:

# Good: コメントは「なぜ」を説明する
# 法的要件により成人のみ利用可能
if user.age >= LEGAL_ADULT_AGE:
```

## DRYの誤用に注意

### 見た目が同じ ≠ 知識が同じ

たまたまコードが似ているだけで、**意味や変更理由が異なる**場合は無理に共通化しない。

```python
# 注文の割引計算と、従業員の給与計算がたまたま同じ式でも
# ビジネスルールとして独立しているなら別々に保つべき
def calc_order_discount(amount):
    return amount * 0.1

def calc_employee_bonus(salary):
    return salary * 0.1  # これをDRYにまとめるのは間違い
```

### 過度な共通化のリスク

- 不要な依存関係が生まれる
- 1箇所の変更が予期せぬ影響を及ぼす（ショットガンサージェリー）
- 抽象化が複雑になり読みにくくなる

## 関連原則

| 原則 | 関係 |
|------|------|
| **WET（Write Everything Twice）** | DRYの対義。「2回書くまでは重複を許容する」という実用的なアプローチ |
| **Rule of Three** | 3回同じパターンが出たら共通化を検討する |
| **YAGNI** | 「必要になるまで作らない」— 過度なDRY適用の抑制に役立つ |
| **単一責任の原則（SRP）** | 変更理由が同じものをまとめる、という観点でDRYと補完関係 |

## まとめ

- DRY原則の本質は「**知識の一元管理**」
- コードの見た目ではなく「**変更理由が同じかどうか**」で判断する
- 過度な適用は逆効果。**Rule of Three** や **YAGNI** とバランスを取る
