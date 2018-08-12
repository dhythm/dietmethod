# Vim
## ファイル名の一括変換
1. 現在のディレクトリ配下のファイルをバッファに展開
2. パイプで出力結果を渡し、(最終行に)発生した空白行を削除
3. 全ての行に対して、"mv ファイル名 ファイル名"の形で出力(後者を修正すれば一括変換可能)
4. Windowsの場合は、`:set fenc=cp932`も加えた方が良い
```
:put! =expand('*') | g/^$/d | %s/\(.*\)$/mv "\1" "\1"/ge | noh
:w !sh    # unix
:w !cmd   # windows
```
## 桁数を揃えた連番を作成  
```
:put! =range(1,100) | g/^$/d | [start],[end]s/^\d\{1}$/0\0/ge | [start],[end]s/^\d\{2}$/0\0/ge
```

## 現在の行を変数に格納して活用
```
:let ln=line('.') | execute ln.",$s/{src}/{dest}/"
:let ln=line('.') | put! =range(1,100) | g/^$/d | execute ln.",$s/^\\d\\{1}$/0\\0/ge" | execute ln.",$s/^\\d\\{2}$/0\\0/ge"
```

## 特定の文字列を含むファイルからディレクトリを作成
```
:put! =expand('\[*\]*') | g/^$/d | %s/\[\(.*\)\].*/mkdir "\1"/ge | sort u | w !sh
```

## 環境変数の変更
下記のコマンドは同じ挙動になる  
```
:set columns=80
:let &columns=80
```

応用して、既存の設定値の一部を変更
```
:let &guifont=substitute(&guifont,"h14","h12","g")
:let &columns=&columsn * 2
```

## 固定長インターフェースファイルの作成
大量のタブスペースを挿入した後、c=50以降の空白を全部削除する方法  
揃えたい項目の前のフィールドで一番長い内容のカーソル位置を知っておく必要がある(ちょっとダサい)  
```
:let c=50 | :normal 0f,l20i^I^[:call cursor('.',c)^Mdw 
```

下記のコマンドの方がきれいな方法  
```
:g/{pattern}/ normal 0f,ldw:let c=55-col('.')^M:execute "normal ".c."i "^M 
```

最終形  
```
:let max = 0 |g/"key"/ normal 0f,l:let c=col('.')^M:if c>max |:let max=c^M
:g/{pattern}/ normal 0f,l:let c=max-col('.')+1^M:execute "normal ".c."i "^M 
```


# JavaScript
## Objectのデバッグ
`console.dir()`を使用することで、Objectのプロパティを列挙可能