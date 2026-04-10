# SOLID原則

オブジェクト指向設計における5つの基本原則。Robert C. Martin（Uncle Bob）が提唱し、Michael Feathers が頭文字をとって SOLID と命名した。

## 一覧

| 略称 | 原則名 | 一言で |
|------|--------|--------|
| **S** | 単一責任の原則（Single Responsibility Principle） | クラスの変更理由は1つだけ |
| **O** | 開放閉鎖の原則（Open/Closed Principle） | 拡張に開き、修正に閉じる |
| **L** | リスコフの置換原則（Liskov Substitution Principle） | 子クラスは親クラスと置換可能 |
| **I** | インターフェース分離の原則（Interface Segregation Principle） | 使わないメソッドに依存させない |
| **D** | 依存性逆転の原則（Dependency Inversion Principle） | 具象ではなく抽象に依存する |

---

## S — 単一責任の原則（SRP）

> A class should have only one reason to change.

クラスが担う責務は1つだけにする。変更理由が複数あるクラスは分割を検討する。

```python
# Bad: レポートの内容生成と出力形式が同じクラスに
class Report:
    def calculate_stats(self, data):
        ...
    def generate_pdf(self, stats):
        ...
    def send_email(self, pdf):
        ...

# Good: 責務ごとに分離
class StatsCalculator:
    def calculate(self, data): ...

class PdfExporter:
    def export(self, stats): ...

class EmailSender:
    def send(self, attachment): ...
```

**判断基準**: 「このクラスを変更する理由は何か？」が複数あるなら分割のサイン。

---

## O — 開放閉鎖の原則（OCP）

> Software entities should be open for extension, but closed for modification.

既存のコードを変更せずに、新しい振る舞いを追加できる設計にする。

```python
# Bad: 新しい割引タイプの追加でif文を修正する必要がある
class DiscountCalculator:
    def calculate(self, order, discount_type):
        if discount_type == "percentage":
            return order.total * 0.1
        elif discount_type == "fixed":
            return 500
        # 新タイプ追加のたびにここを修正...

# Good: 新しい割引はクラスを追加するだけ
class DiscountStrategy:
    def calculate(self, order): ...

class PercentageDiscount(DiscountStrategy):
    def calculate(self, order):
        return order.total * 0.1

class FixedDiscount(DiscountStrategy):
    def calculate(self, order):
        return 500
```

**実現手段**: ストラテジーパターン、ポリモーフィズム、プラグイン機構など。

---

## L — リスコフの置換原則（LSP）

> Subtypes must be substitutable for their base types.

親クラスを使っている箇所で、子クラスに置き換えても正しく動作しなければならない。

```python
# Bad: 子クラスが親の契約を破っている
class Bird:
    def fly(self):
        return "飛んでいます"

class Penguin(Bird):
    def fly(self):
        raise NotImplementedError("ペンギンは飛べません")  # LSP違反

# Good: 飛べることを前提にしない設計
class Bird:
    def move(self): ...

class Sparrow(Bird):
    def move(self):
        return "飛んでいます"

class Penguin(Bird):
    def move(self):
        return "泳いでいます"
```

**チェックポイント**: 子クラスが親クラスのメソッドで例外を投げたり、事前条件を強化していないか？

---

## I — インターフェース分離の原則（ISP）

> Clients should not be forced to depend on interfaces they do not use.

大きなインターフェースを小さな単位に分割し、クライアントが必要なものだけに依存するようにする。

```python
# Bad: 全機能を1つのインターフェースに
class Worker:
    def work(self): ...
    def eat(self): ...
    def sleep(self): ...

class Robot(Worker):
    def work(self): return "作業中"
    def eat(self): pass   # ロボットは食べない → 不要な依存
    def sleep(self): pass  # ロボットは寝ない → 不要な依存

# Good: 役割ごとにインターフェースを分離
class Workable:
    def work(self): ...

class Eatable:
    def eat(self): ...

class HumanWorker(Workable, Eatable):
    def work(self): return "作業中"
    def eat(self): return "食事中"

class Robot(Workable):
    def work(self): return "作業中"
```

**目安**: 実装クラスに `pass` や `NotImplementedError` のメソッドがあれば、インターフェースが大きすぎるサイン。

---

## D — 依存性逆転の原則（DIP）

> High-level modules should not depend on low-level modules. Both should depend on abstractions.

上位モジュールが下位モジュールの具体的な実装に直接依存しないようにする。

```python
# Bad: 上位モジュールが具象クラスに直接依存
class OrderService:
    def __init__(self):
        self.db = MySQLDatabase()  # 具象に依存

    def save_order(self, order):
        self.db.insert(order)

# Good: 抽象（インターフェース）を介して依存
class Database:
    def insert(self, data): ...

class MySQLDatabase(Database):
    def insert(self, data): ...

class PostgreSQLDatabase(Database):
    def insert(self, data): ...

class OrderService:
    def __init__(self, db: Database):  # 抽象に依存
        self.db = db

    def save_order(self, order):
        self.db.insert(order)
```

**効果**: テスト時にモックへ差し替え可能、DB移行時にOrderServiceの変更が不要。

---

## SOLID原則の関係性

```
SRP: クラスを小さく保つ
 └→ OCP: 小さいクラスは拡張しやすい
     └→ LSP: 正しい継承で安全に拡張
         └→ ISP: インターフェースも小さく保つ
             └→ DIP: 抽象に依存して疎結合に
```

5つの原則は独立ではなく、互いに補完し合う。SRPで責務を分離すると、OCPに従った拡張が自然とやりやすくなる。

## 適用時の注意点

- **過度な適用は避ける** — 小規模なスクリプトやプロトタイプに厳密に適用するとオーバーエンジニアリングになる
- **段階的に適用する** — 最初からすべてを満たす必要はない。コードが成長し複雑さが増した段階でリファクタリングする
- **DRY / YAGNI とバランスを取る** — 抽象化のしすぎは理解しにくいコードを生む
