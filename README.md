# salary-calc-tool
# 給与計算・勤怠突合ツール

打刻データ(CSV)、個人マスター(Excel)、シフト表(Excel)の3つの異なるデータソースを統合し、給与計算から勤怠の不整合検知までを自動化するWebアプリケーションです。

## 🚀 特徴
- **自動突合チェック**: 打刻とシフトの不一致（幽霊出勤/無断欠勤）を自動検出し、プレビュー上で警告。
- **複雑な計算ロジック**: 
  - 有給休暇と勤務時間の重複調整（所定労働時間を上限とする自動カット）。
  - 交通費の「実費 vs 月額上限」の自動比較。
  - 残業時間の分離（1.25倍割増）および30分単位の丸め処理。
- **高いデータ耐性**: Excel特有の時刻形式や欠損値（NaN/NaT）をクレンジングし、システムダウンを防ぐ堅牢な設計。
- **デバッグログ**: `logging`モジュールにより、計算プロセスの透明性を確保。

## 🛠 技術スタック
- **Backend**: Python 3.11, Flask
- **Data Analysis**: Pandas, Openpyxl
- **Frontend**: HTML5, Bootstrap 5

## 🏃 使い方
1. ライブラリのインストール
   ```bash
   pip install -r requirements.txt
   ```
2. アプリケーションの起動
   ```bash
   python app.py
   ```
3. ブラウザで `http://127.0.0.1:5000` にアクセスし、`data_sample/` 内のファイルをアップロードしてください。

## 💡 設計のこだわり
- **オブジェクト指向**: 計算エンジンを独立したクラスとして実装し、Webフレームワークに依存しない疎結合な設計にしました。
- **エラーハンドリング**: 実務での「汚れたデータ」を想定し、不正な入力があっても処理を継続しつつ、異常箇所をユーザーに通知するUI/UXを重視しました。

##ER図
erDiagram
    PERSONAL_MASTER ||--o{ ATTENDANCE_CSV : "1:N (社員番号)"
    PERSONAL_MASTER ||--o{ SHIFT_EXCEL : "1:N (社員番号)"

    PERSONAL_MASTER {
        string employee_id PK "社員番号"
        int hourly_wage "時給"
        time scheduled_start "始業時刻"
        time scheduled_end "終業時刻"
        int break_minutes "休憩時間(分)"
        int transport_daily "交通費日額"
        int transport_limit "交通費月額上限"
    }

    ATTENDANCE_CSV {
        string employee_id FK "社員番号"
        date work_date PK "日付"
        time clock_in "出勤時刻"
        time clock_out "退勤時刻"
    }

    SHIFT_EXCEL {
        string employee_id FK "社員番号"
        date work_date PK "日付"
        int lunch_unit_price "お弁当単価"
        int paid_leave_minutes "有給時間(分)"
    }

