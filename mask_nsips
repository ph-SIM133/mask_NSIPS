# Copyright (c) 2026 ph-SIM133
# All rights reserved.
# This software is for non-commercial use only.

import csv
import os
import glob
import random
import string
import sys

# ==============================================================================
# 1. フォルダ・ファイル設定
# ==============================================================================
BASE_DIR = os.path.dirname(os.path.abspath(sys.argv[0]))

INPUT_FOLDER = os.path.join(BASE_DIR, "A_Input")
OUTPUT_FOLDER = os.path.join(BASE_DIR, "B_Output")
TARGET_EXTENSION = "*.txt"

# ==============================================================================
# 変換用補助関数
# ==============================================================================

def mask_kanji(text, length=None):
    if not text: return text
    kanji_list = "東西南北春夏秋冬山川海空日月星草木花男女父母子兄弟姉妹一二三四五六七八九十"
    L = length if length else len(text)
    return "".join(random.choice(kanji_list) for _ in range(L))

def mask_kana(text, length=None):
    if not text: return text
    kana_list = "ｱｲｳｴｵｶｷｸｹｺｻｼｽｾｿﾀﾁﾂﾃﾄﾅﾆﾇﾈﾉﾊﾋﾌﾍﾎﾏﾐﾑﾒﾓﾔﾕﾖﾗﾘﾙﾚﾛﾜｦﾝ"
    L = length if length else len(text)
    return "".join(random.choice(kana_list) for _ in range(L))

def mask_digits(text):
    if not text: return text
    res = ""
    for c in text:
        # 数字、または万が一＠が混じっていてもランダムな数字に置き換える
        if c.isdigit() or c == '＠' or c == '@':
            res += random.choice(string.digits)
        else:
            res += c
    return res

def mask_date(text):
    if not text or not text.strip(): return text
    # 生年月日（YYYYMMDD形式）をランダム化
    return f"{random.randint(1960, 2023):04d}{random.randint(1, 12):02d}{random.randint(1, 28):02d}"

# ==============================================================================
# メイン処理 (実データの項目位置に準拠)
# ==============================================================================

def mask_nsips_file(input_file, output_file):
    processed_rows = []
    try:
        # NSIPS標準の Shift-JIS (cp932) で読み込み
        with open(input_file, 'r', encoding='cp932', newline='') as f:
            reader = csv.reader(f)
            for row in reader:
                if not row: continue
                rtype = row[0]

                # --- VER. ヘッダー (薬局情報) ---
                if rtype.startswith("VER"):
                    if len(row) > 6: row[6] = mask_digits(row[6])   # 薬局コード
                    if len(row) > 7: row[7] = mask_kanji(row[7])    # 薬局名称
                    if len(row) > 8: row[8] = mask_digits(row[8])   # 郵便番号
                    if len(row) > 9: row[9] = mask_kanji(row[9])    # 住所
                    if len(row) > 10: row[10] = mask_digits(row[10]) # 電話番号

                # --- 1. 患者情報 ---
                elif rtype == "1":
                    if len(row) > 1: row[1] = mask_digits(row[1])   # 患者ID
                    if len(row) > 2: row[2] = mask_kana(row[2])     # 氏名カナ
                    if len(row) > 3: row[3] = mask_kanji(row[3])    # 氏名漢字
                    if len(row) > 5: row[5] = mask_date(row[5])     # 生年月日
                    if len(row) > 6: row[6] = mask_digits(row[6])   # 郵便番号
                    if len(row) > 7: row[7] = mask_kanji(row[7])    # 住所
                    if len(row) > 8: row[8] = mask_digits(row[8])   # 電話番号

                # --- 2. 医療機関・医師・薬剤師情報 ---
                elif rtype == "2":
                    if len(row) > 1: row[1] = mask_digits(row[1])    # 処方箋番号
                    if len(row) > 12: row[12] = mask_digits(row[12]) # 医療機関コード
                    if len(row) > 14: row[14] = mask_kanji(row[14])  # 医療機関名
                    if len(row) > 15: row[15] = mask_digits(row[15]) # 郵便番号
                    if len(row) > 16: row[16] = mask_kanji(row[16])  # 住所
                    if len(row) > 17: row[17] = mask_digits(row[17]) # 電話番号
                    if len(row) > 23: row[23] = mask_kana(row[23])   # 医師名カナ
                    if len(row) > 24: row[24] = mask_kanji(row[24])  # 医師名漢字
                    if len(row) > 25: row[25] = mask_digits(row[25]) # 薬剤師コード等
                    if len(row) > 26: row[26] = mask_kanji(row[26])  # 薬剤師氏名

                # 3(用法), 4(薬品), 5-7(点数) はそのまま保持
                processed_rows.append(row)

        # Shift-JIS で書き出し
        with open(output_file, 'w', encoding='cp932', newline='') as f:
            writer = csv.writer(f)
            writer.writerows(processed_rows)
        print(f"匿名化完了: {os.path.basename(input_file)}")
    except Exception as e:
        print(f"エラー: {e}")

def process_folders():
    if not os.path.exists(INPUT_FOLDER): os.makedirs(INPUT_FOLDER)
    if not os.path.exists(OUTPUT_FOLDER): os.makedirs(OUTPUT_FOLDER)

    files = glob.glob(os.path.join(INPUT_FOLDER, TARGET_EXTENSION))
    for file_path in files:
        output_path = os.path.join(OUTPUT_FOLDER, os.path.basename(file_path))
        mask_nsips_file(file_path, output_path)

if __name__ == "__main__":
    process_folders()
