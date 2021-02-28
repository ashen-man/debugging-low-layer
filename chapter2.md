## 2.1 ELF 形式のファイルを逆アセンブルせよ

- ELF形式はオブジェクトファイルや実行ファイルのファイルフォーマットの一つ。  
- https://refspecs.linuxfoundation.org/elf/elf.pdf

```sh
objdump -d -M intel -C elf.o
```


## 2.2 独自形式のバイナリファイルを逆アセンブルせよ

- objdumpはhrb形式の構造が分からない
- Dオプションで機械語のセクションに限らず全セクションを逆アセンブルする

```
objdump -D -b binary -m i386 --start-address=0x24 bootpack.hrb
```

- objdump -D は32ビットとして逆アセンブルする
- -M x86-64を指定して64ビットモードのバイナリを逆アセンブルする

```
objdump -D -b binary -m i386 -M x86-64 asmfunc.bin
```
