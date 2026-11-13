# Retrieval Builder Schema
### 型別

| 自訂 Schema Type | ES Mapping Type | 使用情境 | searchable | filterable |
| :--- | :--- | :--- | :---: | :---: |
| string | 依勾選決定（見下表） | ID、分類、狀態、描述、文件內容 | ✓ | ✓ |
| string_array | 依勾選決定（同 string） | 多個 Tag、多個分類 | ✗ | ✓ |
| integer | long 或 integer | 數量、年齡 | - | ✓ |
| float | double | 分數、價格、權重 | - | ✓ |
| boolean | boolean | True / False | - | ✓ |
| date | date | 日期、時間 | - | ✓ |
  
- Searchable: T/F 決定該欄位能不能被搜尋
- Filterable: T/F 決定該欄位能不能被篩選
### 範例 
```
{
  "title": "Introduction to Machine Learning",
  "category": "AI",
  "author": "Alice Chen",
  "content": "Machine learning is a branch of artificial intelligence...",
  "price": 500,
  "published_at": "2026-01-01"
} 
```
- Searchable：使用者想找到什麼內容？
``` machine learning ```
希望找到：
```
Title 包含 machine learning 的文件。
Content 包含 machine learning 的文件。
Author 名稱包含 machine learning 的文件（如果有這種需求）。
```
這是全文搜尋（Full-text Search），通常使用 match Query。

- Filterable：使用者想限定哪些條件？
'''
category = AI
price >= 300
published_at >= 2026-01-01
'''
這是條件篩選（Structured Filtering），通常使用：
```
- term
- terms
- range
```
### meta schema -> ES setting 轉換規則
| meta 型別 | searchable | filterable | ES mapping | 說明 |
| :--- | :---: | :---: | :--- | :--- |
| string | ✓ | ✗ | `text` | 全文檢索，例如 title、content |
| string | ✗ | ✓ | `keyword` | 精確比對、聚合、排序，例如 department |
| string | ✓ | ✓ | `text` + 子欄位 `.keyword` | 兩種都要，例如品牌名稱 |
| string | ✗ | ✗ | `keyword` + `index: false` | 只存在 `_source`，例如 url |
| integer / float | — | ✓ | `long` / `double` | 範圍篩選、排序 |
| integer / float | — | ✗ | 同上 + `index: false, doc_values: false` | 只存不查 |
| datetime | — | ✓ | `date` | 範圍篩選、排序 |
| boolean | — | ✓ | `boolean` | 精確比對 |


