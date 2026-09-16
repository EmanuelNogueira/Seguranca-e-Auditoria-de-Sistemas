# Desafio de criptografia RSA

Arquivo para entrega: [frase.enc](frase.enc).

O arquivo `frase.txt` foi criptografado com a chave pública RSA de 3072 bits do professor Igor Luiz Oliveira de Souza. O texto usado é o exemplo da atividade de hash, já presente neste repositório.

- Chave original: [igor-ifba.pub](igor-ifba.pub), em formato OpenSSH.
- Mesma chave convertida para PEM: [igor-ifba.pem](igor-ifba.pem).
- Origem: https://github.com/igorlosouza/auditoria-seguranca-sistemas-2026/blob/main/igor-ifba.pub
- Impressão digital SSH: `SHA256:RgjT2pmdYXuHx7XY1LG4gvxhLe5iWkA2IJ5qyRFsg1U`.
- Criptografia: RSA-OAEP, com SHA-256 tanto no OAEP quanto no MGF1, sem rótulo adicional.
- Saída: arquivo binário de 384 bytes; não é um arquivo de texto nem Base64.

## Como foi gerado no PowerShell

Com o terminal aberto na pasta do projeto:

```powershell
& 'C:\Windows\System32\OpenSSH\ssh-keygen.exe' -e -m PKCS8 -f .\igor-ifba.pub | Set-Content .\igor-ifba.pem -Encoding ASCII
& 'C:\Program Files\OpenSSL-Win64\bin\openssl.exe' pkeyutl -encrypt -pubin -inkey .\igor-ifba.pem -in .\frase.txt -out .\frase.enc -pkeyopt rsa_padding_mode:oaep -pkeyopt rsa_oaep_md:sha256 -pkeyopt rsa_mgf1_md:sha256
```

Este comando usa RSA diretamente e aceita até 318 bytes com esta chave e estes parâmetros. Para arquivos maiores, é necessário outro procedimento, como criptografia híbrida.

## Descriptografia pelo professor

Baixar o arquivo `frase.enc` pelo botão de download do GitHub. Com a chave privada correspondente em formato PEM, executar:

```sh
openssl pkeyutl -decrypt -inkey chave_privada_do_professor.pem -in frase.enc -out frase_recuperada.txt -pkeyopt rsa_padding_mode:oaep -pkeyopt rsa_oaep_md:sha256 -pkeyopt rsa_mgf1_md:sha256
```

`chave_privada_do_professor.pem` é um nome ilustrativo: deve ser substituído pelo caminho da chave privada do professor. Se ela estiver em formato OpenSSH, o professor precisa converter uma cópia para PEM antes de usar o OpenSSL. A chave privada permanece com ele.

A chave pública foi validada pelo OpenSSL e a conversão foi conferida contra a chave OpenSSH original. A descriptografia deste arquivo depende da chave privada do professor.

Referência dos parâmetros: [documentação do OpenSSL](https://docs.openssl.org/3.5/man1/openssl-pkeyutl/).
