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

## 2.5 論理アドレスをリニアアドレスへ変換せよ

DS レジスタの値が 0x10 であるとき，論理アドレス ds:0x123 をリニアアドレスに変換してください。  
ただし GDT（global descriptor table）の内容は表 2.1 の通りとし，CPU は保護モード（protected mode）で動いているとします。  

- 論理アドレス <-（セグメンテーション）-> リニアアドレス <-（ページング）-> 物理アドレス
- 論理アドレス=ds:0x123 <-> リニアアドレス=DSレジスタが指すセグメントのベースアドレス+オフセット
- リニアアドレス=0x100000 + 0x123

## 2.6 リニアアドレスを物理アドレスへ変換せよ

リニアアドレス 0xffff800000003120 を物理アドレスに変換してください。  
ただし、CR3 レジスタの値は 0x252000，メモリの値は表 2.2 の通りとし，4 レベルページングが有効になっているものとします。  


- 論理アドレス <-（セグメンテーション）-> リニアアドレス <-（ページング）-> 物理アドレス
- ??

## 2.15 CPUID を実行するインラインアセンブラを書け

- CPUID命令は、CPU識別情報または機種依存機能の情報を取得するCPU命令

```c:test.c
#include <stdint.h>

struct CPUIDResult {
    uint32_t eax, ebx, ecx, edx;
};
struct CPUIDResult ReadCPUID(uint32_t eax, uint32_t ecx) {
    struct CPUIDResult r;
    __asm__("cpuid" : "=a"(r.eax), "=b"(r.ebx), "=c"(r.ecx), "=d"(r.edx)
                    : "0"(eax), "2"(ecx));
    return r;
}
```

```sh
gcc -c test.c
objdump -d -M intel -C test.o
```

```asm
0000000000000000 <ReadCPUID>:
   0:   55                      push   rbp
   1:   48 89 e5                mov    rbp,rsp
   4:   53                      push   rbx
   5:   89 7d e4                mov    DWORD PTR [rbp-0x1c],edi
   8:   89 75 e0                mov    DWORD PTR [rbp-0x20],esi
   b:   8b 45 e4                mov    eax,DWORD PTR [rbp-0x1c]
   e:   8b 55 e0                mov    edx,DWORD PTR [rbp-0x20]
  11:   89 d1                   mov    ecx,edx
  13:   0f a2                   cpuid
  15:   89 de                   mov    esi,ebx
  17:   89 45 e8                mov    DWORD PTR [rbp-0x18],eax
  1a:   89 75 ec                mov    DWORD PTR [rbp-0x14],esi
  1d:   89 4d f0                mov    DWORD PTR [rbp-0x10],ecx
  20:   89 55 f4                mov    DWORD PTR [rbp-0xc],edx
  23:   48 8b 45 e8             mov    rax,QWORD PTR [rbp-0x18]
  27:   48 8b 55 f0             mov    rdx,QWORD PTR [rbp-0x10]
  2b:   5b                      pop    rbx
  2c:   5d                      pop    rbp
  2d:   c3                      ret
```

