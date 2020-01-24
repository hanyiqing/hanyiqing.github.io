# Biopython 学习日记



## Get Start

### Working with sequences

- 序列对象 Seq

```python
from Bio.Seq import Seq
my_seq = Seq("AGTACACTGGT")
print(my_seq)
Out[1]: Seq('AGTACACTGGT')
my_seq.alphabet
Out[2]: Alphabet()
## 表示当前的seq对象序列（DNA，PRO）是常用的字母表
my_seq
Out[3]: Seq('AGTACACTGGT')
## 与字符串不同直接显示，会带Seq()
my_seq.complement()
Out[4]: Seq('TCATGTGACCA')
## 转录
my_seq.reverse_complement()
Out[5]: Seq('ACCAGTGTACT')
## 转录并倒序
```

- ==SeqRecord==： Seq对象的注释：标识符，名称，描述
-  `Bio.SeqIO` 这个模块与Seq 和 SeqRecord 一起工作，reading and writing sequence file formats



### A usage example

- 关于女士拖鞋兰花的的用例：我们将在 PubMed 中搜索关于兰花的论文，并在第9章中从 GenBank 中提取序列数据，在第10章中从某些兰花蛋白质中提取 Swiss-Prot 数据，并在第6.4.1节中与 ClustalW 兰花蛋白质多重序列比对工作





### Parsing sequence file formats

- 许多生物信息学工作的很大一部分涉及处理用于保存生物数据的许多类型的文件格式。 这些文件装载着有趣的生物数据，一个特殊的挑战是将这些文件解析为一种格式，以便您可以用某种编程语言来操作它们。 然而，解析这些文件的任务可能会受挫，因为格式可能会定期更改，而且格式可能包含一些微妙的细节，这些细节甚至会破坏设计最好的解析器
- 使用NCBI 下载女士拖鞋兰花的序列数据，选最上面的一个条目，下载genebank和FASTA格式的数据
-  [ls_orchid.fasta](https://raw.githubusercontent.com/biopython/biopython/master/Doc/examples/ls_orchid.fasta)and [ls_orchid.gbk](https://raw.githubusercontent.com/biopython/biopython/master/Doc/examples/ls_orchid.gbk)



### Simple  parsing example

```python
'''解析fasta'''
from Bio import SeqIO

for seq_record in SeqIO.parse("C:/Users/pc-0224/Desktop/Biopython_work/data/ls_orchid.fasta.txt","fasta"):
    print(seq_record.id)
    print(repr(seq_record.seq))
    print(len(seq_record))
#id   
Out[6]:gi|2765658|emb|Z78533.1|CIZ78533
#repr(seq_record.seq)
Seq('CGTAACAAGGTTTCCGTAGGTGAACCTGCGGAAGGATCATTGATGAGACCGTGG...CGC', SingleLetterAlphabet())
#len(seq_record)
740
##......
##94 repeat
##......
# FASTA format 没有指定字母表 ,所以默认使用相当通用的 SingleLetterAlphabet () 而不是一些特殊的DNA

'''解析genebank'''
for seq_record in SeqIO.parse("C:/Users/pc-0224/Desktop/Biopython_work/data/ls_orchid.gbk.txt","genbank"):
    print(seq_record.id)
    print(repr(seq_record.seq))
    print(len(seq_record))
# seq_record.id
Out[7]:Z78439.1
# repr(seq_record.seq)
Seq('CATTGTTGAGATCACATAATAATTGATCGAGTTAATCTGGAGGATCTGTTTACT...GCC', IUPACAmbiguousDNA())
# len(seq_record)
592
## Seqio 已经能够选择一个合理的字母表，IUPAC 模糊的 DNA

```



>  Biopython has a lot of parsers, and each has its own little special niches based on the sequence format it is parsing and all of that



### Connecting with biological databases

可以从以下数据库提取信息

- [Entrez](https://www.ncbi.nlm.nih.gov/Web/Search/entrezfs.html)
- [PubMed](https://www.ncbi.nlm.nih.gov/PubMed/)
- [ExPASy](https://www.expasy.org/)
- [SCOP](http://scop.mrc-lmb.cam.ac.uk/scop/)



## Sequence objects



#### Seq与普通character的区别

- 有其他的方法，如翻译，反向翻译
- 有一个重要的属性，字母表
  - 为了区分ATCG，是DNA，还是Alanines, Glycines, Cysteines and Threonines的蛋白质序列，等等
  - 这个对象描述组成序列字符串的单个字符的“含义” ，以及它们应该如何解释
  - 目前可用的字母表定义在==Bio.Alphabet==模块
  - 我们将在这里使用 IUPAC 字母来处理一些我们最喜欢的对象: DNA，RNA 和蛋白质
    - ==Bio.Alphabet.IUPAC==提供了蛋白质、 DNA 和 RNA 的基本定义，还提供了扩展和定制基本定义的能力
    - 对于蛋白质，有一个基本 IUPACProtein 类，但有一个额外的 ExtendedIUPACProtein 类提供额外的元素：
      - U” (or “Sec” for selenocysteine) and “O” (or “Pyl” for pyrrolysine)
      - 多指代的符号 B” (or “Asx” for asparagine or aspartic acid), “Z” (or “Glx” for glutamine or glutamic acid), “J” (or “Xle” for leucine isoleucine) and “X” (or “Xxx” for an unknown amino acid)
    - 对于DNA
      - IUPACUnambiguousDNA：提供基本字符
      - IUPACAmbiguousDNA：为每一种可能提供模糊指代
      - ExtendedIUPACDNA：提供每种碱基修饰的类型
    - 对于RNA
      - IUPACAmbiguousRNA
      - IUPACUnambiguousRNA

- create an ambiguous sequence with the default generic alphabet

  - ```python
    from Bio.Seq import Seq
    my_seq = Seq("AGTACACTGGT")
    my_seq
    out[1]:Seq('AGTACACTGGT')
    my_seq.alphabet
    out[2]:Alphabet()
    ## 当没有指代字母表属性的时候，默认为Alphabet()
    ```

  - ```python
    from Bio.Seq import Seq
    from Bio.Alphabet import IUPAC
    my_seq = Seq("AGTACACTGGT",IUPAC.ambiguous_dna)
    ```

    

