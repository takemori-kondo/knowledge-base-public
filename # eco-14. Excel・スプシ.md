# eco-14. Excel・スプシ
________________________________________
## 1. 典型的な条件式の例

- =LEFT($A1,3)="///"
- =LEFT($A1,6)="//TODO"
- =LEFT($A1, 2) = "//"
- =LEFT(A1,1)="*"
- =OR(A1="Param",A1="Jump",A1="Selection",A1="SelectionTitle")
- =RIGHT(A1,5)="Macro"
- =A1="SendMessage"

________________________________________
## 2. 機能

フォルダパスの確認方法

- メニュー上部フォルダ移動アイコン > 現在の場所をクリック

指定の基本

- A:Z：A～Zまで全行
- 1:1：1行目全て

VLOOKUP(照合箇所, 参照表, 列番号,  近似照合か)

- 照合ナシなら#N/A

IFERROR(a, エラー時の値)

ARRAYFORMULA(配列)

=QUERY(
  {
    ARRAYFORMULA(TO_TEXT('MP1'!A:H)),
    ARRAYFORMULA(
        IFERROR(VLOOKUP('MP1'!I:I, MP1_text!A:F, 5, FALSE), 'MP1'!I:I)
    ),
    ARRAYFORMULA(TO_TEXT('MP1'!J:M))
  },
  "select * where Col1 is not null or Col9 is not null",
  1
) 

=ARRAYFORMULA(
  IF(
    COLUMN('MP1'!A:Z) = MATCH("Text", 'MP1'!1:1, 0),
    IFERROR(
      TO_TEXT(VLOOKUP('MP1'!A:Z, MP1_text!A:F, 5, FALSE)),
      TO_TEXT('MP1'!A:Z)
    ),
    'MP1'!A:Z
  )
)