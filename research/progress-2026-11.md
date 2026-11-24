# Bomba Patch REA — progresso da investigação

**Estado em 2026-11-23.** Pesquisa independente sobre uma imagem local, sem distribuir arquivos do jogo.

## Identificação e integridade

- Imagem fonte local: `bomba_patch.iso`; SHA-256 `4c61e679033c4ddf3275b9032d224a1b4c2693d1fd2a8a213387b6efa11547e3`.
- `SYSTEM.CNF`: `SLPM_663.74`, versão `1.02`, NTSC. O serial e a versão coincidem com o lançamento japonês de *World Soccer Winning Eleven 10* no [Redump](http://redump.org/disc/17280). Isso identifica o jogo-base provável; não prova que a imagem modificada seja idêntica ao disco original nem revela a linhagem do patch.
- ELF local `SLPM_663.74`: SHA-256 `8ffbfdcbe30d0cda3de54a8fab9ea238bdbb44df10e5684d42b5c6a66b77ab6b`; ELF32 little-endian, flags de Emotion Engine (`5900`, `eabi64`, `mips3`), entry point `0x100008`.

## Arquivos e formatos

- `MODULE.AFS` (SHA-256 `558f2127486432cf03bc7d7bdbe01c6917497eca6d401e79fb711dc4f2151ac5`): tabela verificada de 12 entradas. As 12 são ELF32 MIPS-I, tipo IOP de PS2; os metadados `.iopmod` incluem nomes como `padman`, módulos de memory card, áudio, USB e CD/DVD. Não são o código de gameplay do Emotion Engine.
- `BOOT.AFS` (SHA-256 `fbef6b39eaf1ae0daed5958c8eee7d64aea87cc65faf4f2bd2b3ab85a04e72f5`): sete entradas; os streams das entradas 4–6 foram validados como zlib, com tamanhos descomprimidos coerentes. Os tipos exatos desses dados permanecem desconhecidos.
- O layout externo dos dois arquivos foi cruzado com o script comunitário [winning_eleven_10.bms](https://aluigi.altervista.org/bms/winning_eleven_10.bms), não uma especificação oficial. Na entrada 0 de `BOOT.AFS`, `MWo3` e `bootset.ovl` são correspondências observadas, mas não identificam o formato completo. Uma busca direcionada não encontrou confirmação específica para WE10.

## Executável e limite atual

- `readelf -SW` mostrou 45 cabeçalhos de seção, sem `.mdebug`, `.debug_*`, `.pdr` ou seções de relocação; `.symtab` e `.strtab` são vazias. `.comment` declara `MW MIPS C Compiler (2.4.1.01)` e `PlayStation2`.
- LLVM 23.1.3 reconhece `r5900`, mas sua decodificação local é parcial. O provedor REA disponível rejeitou a arquitetura. A extensão Ghidra Emotion Engine Reloaded v2.1.38 foi instalada no perfil do usuário; a análise headless reconheceu `r5900:LE:32:default` no executável.
- O entry chama `FUN_0011fe60`, cujo loop por frame chama `FUN_0014f260`, condicionalmente `FUN_001757a0`, e os dispatchers/callbacks `FUN_00120a60`, `FUN_00120d00` e `FUN_001200f0`.
- `FUN_0014f260` chama um alvo externo em `0x009b41b0` e altera o fluxo conforme o estado global `uGpffff8058`; isso ainda não comprova leitura do controle nem ligação com movimento. `FUN_001757a0` despacha com base em `iRam004f0a50`; seus handlers não estão classificados.
- **Movimentação, física da bola e animações continuam sem conclusão.** Esta é uma trilha estática inicial, não execução nem observação dinâmica.

## Próxima etapa

Seguir o dispatcher `FUN_00120d00` e a tabela em `0x003570c8`, além de mapear callbacks/estruturas usados pelos handlers. Confirmar referências a entidades e fluxo de valores antes de atribuir nomes de gameplay. A ferramenta está instalada; não é necessário baixar outra.

## Limites de publicação

A ISO, o executável e os arquivos extraídos permanecem locais, sob `work/` ignorado pelo Git. Este relatório contém apenas hashes, metadados estruturais, conclusões limitadas e referências; nenhum binário, asset, pixel, áudio ou trecho substancial de código proprietário foi incluído. O executável não foi iniciado e a ISO não foi modificada.
