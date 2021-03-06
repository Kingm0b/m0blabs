---
layout: post
title: "O finado TrueCrypt"
---

> Ele morreu, mas continua vivo em nossos corações!

O **Truecrypt** é um software para proteção de arquivos de computador contra acesso não autorizado, isolando estes arquivos em contêiners virtuais criptografados por algoritmos de chave simétrica. Estes contêiners (também chamados de volumes) tanto podem ser encontrados no formato de arquivos binários como em partições de um disco. O Truecrypt possui interfaces GUI e CLI.

![Tela principal do Truecrypt.](https://raw.githubusercontent.com/m0blabs/m0blabs.github.io/master/images/2017-01-20/imagem12.png)

Independente da forma em que se encontra o volume, eles são estruturados de forma um pouco parecido com discos que trabalham com o esquema MBR de particionamento, em que os primeiros 512 bytes do volume armazenam informações primordiais para o acesso dos dados presentes no mesmo.

Em uma visão simplicada do funcionamento do Truecrypt, quando criamos um volume, primeiro definimos uma senha (esta senha será futuramente utilizada para acessar os arquivos dentro do volume criado).

![Definição da senha do volume em criação.](https://raw.githubusercontent.com/m0blabs/m0blabs.github.io/master/images/2017-01-20/imagem13.png)

Em seguida, será gerado aleatoriamente um *salt number* de 512 bits. Este salt number, junto com os bits da senha fornecida, será utilizado para gerar duas chaves criptográficas: o **Header Key** e o **Master Key**.

![Geração das chaves simétricas usadas na encriptação do cabeçalho e dados do volume.](https://raw.githubusercontent.com/m0blabs/m0blabs.github.io/master/images/2017-01-20/imagem14.png)

O *header key* é uma chave utilizada para criptografar os metadados do volume (o cabeçalho do volume, excetuando-se os primeiros 64 bytes que não são criptografados, pois se referem ao salt number). O *master key* é a chave usada para criptografar todo o restante do volume (local onde estará presente o sistema de arquivos).

O algoritmo de chave simétrica utilizado, para encriptar tanto o cabeçalho do volume (usando o header key) quanto a área de dados (utilizando o master key), é especificado pelo usuário no momento da criação do volume.

![Escolha de qual algoritmo de criptografia será usado para encriptar o volume.](https://raw.githubusercontent.com/m0blabs/m0blabs.github.io/master/images/2017-01-20/imagem15.png)

O processo de encriptação e decriptação dos dados em um volume ocorrem "*on-the-fly*" (de acordo com o próprio slogan do software), isto significa que cada operação de escrita em um volume desencadeia, de forma transparente para o usuário, o processo de encriptação. O mesmo ocorre com as operações de leitura, em que cada bloco do arquivo lido é automaticamente descriptografado.

Uma funcionalidade interessante do Truecrypt é o chamado "*cascateamento de cifras*", em que, por exemplo, caso o usuário tenha escolhido a cifra "AES-Twofish-Serpent" cada bloco dos dados transferidos para o volume primeiro serão encriptados pelo Serpent, seguido pelo Twofish e por último, pelo AES. Caso o dado seja descriptografado, primeiro será decriptado pelo AES, depois pelo Twofish e por último o Serpent.

Como já foi dito, o Truecrypt também possui uma interface CLI. Segue um exemplo da criação de um volume de 10MB:

```
$ truecrypt --text --create Volume10 --volume-type=Normal --keyfiles="" --password=123456 --random-source=/etc/passwd --encryption=AES --filesystem=FAT --hash=SHA-512 --size=10485760 --verbose

$ du -sh Volume10
10M Volume10
```

Observações:

* O nome do volume se chama Volume10
* A senha usada para acessar seu conteúdo é: 123456
* Os últimos 320 bytes do arquivo /etc/passwd foi usado como seed para a geração das chaves

Este volume poderá ser montado com:

```
$ truecrypt --text --password=123456 --keyfiles="" --protect-hidden="no" Volume10 montagem/
```

E desmontado com:

```
$ truecrypt --dismount Volume10
```

Em Maio de 2014 o Truecrypt teve seu desenvolvimento encerrado sob a alegação de não ser um software seguro possuindo falhas de segurança não corrigidas. Em sua página oficial foram disponibilizados tutoriais para substituição do Truecrypt por recursos nativos de encriptação de discos disponibilizado no Windows e OS X.

Com o fim do Truecrypt, forks já existentes ganharam força, como o VeraCrypt:

[https://veracrypt.codeplex.com](https://veracrypt.codeplex.com)

Em razão dos desenvolvedores não publicarem quais gravíssimas falhas eram estas, a ponto de fazerem desistirem do projeto, muitas especulações surgiram sobre o real motivo do fim do Truecrypt. Algumas pessoas se reuniram para auditar o código fonte e redistribuí-lo:

[https://truecrypt.ch](https://truecrypt.ch)

O pessoal do *Pure Privacy*, de tanto auditar e estudar o código fonte do TrueCrypt, quase um ano depois do seu fim, resolveram escrever seu próprio fork: o **CipherShed**. Link:

[https://www.ciphershed.org/](https://www.ciphershed.org/)



